# EKS Cluster 생성

![](../../.gitbook/assets/logo.png)

## eksctl이란?

**eksctl**은 Amazon EKS에서 Kubernetes 클러스터를 가장 빠르고 쉽게 생성하는 CLI 툴입니다. Go로 작성되었으며 CloudFormation을 사용하지만 EKS UI, CDK, Terraform, Rancher 등 다양한 도구로도 구성이 가능합니다.

eksctl의 기능 및 특징에 대해 추가적으로 알고 싶다면 [여기 ](https://eksctl.io/)를 클릭하세요.

## EKS Cluster 아키텍처&#x20;

이 워크샵은 방금 생성한 VPC 기반으로 아래와 같은 cluster 아키텍처를 eksctl 통해 구성합니다.

![](<../../.gitbook/assets/EKS\_workshop-EKS Full.drawio (1).png>)
