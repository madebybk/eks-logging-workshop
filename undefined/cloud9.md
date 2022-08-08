# Cloud9 환경 구성

## Cloud9 IDE 생성

1\. AWS Cloud9 콘솔창 에 접속한 후, **Create environment** 버튼을 클릭합니다. IDE 이름을 적은 후, **Next step**을 클릭합니다.

![](<../.gitbook/assets/Screen Shot 2022-07-23 at 11.36.19 PM.png>)

2\. 인스턴스 타입(instance type)을 other instance type 버튼을 클릭 후, **t3.large** 로 선택합니다. 플랫폼(platform) **Amazon Linux 2 (recommended)** 를 선택하고, Next step을 클릭하여 지정한 속성 값을 확인한 후, **Create environment**를 클릭합니다.

<img src="../.gitbook/assets/file.drawing (97).svg" alt="" class="gitbook-drawing">

3\. 생성이 완료되면 아래와 같은 화면이 나타납니다.

![](<../.gitbook/assets/Screen Shot 2022-07-23 at 11.33.18 PM.png>)

## IAM Role 생성

IAM Role은 특정 권한을 가진 IAM 자격 증명입니다. IAM 역할의 경우, IAM 사용자 및 AWS가 제공하는 서비스에 사용할 수 있습니다. 서비스에 IAM Role을 부여할 경우, 서비스가 사용자를 대신하여 수임받은 역할을 수행합니다.

본 실습에서는 **Administrator Access** 정책을 가진 IAM Role을 생성하여 AWS Cloud9에 붙입니다.

1\. [여기 ](https://us-east-1.console.aws.amazon.com/iamv2/home#/roles)를 클릭하여 IAM Role 페이지에 접속합니다.

<img src="../.gitbook/assets/file.drawing (42).svg" alt="" class="gitbook-drawing">

2\. **Create role**을 클릭합니다.

<img src="../.gitbook/assets/file.drawing (55).svg" alt="" class="gitbook-drawing">

3\. **AWS service** 및 **EC2**가 선택된 것을 확인하고 **Next**를 클릭합니다.

<img src="../.gitbook/assets/file.drawing (35).svg" alt="" class="gitbook-drawing">

4\. **AdministratorAccess** 정책이 선택된 것을 확인하고 **Next: Tags**를 클릭합니다.

<img src="../.gitbook/assets/file.drawing (78).svg" alt="" class="gitbook-drawing">

5\. **Role name**에 `eks-workspace-admin`을 입력한 후, AdministratorAccess 관리형 정책이 추가된 것을 확인하고 **Create role**을 클릭합니다.

<img src="../.gitbook/assets/file.drawing (5).svg" alt="" class="gitbook-drawing">

<img src="../.gitbook/assets/file.drawing (91).svg" alt="" class="gitbook-drawing">

## **Cloud9 권한 설정**

AWS Cloud9 환경은 EC2 인스턴스로 구동됩니다. 따라서 EC2 콘솔에서 AWS Cloud9 인스턴스에 방금 생성한 IAM Role을 부여합니다.

1\. **** [여기 ](https://ap-northeast-2.console.aws.amazon.com/ec2/v2/home?region=ap-northeast-2#Home:)를 클릭하여 EC2 인스턴스 페이지에 접속합니다.

2\. 해당 인스턴스를 선택 후, **Actions > Security > Modify IAM Role**을 클릭합니다.

<img src="../.gitbook/assets/file.drawing (46).svg" alt="" class="gitbook-drawing">

3\. IAM Role에서 `eksworkspace-admin`을 선택한 후, **Save** 버튼을 클릭합니다.

<img src="../.gitbook/assets/file.drawing (87).svg" alt="" class="gitbook-drawing">

4\. Cloud9 IDE의 IAM 역할이 정상적으로 연결되었는지 확인합니다.

<img src="../.gitbook/assets/file.drawing (43).svg" alt="" class="gitbook-drawing">

## 기존 자격 증명 파일 제거

Cloud9의 기존 자격 증명과 임시 자격 증명등을 비활성화 합니다.

1\. AWS Cloud9 콘솔창에서 생성한 IDE로 다시 접속한 후, 우측 상단에 기어 아이콘을 클릭한 후, 사이드 바에서 **AWS Setting**을 클릭합니다.

2\. **Credentials** 항목에서 **AWS managed temporary credentials** 설정을 비활성화합니다.

<img src="../.gitbook/assets/file.drawing (10).svg" alt="" class="gitbook-drawing">

3\. **Temporary credentials**이 없는지 확실히 하기 위해 기존의 자격 증명 파일도 제거합니다.

```
rm -vf ${HOME}/.aws/credentials

```

### Cloud9 IDE 권한 점검

1\. Cloud9 이 올바른 IAM 역할을 사용하고 있는지 확인합니다. (앞서 선언한 IAM Role 이름을 `eksworkspace-admin` 으로 선언하지 않은 경우에는 사용한 이름으로 변경합니다.)

```
aws sts get-caller-identity --region ap-northeast-2 --query Arn | grep eksworkspace-admin -q && echo "IAM role valid" || echo "IAM role NOT valid"

```

2\. 실제 Role의 Arn은 아래 명령을 통해 확인 할 수 있습니다.

```
aws sts get-caller-identity --region ap-northeast-2

```
