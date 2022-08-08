# Shell 환경변수 저장

## Cloud9에 환경변수 저장&#x20;

1\. Account ID, Region 정보 등을 환경 변수와 프로파일에 저장합니다.

```
export ACCOUNT_ID=$(aws sts get-caller-identity --region ap-northeast-2 --output text --query Account)
export AWS_REGION=$(curl -s 169.254.169.254/latest/dynamic/instance-identity/document | jq -r '.region')
echo $ACCOUNT_ID
echo $AWS_REGION

```

2\. bash\_profile에 저장합니다.

```
echo "export ACCOUNT_ID=${ACCOUNT_ID}" | tee -a ~/.bash_profile
echo "export AWS_REGION=${AWS_REGION}" | tee -a ~/.bash_profile
aws configure set default.region ${AWS_REGION}
aws configure --profile default list

```

## SSH Key 생성

1\. Cloud9에서 SSH Key를 생성합니다. 해당 키는 EKS 워커노드(Worker Node)에서 사용될것입니다. File name은 `eksapp` 으로 입력합니다.

```
cd ~/environment/
ssh-keygen

```

2\. Cloud9에서 이후 생성된 워커노드 또는 EC2에 접속하기 위해 Pem Key의 권한 설정을 합니다.

```
cd ~/environment/
mv ./eksapp ./eksapp.pem
chmod 400 ./eksapp.pem

```

3\. 생성된 SSH Public Key를 Region으로  전송합니다. 앞서 eksapp.pub로 Public Key가 생성되었습니다.

```
cd ~/environment/
aws ec2 import-key-pair --key-name "eksapp" --public-key-material fileb://./eksapp.pub

```
