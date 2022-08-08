# 네트워크 구성

## VPC 구성

이 워크샵은 **CloudFormation**으로 아래와 같은 VPC를 구성합니다.

* 인터넷 게이트웨이를 사용해 서울 리전에 있는 워커노드 VPC에 접근합니다.
* 퍼블릭 서브넷에 있는 NAT 게이트웨이를 통해 프라이빗 서브넷에 있는 EKS Cluster에 접속합니다.

![](<../../.gitbook/assets/EKS\_workshop-VPC.drawio (3).png>)
