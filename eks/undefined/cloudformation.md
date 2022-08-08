# CloudFormation

![](../../.gitbook/assets/1\_lgMb-q1UPxco34n6s\_lLVw.png)

## CloudFormation이란?

AWS CloudFormation은 개발자와 기업이 손쉽게 관련 AWS 및 서드 파티 리소스의 모음을 쉽게 생성하고 순서에 따라 예측 가능한 방식으로 프로비저닝 및 관리할 수 있는 방법을 제공하는 서비스입니다. CloudFormation 템플릿에는 원하는 리소스와 종속성이 설명되어 있으므로 이를 모두 하나의 스택으로 구성하고 시작할 수 있습니다. 리소스를 개별적으로 관리하는 대신 템플릿을 통해 전체 스택을 단일 단위로 처리하여 필요한 만큼 자주 생성 및 업데이트하고 삭제할 수 있습니다. 스택은 여러 AWS 계정 및 AWS 리전에 걸쳐 관리 및 프로비저닝할 수 있습니다.

CloudFormation의 기능 및 특징에 대해 추가적으로 알고 싶다면 [여기 ](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html)를 클릭하세요.

## CloudFormation 생성

1 . 이번 워크샵에 사용할 다양한 CloudFormation yaml file들을 [Git에서](https://github.com/madebybk/eksapp) 내려받습니다. Cloud9에서 아래와 같이 실행합니다.&#x20;

```
cd ~/environment
git clone https://github.com/madebybk/eksapp.git

```

2\. CloudFormation을 실행해 VPC를 구성합니다. 생성 시간은 5분 정도 필요합니다.

```
cd ./eksapp/
aws cloudformation deploy \
  --stack-name "eksapp" \
  --template-file "EKSVPC3AZ.yaml" \
  --capabilities CAPABILITY_NAMED_IAM
  
```

3\. VPC 구성이 정상적으로 완료된 것을 확인합니다. CloudFormation 과정을 AWS Console에서 확인하실 수 있습니다. EKS Cluster를 생성하기 위해 필요한 VPC 자원들이 정상적으로 생성되었는지 확인합니다.

* VPC
* 프라이빗 서브넷(Private Subnet) 01, 02, 03
* 퍼블릭 서브넷(Public Subnet) 01, 02, 03

<img src="../../.gitbook/assets/file.drawing (88).svg" alt="" class="gitbook-drawing">
