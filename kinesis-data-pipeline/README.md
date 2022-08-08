# Kinesis로 Data Pipeline 구성

![](../.gitbook/assets/images.jpeg)

## Amazon Kinesis란?

Amazon Kinesis를 사용하면 실시간 스트리밍 데이터를 손쉽게 수집, 처리 및 분석할 수 있으므로 적시에 통찰력을 확보하고 새로운 정보에 신속하게 대응할 수 있습니다. Amazon Kinesis는 모든 규모의 스트리밍 데이터를 비용 효율적으로 처리할 수 있는 핵심 기능과 더불어 애플리케이션 요구 사항에 가장 적합한 도구를 선택할 수 있는 유연성을 제공합니다. Amazon Kinesis에서는 기계 학습, 분석 및 기타 애플리케이션을 위해 비디오, 오디오, 애플리케이션 로그, 웹 사이트 클릭스트림 및 IoT 텔레메트리 데이터와 같은 실시간 데이터를 수집할 수 있습니다. Amazon Kinesis를 사용하면 모든 데이터가 수집된 후에야 처리를 시작할 수 있는 것이 아니라 데이터가 수신되는 대로 처리 및 분석하여 즉시 대응할 수 있습니다.

Amazon Kinesis의 기능 및 특징에 대해 추가적으로 알고 싶다면 [여기 ](https://docs.aws.amazon.com/kinesis/index.html)를 클릭하세요.

Kinesis를 구축하게 된다면 큰 규모의 로그 데이터를 buffering하고 애플리케이션에 문제가 생기면 신속하게 대응할 수 있습니다. 이번 워크샵에서는 Kinesis Data Stream과 Kinesis Firehose를 사용할 것입니다.

### Amazon Kinesis Data Stream

Kinesis Data Stream은 모든 규모의 데이터 스트림을 쉽게 **캡처, 처리 및 저장할 수 있는 서버리스 스트리밍 데이터 서비스**입니다. 특수 요구에 맞춰 스트리밍 데이터를 처리 또는 분석하는 사용자 지정 애플리케이션을 구축할 수 있습니다. 수십 만개의 소스에서 클릭 스트림, 애플리케이션 로그, 소셜 미디어와 같은 다양한 유형의 데이터를 Kinesis 데이터 스트림에 추가할 수 있습니다.

### Amazon Kinesis Firehose

Kinesis Data Firehose는 스트리밍 데이터를 **안정적으로 캡처하고 변환하여 데이터 레이크, 데이터 스토어, 분석 서비스에 전달하는 추출, 변환 로드 서비스**입니다. 이번 워크샵에는 **AWS Glue**라는 ETL 서비스를 Firehose에서 사용해, 로그를 JSON 형태에서 Parquet으로 변환할 것입니다.

## Kinesis Log Data Pipeline

![](<../.gitbook/assets/EKS\_workshop-Kinesis.drawio (3).png>)

아래와 같은 Data Pipeline으로 로깅 아키텍처를 구축할 것입니다.

* 기존에 구축한 CloudWatch Logs는 변경하지 않고 새로운 파이프라인을 추가합니다.
* Kinesis Data Stream: 데이터 ingestion 하고 buffering 역할을 합니다.
* Kinesis Firehose: KDS로 전송된 데이터를 S3로 전송합니다.
* Glue: ETL -> JSON 형태 로그 데이터를 Parquet 형태로 전환합니다.
* Athena: S3에 저장된 로그 데이터를 분석합니다.
