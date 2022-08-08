# OpenSearch Service Domain 생성

OpenSearch Service 도메인은 OpenSearch 클러스터와 동의어입니다. 도메인은 설정, 인스턴스 유형, 인스턴스 수, 스토리지 리소스를 지정한 설정입니다. 콘솔, AWS CLI 또는 AWS SDK를 사용하여 OpenSearch Service 도메인을 만들 수 있습니다.

## **콘솔을 사용하여 OpenSearch Service Domain 생성하기**

1 . AWS Console에서 OpenSearch Service 창으로 이동합니다. Create Domain을 클릭해 Domain 생성 절차를 시작합니다.

<img src="../.gitbook/assets/file.drawing (25).svg" alt="" class="gitbook-drawing">

2\. Domain 이름을 입력합니다. 이 워크샵 예제에서는 _eksapp-sample-analytics_라는 이름을 사용합니다.  Customer endpoint는 무시합니다.

<img src="../.gitbook/assets/file.drawing (64).svg" alt="" class="gitbook-drawing">

3\. 배포 유형은 **Development and testing**을 선택합니다. **Version**은 최신 버전을 선택합니다.

<img src="../.gitbook/assets/file.drawing (54).svg" alt="" class="gitbook-drawing">

4\. **Data nodes**에서 인스턴스 유형을 `r6g.large.search`로 지정하고 세 개의 노드 기본값을 유지합니다.

<img src="../.gitbook/assets/file.drawing (62).svg" alt="" class="gitbook-drawing">

5\. 이번 워크샵에서는 간단한 설명을 위해 퍼블릭 액세스 도메인을 사용합니다. **Network**에서 **Public Access**를 선택합니다. 세분화된 액세스 제어 설정에서 **Create master user**을 선택합니다. 사용자 이름과 암호를 입력합니다.

<img src="../.gitbook/assets/file.drawing (69).svg" alt="" class="gitbook-drawing">

6\. **Access policy**에서 **Only use fine-grained access control**을 선택합니다. 이번 워크샵에서는 분화된 액세스 제어를 통해 도메인 액세스 정책이 아닌 인증을 처리합니다.

<img src="../.gitbook/assets/file.drawing (107).svg" alt="" class="gitbook-drawing">

7\. 나머지 설정은 무시하고 **Create**을 선택합니다. 새 domain은 초기화하는 데 15\~30분 정도 걸리지만 구성에 따라 시간이 더 오래 걸릴 수 있습니다. 생성이 완료되면 domain을 선택하여 구성 창을 엽니다. **General information**에 보이는 Domain Endpoint를 클릭하시면 됩니다. 예: `https://search-my-domain.us-east-1.es.amazonaws.com`).

<img src="../.gitbook/assets/file.drawing (89).svg" alt="" class="gitbook-drawing">

8\. 다음과 같은 화면이 나오는지 확인하고 이전에 설정한 Master 로그인 정보로 Kibana에 접속합니다.

<img src="../.gitbook/assets/file.drawing (27).svg" alt="" class="gitbook-drawing">

9\. 로그인이 정상적으로 되면 다음과 같은 화면이 나옵니다.

<img src="../.gitbook/assets/file.drawing (110).svg" alt="" class="gitbook-drawing">
