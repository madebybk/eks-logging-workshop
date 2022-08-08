# Kinesis Data Stream

## Kinesis Data Stream 생성

1\. AWS Console에서 [Kinesis 창](https://console.aws.amazon.com/kinesis)으로 이동하고 Create Data Stream 버튼을 누릅니다.

<img src="../.gitbook/assets/file.drawing (22).svg" alt="" class="gitbook-drawing">

2\. Data Stream 이름, Shard 수 입력 후 생성합니다.

온디맨드 용량 모드를 사용하면 데이터 스트림에서 쓰고 읽는 데이터의 GB당 비용을 지불하게 되고 프로비저닝된 용량 모드에서는 쓰기 및 읽기 요청 속도를 기반으로 애플리케이션에 필요한 샤드 수를 지정합니다. 샤드는 1MB/초의 쓰기와 2MB/초의 읽기를 제공하는 용량 단위입니다.

<img src="../.gitbook/assets/file.drawing (112).svg" alt="" class="gitbook-drawing">

## Fluent Bit Configuration

3\. Fluent Bit이 Kinesis로 데이터를 보낼 수 있게 IAM role을 생성합니다.

```
mkdir ~/environment/logging/

cat <<EoF > ~/environment/logging/fluent-bit-policy.json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "kinesis:PutRecords"
            ],
            "Resource": "*"
        }
    ]
}
EoF

aws iam create-policy   \
  --policy-name fluent-bit-policy \
  --policy-document file://~/environment/logging/fluent-bit-policy.json

```

4\. Role을 확인하기 위해 아래 과정을 수행합니다.

```
STACK_NAME=$(eksctl get nodegroup --cluster eksapp -o json | jq -r '.[].StackName')
echo $STACK_NAME

```

5\. 워커노드의 public, private 노드 이름을 저장합니다.

```
MGMD_PUB_ROLE_NAME=$(aws cloudformation describe-stack-resources --stack-name eksctl-eksapp-nodegroup-managed-ng-public-01 | jq -r '.StackResources[] | select(.ResourceType=="AWS::IAM::Role") | .PhysicalResourceId')
echo "export MGMD_PUB_ROLE_NAME=${MGMD_PUB_ROLE_NAME}" | tee -a ~/.bash_profile
test -n "$MGMD_PUB_ROLE_NAME" && echo MGMD_PUB_ROLE_NAME is "$MGMD_PUB_ROLE_NAME" || echo MGMD_PUB_ROLE_NAME is not set
MGMD_PRI_ROLE_NAME=$(aws cloudformation describe-stack-resources --stack-name eksctl-eksapp-nodegroup-managed-ng-private-01 | jq -r '.StackResources[] | select(.ResourceType=="AWS::IAM::Role") | .PhysicalResourceId')
echo "export MGMD_PRO_ROLE_NAME=${MGMD_PRI_ROLE_NAME}" | tee -a ~/.bash_profile
test -n "$MGMD_PRI_ROLE_NAME" && echo PRI_ROLE_NAME is "$MGMD_PRI_ROLE_NAME" || echo MGMD_PRI_ROLE_NAME is not set

```

6\. 커노드의 IAM Role에 연결합니다.

```
aws iam attach-role-policy \
  --role-name $MGMD_PUB_ROLE_NAME \
  --policy-arn arn:aws:iam::${ACCOUNT_ID}:policy/fluent-bit-policy
aws iam attach-role-policy \
  --role-name $MGMD_PRI_ROLE_NAME \
  --policy-arn arn:aws:iam::${ACCOUNT_ID}:policy/fluent-bit-policy
  
```

7\. IAM Role이 정상적으로 배정됐는지 확인합니다.

```
aws iam list-attached-role-policies --role-name $MGMD_PUB_ROLE_NAME | grep fluent-bit-policy || echo 'Policy not found'
aws iam list-attached-role-policies --role-name $MGMD_PRI_ROLE_NAME | grep fluent-bit-policy || echo 'Policy not found'

```

8\. CloudWatch의 `fluent-bit-config.yaml` 파일을 수정합니다.

Fluent Bit은 여러 개의 output을 설정할 수 있으니 **line 133** 에 주석으로 되어있는 kinesis output 코드를 추가하고 저장합니다.

```
[OUTPUT]
        Name                kinesis_streams
        Match               application.*
        region              ${AWS_REGION}
        stream              kds-eksapp-logging
```

9\. 재배포를 위해 amazon-cloudwatch namespace는 임시 삭제합니다.

```
kubectl delete namespace amazon-cloudwatch

```

10\. `amazon-cloudwatch` namespace를 생성합니다.

```
kubectl apply -f https://raw.githubusercontent.com/aws-samples/amazon-cloudwatch-container-insights/latest/k8s-deployment-manifest-templates/deployment-mode/daemonset/container-insights-monitoring/cloudwatch-namespace.yaml

```

11\. 로그를 전송할 클러스터 이름 및 리전이 포함된 `cluster-info`라는 **ConfigMap**을 생성합니다.

```
ClusterName=${ekscluster_name}
RegionName=${AWS_REGION}
FluentBitHttpPort='2020'
FluentBitReadFromHead='Off'
[[ ${FluentBitReadFromHead} = 'On' ]] && FluentBitReadFromTail='Off'|| FluentBitReadFromTail='On'
[[ -z ${FluentBitHttpPort} ]] && FluentBitHttpServer='Off' || FluentBitHttpServer='On'
kubectl create configmap fluent-bit-cluster-info \
--from-literal=cluster.name=${ClusterName} \
--from-literal=http.server=${FluentBitHttpServer} \
--from-literal=http.port=${FluentBitHttpPort} \
--from-literal=read.head=${FluentBitReadFromHead} \
--from-literal=read.tail=${FluentBitReadFromTail} \
--from-literal=logs.region=${RegionName} -n amazon-cloudwatch

```

12\. 수정된 fluent-bit-config.yaml 파일로 Fluent Bit DaemonSet을 배포합니다.

```
kubectl apply -f ~/environment/eksapp/fluent-bit/fluent-bit-config.yaml

```

13\. 배포를 검증합니다. 각 노드에는 **fluent-bit-\***라는 Pod가 하나씩 있어야 합니다.

```
kubectl get pods -n amazon-cloudwatch

```

13\. 이번엔 Random Log Generator에서 JSON 데이터를 초당 **5 record**씩 **10분**동안 전송합니다.

<img src="../.gitbook/assets/file.drawing (95).svg" alt="" class="gitbook-drawing">

14\. AWS Console에서 Kinesis를 확인해 봅니다.

<img src="../.gitbook/assets/file.drawing (71).svg" alt="" class="gitbook-drawing">
