# AWS 계정으로 시작

### AWS 계정 생성하기

이미 AWS 계정을 가지고 있다면 바로 이 실습의 가이드를 따라 진행할 수 있으나 계정이 없다면 먼저 AWS 계정을 만들어야 합니다. 관리자 권한이 있는 AWS 계정이 아직 없는 경우, [여기](https://aws.amazon.com/ko/premiumsupport/knowledge-center/create-and-activate-aws-account/) 를 클릭하여 계정을 생성합니다.

### IAM 사용자

**AWS 계정**을 생성했거나 이미 있는 경우, AWS 계정에 접근할 수 있는 **IAM 사용자**를 생성합니다. 계정에 로그인한 후, IAM 콘솔을 사용하여 IAM 사용자를 생성할 수 있습니다. 아래의 순서에 따라 Administrator(관리자) 권한을 가진 사용자를 생성합니다. 이미 관리자 권한을 가진 IAM 사용자가 있다면, 다음 작업을 건너뜁니다.

1\. [로그인 페이지 ](https://console.aws.amazon.com/) 에서 AWS 계정 이메일 주소와 비밀번호를 사용하여 **AWS 계정의 루트 사용자**로 [IAM 콘솔 ](https://console.aws.amazon.com/iam/home#/home) 에 로그인 합니다.

2\. IAM 콘솔 화면 왼쪽 사이드 바에서 Users(사용자)를 클릭한 다음, **Add user**(사용자 추가) 버튼을 클릭합니다.

3\. **User name**(사용자 이름)은 `Administrator`로 입력합니다.

4\. Access type(엑세스 유형)에서 **AWS Management Console access** 체크 박스를 선택하고, **Custom password**를 선택한 다음 비빌번호를 입력합니다.

5\. **Next: Permissions**을 클릭합니다.

![](../../.gitbook/assets/1.png)

6\. **Attach existing policies directly** (기존 정책 직접 연결)를 선택하고, **AdministratorAccess** 정책에 체크박스를 선택한 후, **Next: Tags** (다음: 태그)를 클릭합니다.

{% hint style="warning" %}
이번 워크샵을 위해 _AdministratorAccess_를 사용하지만 실제 현업에서 IAM 정책을 생성할 때는 최소 권한 부여의 표준 보안 조언을 따르거나, 작업 수행에 필요한 _최소한의 권한_만 부여합니다. 사용자가 수행해야 하는 작업을 파악한 후 사용자들이 _해당 작업만_ 수행하도록 사용자에 대한 정책을 작성합니다.
{% endhint %}



![](../../.gitbook/assets/2.png)

7\. **** 태그 추가(선택 사항) 단계에서 **Next: Review** (다음: 검토)를 클릭합니다.

8\. Administrator 사용자에 AdministratorAccess 관리형 정책이 추가된 것을 확인하고 **Create user** (사용자 만들기)를 클릭합니다.

9\. 사용자가 추가되면 **로그인 URL**을 복사합니다. 해당 URL은 아래의 형식을 가집니다. `https://<your_aws_account_id>.signin.aws.amazon.com/console`\


![](../../.gitbook/assets/3.png)

10\. 이제 루트 사용자에서 로그아웃하고, 방금 복사한 URL로 접속해서 **새로 생성한 Administrator 사용자**로 로그인 합니다.
