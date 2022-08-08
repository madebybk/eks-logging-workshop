# AWS Load Balancer Controller 구축

### CoreDNS

1\. Kubernetes에 DNS 서비스를 사용할수 있도록 CoreDNS를 생성합니다.

```
eksctl create addon --name coredns --cluster eksapp --force

```

### OIDC

AWS Identity and Access Management에서는 OpenID Connect(OIDC)를 사용해 연동 자격 증명을 지원하는 기능이 있습니다. 이 기능을 사용하면 지원되는 자격 증명 공급자를 이용해 AWS API 호출을 인증하고 유효한 OIDC JSON 웹 토큰(JWT)을 수신할 수 있습니다. 이 토큰을 AWS STS `AssumeRoleWithWebIdentity` API 작업에 전달하고 IAM 임시 역할 자격 증명을 수신할 수 있습니다. 이 자격 증명을 사용하여 다양한 AWS 서비스와 상호 작용할 수 있습니다.&#x20;

EKS 클러스터에 IAM OIDC Provider가 존재해야만 kubernetes가 직접 관리하는 **Service Account**에 IAM Role을 사용할 수 있습니다.

2\. IAM OIDC Provider를 생성합니다.&#x20;

```
source ~/.bash_profile
eksctl utils associate-iam-oidc-provider \
    --region ${AWS_REGION} \
    --cluster ${ekscluster_name} \
    --approve

```

3\. 생성된 OICD를 확인해 봅니다.

```
aws eks describe-cluster --name ${ekscluster_name} --query "cluster.identity.oidc.issuer" --output text
aws iam list-open-id-connect-providers

```

4\. ALB Load Balancer Controller에 대한 IAM 정책(AWSLoadBalancerControllerIAMPolicy)을 생성합니다.

```
cd ~/environment/eksapp/alb-controller
aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://./iam_policy.json

```

5\. AWS LoadBalancer Controller에 대한 Service Account를 생성하고, 앞서 생성한 IAM Role을 연결합니다.

```
eksctl create iamserviceaccount \
--cluster=${ekscluster_name} \
--namespace=kube-system \
--name=aws-load-balancer-controller \
--attach-policy-arn=arn:aws:iam::${ACCOUNT_ID}:policy/AWSLoadBalancerControllerIAMPolicy \
--override-existing-serviceaccounts \
--region ${AWS_REGION} \
--approve

```

6\. Service Account가 정상적으로 생성됐는지 확인합니다.

```
kubectl get serviceaccounts -n kube-system aws-load-balancer-controller -o yaml

```

7\. Certification Manager(인증서 관리자)를 설치합니다.

```
kubectl apply --validate=false -f https://github.com/jetstack/cert-manager/releases/download/v1.5.3/cert-manager.yaml

```

8\. AWS Loadbalancer Controller Pod를 배포합니다.

```
cd ~/environment/eksapp/alb-controller
kubectl apply -f v2_3_1_full.yaml

```

9\. 배포가 성공적으로 되고 **Load Controller**가 실행되는지 아래의 명령어를 통해 확인합니다. 결과 값이 도출되면 정상임을 의미합니다.

```
kubectl get deployment -n kube-system aws-load-balancer-controller

```

10\. 아래의 명령어를 통해, **Service Account**가 생성된 것을 확인할 수 있습니다.

```
kubectl get sa aws-load-balancer-controller -n kube-system -o yaml

```

### ALB Ingress Controller 기반 Application 배포

이 워크샵에서는 랜덤 JSON을 지속적으로 Logging하는 [nextjs-random-logger](https://github.com/madebybk/nextjs-random-logger)를 쿠버네티스 클러스터에 배포합니다. 이 application의 도커 이미지는 [Docker Hub](https://hub.docker.com/r/madebybk/nextjs-random-json-logger)에서 찾을 수 있습니다.

11\. 아래와 같이 application을 EKS로 배포합니다. 배포하는 Object는 다음과 같습니다.

* Namespace
* Deployment
* Service
* Ingress

```
cd ~/environment/eksapp/alb-controller
kubectl apply -f log-generator-namespace.yaml
kubectl apply -f log-generator-deployment.yaml
kubectl apply -f log-generator-service.yaml
kubectl apply -f log-generator-ingress.yaml

```

12\. 정상적으로 배포됐는지 확인합니다.

```
kubectl -n log-generator get pods,svc,ingress

```

13\. Application URL을 확인하고 접속해 봅니다. Pod 배포 후 정상적으로 접속 가능하기에 1\~2분 정도 소요될 수도 있습니다.

```
kubectl -n log-generator get ingress log-generator-ingress | tail -n 1 | awk '{ print "log-generator URL = http://"$4 }'

```

14\. 아래와 같이 application이 정상적으로 접속되는지 확인합니다. 워크샵 다음 부분에서 이 앱을 통하여 샘플 JSON 로그를 수집하는 data pipeline을 알아보겠습니다.

![](<../../.gitbook/assets/Screen Shot 2022-07-25 at 2.27.29 AM.png>)
