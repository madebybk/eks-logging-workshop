# CloudWatch Logs로 로그 전송

## Fluent Bit 배포 <a href="#container-insights-setup-logs-fluentbit" id="container-insights-setup-logs-fluentbit"></a>

1\. `amazon-cloudwatch` 라는 namespace를 생성합니다.

```
kubectl apply -f https://raw.githubusercontent.com/aws-samples/amazon-cloudwatch-container-insights/latest/k8s-deployment-manifest-templates/deployment-mode/daemonset/container-insights-monitoring/cloudwatch-namespace.yaml

```

2\. 로그를 전송할 클러스터 이름 및 리전이 포함된 `cluster-info`라는 **ConfigMap**을 생성합니다.

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

3\. 이전에 [Git으로](https://github.com/madebybk/eksapp) Clone 한 Yaml 파일을 Fluent Bit DaemonSet으로 클러스터에 배포합니다.

```
kubectl apply -f ~/environment/eksapp/fluent-bit/fluent-bit-config.yaml

```

4\. 배포를 검증합니다. 각 노드에는 **fluent-bit-\***라는 Pod가 하나씩 있어야 합니다.

```
kubectl get pods -n amazon-cloudwatch

```

## CloudWatch Logs로 로그 데이터 확인

1 . 이전에 배포한 Random Log Generator에 접속해 로그를 초당 **3 record**씩 **10분**동안 전송합니다.

<img src="../.gitbook/assets/file.drawing (48).svg" alt="" class="gitbook-drawing">

2\. AWS Console에서 CloudWatch Logs를 확인합니다. 랜덤 JSON 형태의 로그가 초당 3개씩, 10분동안 전송되는것을 확인할 수 있습니다.

<img src="../.gitbook/assets/file.drawing (102).svg" alt="" class="gitbook-drawing">
