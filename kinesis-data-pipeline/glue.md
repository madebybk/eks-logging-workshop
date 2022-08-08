# Glue

## AWS Glue 구축&#x20;

![AWS Glue](../.gitbook/assets/download.jpeg)

AWS Glue는 완전 관리형 추출, 변환 및 로드(ETL) 서비스로, 효율적인 비용으로 간단하게 여러 데이터 스토어 및 데이터 스트림 간에 원하는 데이터를 분류, 정리, 보강, 이동합니다. AWS Glue는 AWS Glue Data Catalog로 알려진 중앙 메타데이터 리포지토리, 자동으로 Python 및 Scala 코드를 생성하는 ETL 엔진, 그리고 종속성 확인, 작업 모니터링 및 재시도를 관리하는 유연한 스케줄러로 구성됩니다. AWS Glue는 서버리스이므로 설정하거나 관리할 인프라가 없습니다.

AWS Glue의 기능 및 특징에 대해 추가적으로 알고 싶다면 [여기 ](https://docs.aws.amazon.com/glue/index.html)를 클릭하세요.

Glue로 로그 파일을 JSON 형태에서 parquet로 변환시키는 절차는 다음과 같습니다.

* Glue database 생성
* Sample JSON 파일을 S3 bucket에 저장
* Glue Table 생성
* Kinesis Firehose에서 data transformation 설정

### Glue Database 생성

1\. AWS Console에서 [Glue 창](https://console.aws.amazon.com/glue)으로 이동하고 **Database** -> **Add Database** 버튼을 누릅니다.

<img src="../.gitbook/assets/file.drawing (2).svg" alt="" class="gitbook-drawing">

2\. Database 이름을 입력하고 **Create** 버튼을 누릅니다.

<img src="../.gitbook/assets/file.drawing (45).svg" alt="" class="gitbook-drawing">

### S3에 샘플 JSON 파일 저장

3\. 샘플 JSON 파일을 로컬에 저장합니다.

```
# S3에 저장될 샘플 JSON 파일 저장 
cat << EOF > ~/environment/eksapp/sample-json-file.json
{
    "timestamp": "2022-07-24T21:04:37.548Z",
    "level": "info",
    "error_code": "err_01",
    "status": "OK",
    "server_protocol": "HTTP/1.1",
    "request_method": "GET",
    "request_uri": "/api/logger",
    "message": "This is a sample log message no. 1007"
}
EOF

```

4\. 샘플 파일을 저장할 S3 Bucket 이름을 정합니다.&#x20;

{% hint style="warning" %}
아래 **S3\_Bucket**_**\_**_**Name**은 정하신 Bucket 이름으로 변경하셔야 합니다.&#x20;
{% endhint %}

```
# S3 Bucket 이름을 정합니다. 
export s3_bucket_sample_json="S3_Bucket_Name"
echo ${s3_bucket_sample_json}

```

5\. S3 Bucket을 생성하고 샘플 JSON 파일을 S3에 저장합니다.&#x20;

<pre><code><strong># Bucket 생성 
</strong><strong>aws s3 mb s3://${s3_bucket_sample_json} --region ${AWS_REGION}
</strong>
# JSON 파일 upload 
cd ~/environment/eksapp/
aws s3 cp sample-json-file.json s3://${s3_bucket_sample_json}/ --region ${AWS_REGION}
</code></pre>

6\. [S3 Console](https://console.aws.amazon.com/s3)에 접속해 정상적으로 파일이 저장되었는지 확인합니다.&#x20;

<img src="../.gitbook/assets/file.drawing (53).svg" alt="" class="gitbook-drawing">

### 로그 데이터를 저장할 S3 Bucket 생성&#x20;

7\. 이번엔 Kinesis에서 전달하는 로그 데이터를 저장할 Bucket을 생성합니다.&#x20;

{% hint style="warning" %}
이전 단계처럼 새로 **S3\_Bucket\_Name** 이름을 정합니다.&#x20;
{% endhint %}

```
# S3 Bucket 이름을 정합니다. 
export s3_bucket_log_data="S3_Bucket_Name"
echo ${s3_bucket_log_data}

# Bucket 생성 
aws s3 mb s3://${s3_bucket_log_data} --region ${AWS_REGION}

```

8\. [S3 Console](https://console.aws.amazon.com/s3)에 접속해 Bucket이 정상적으로 생성됐는지 확인합니다.&#x20;

<img src="../.gitbook/assets/file.drawing (57).svg" alt="" class="gitbook-drawing">

### Glue를 위한 IAM Role&#x20;

Glue는 S3의 GetObject와 PutObject 권한이 필요합니다. AWS IAM Role을 통해 권한을 설정합니다.

9\. AWS Console에 [IAM 창](https://console.aws.amazon.com/iam)으로 이동하고 Roles -> **Create Role** 버튼을 클릭합니다.

<img src="../.gitbook/assets/file.drawing (51).svg" alt="" class="gitbook-drawing">

10\. Trust entity를 **Custom trust policy**로 정하고 아래와 같은 trust policy를 입력합니다. **Next** 버튼을 클릭합니다.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "Service": "glue.amazonaws.com"
            },
            "Action": "sts:AssumeRole"
        }
    ]
}
```

11\. 2가지 Permission을 추가합니다. **AWSGlueServiceRole**을 먼저 추가합니다. 두번째 Custom policy를 만들기 위해 Create Policy 버튼을 클릭합니다.

<img src="../.gitbook/assets/file.drawing (44).svg" alt="" class="gitbook-drawing">

12\. Custom Policy를 아래와 같이 작성합니다. \[INPUT 버켓 이름], \[ACCOUNT NUMBER], \[OUTPUT 버켓 이름] 은 반드시 S3 bucket 이름과 AWS Account Number로 수정해야 합니다.&#x20;

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:PutObject"
            ],
            "Resource": [
                "arn:aws:s3:::[INPUT 버켓 이름]/*",
                "arn:aws:s3:::aws-glue-assets-[ACCOUNT NUMBER]-ap-northeast-2/*",
                "arn:aws:s3:::[OUTPUT 버켓 이름]/*"
            ]
        }
    ]
}
```

13\. **Next: Tags** 버튼을 클릭하고 바로 **Next: Review** 를 클릭합니다. Policy의 이름을 지정하고 **Create policy** 를 클릭합니다.

<img src="../.gitbook/assets/file.drawing (96).svg" alt="" class="gitbook-drawing">

14\. 이전에 Role 생성 창으로 돌아가서 방금 만든 custom permission을 Role에 추가합니다. Next 버튼을 클릭합니다.

<img src="../.gitbook/assets/file.drawing (67).svg" alt="" class="gitbook-drawing">

15\. Role의 이름을 정하고 2가지 Permission들이 정상적으로 포함되었는지 확인합니다. **Create Role** 을 클릭해 IAM Role 생성을 완료합니다.

<img src="../.gitbook/assets/file.drawing (93).svg" alt="" class="gitbook-drawing">

### Glue Studio

16\. [AWS Glue Studio](https://console.aws.amazon.com/glue) 에서 Jobs 버튼을 클릭합니다.

<img src="../.gitbook/assets/file.drawing (13).svg" alt="" class="gitbook-drawing">

17\. **Visual with a source and target**을 선택하고 **Create** 버튼을 클릭합니다.

<img src="../.gitbook/assets/file.drawing (61).svg" alt="" class="gitbook-drawing">

18\. **Job의 이름**과 **Data Source**를 설정합니다.

* S3 Source Type: S3 Location
* S3 URL: 이전에 저장한 Sample JSON 파일 S3 주소
* Data format: JSON
* Multiline: check 표시
* Infer schema: 클릭

<img src="../.gitbook/assets/file.drawing (68).svg" alt="" class="gitbook-drawing">



19\. **Output Schema**가 정상적으로 Sample JSON 형태로 나오는지 확인합니다.

<img src="../.gitbook/assets/file.drawing (47).svg" alt="" class="gitbook-drawing">

20\. **Transform** 에서는 ApplyMapping으로 설정된 것을 확인합니다.

<img src="../.gitbook/assets/file.drawing (31).svg" alt="" class="gitbook-drawing">

21\. **Data Target**을 설정합니다.

* Format: Parquet
* Compression Type: Snappy
* S3 Target Location: 데이터 목적지 S3 위치. S3 Bucket의 위치가 Firehose와 Athena에서 partition으로 이용할 'date=yyyy-MM-dd' 형태의 prefix를 꼭 추가해 줘야 됩니다.
* Data Catalog update option: 'Do not update the Data Catalog'

<img src="../.gitbook/assets/file.drawing (60).svg" alt="" class="gitbook-drawing">

22\. Job Detail 에서 이전에 생성한 IAM Role을 설정합니다. Save -> Run 버튼을 클릭해 Job을 생성합니다. 작업이 1\~2분 정도 소요됩니다.

<img src="../.gitbook/assets/file.drawing (90).svg" alt="" class="gitbook-drawing">

23\. Job이 완성되는 것을 확인합니다.

<img src="../.gitbook/assets/file.drawing (111).svg" alt="" class="gitbook-drawing">

24\. S3 Bucket에서 parquet 파일이 생성된 것을 확인하실 수 있습니다.

<img src="../.gitbook/assets/file.drawing (7).svg" alt="" class="gitbook-drawing">

### Glue Crawler

크롤러를 사용하여 테이블로 AWS Glue Data Catalog를 채웁니다.

25\. AWS Console에서 Glue 창으로 이동합니다. Crawlers에서 **Add Crawler** 버튼을 클릭합니다.

<img src="../.gitbook/assets/file.drawing (16).svg" alt="" class="gitbook-drawing">



26\. Crawler 내용을 아래와 같이 설정하고 crawler를 생성합니다.

<img src="../.gitbook/assets/file.drawing (66).svg" alt="" class="gitbook-drawing">

<img src="../.gitbook/assets/file.drawing (114).svg" alt="" class="gitbook-drawing">

<img src="../.gitbook/assets/file.drawing (39).svg" alt="" class="gitbook-drawing">

* Glue Studio Job으로 생성된 parquet 파일 주소를 timestamp 전까지 선택합니다.

<img src="../.gitbook/assets/file.drawing (8).svg" alt="" class="gitbook-drawing">

* Glue Studio Job에서 사용한 IAM Role을 사용합니다.

<img src="../.gitbook/assets/file.drawing (37).svg" alt="" class="gitbook-drawing">

<img src="../.gitbook/assets/file.drawing (17).svg" alt="" class="gitbook-drawing">

* Glue Database와 table name prefix를 설정합니다.

<img src="../.gitbook/assets/file.drawing (23).svg" alt="" class="gitbook-drawing">

27\. Crawler **Run crawler** 버튼을 통해 Glue Table을 생성합니다.

<img src="../.gitbook/assets/file.drawing (59).svg" alt="" class="gitbook-drawing">

28\. Glue Database에서 생성된 Table을 확인합니다.

<img src="../.gitbook/assets/file.drawing (104).svg" alt="" class="gitbook-drawing">

