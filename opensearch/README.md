# OpenSearch

![](../.gitbook/assets/opensearch\_logo\_1000x500.png)

## OpenSearch란?

OpenSearch는 Apache 2.0 license 하에 제공되는 오픈 소스 검색 및 분석 툴으로, 실시간 애플리케이션 모니터링, 로그 분석 및 웹 사이트 검색과 같이 다양한 사용 사례에 사용됩니다. OpenSearch는 대량 데이터 볼륨에 빠르게 액세스 및 응답하는, 뛰어난 확장성을 지닌 시스템을 제공합니다. OpenSearch는 Apache Lucene 검색 라이브러리로 구동되며 k-nearest neighbors(KNN) 검색, SQL, Anomaly Detection, Machine Learning Commons, Trace Analytics, 전체 텍스트 검색 등 다수의 검색 및 분석 기능을 지원하며, OpenSearch를 사용하면 로그 분석, 애플리케이션 검색, 엔터프라이즈 검색 및 기타 다수의 사용 사례에서 데이터의 수집, 보안, 검색, 집계, 보기 및 분석이 쉬워집니다.

OpenSearch의 기능 및 특징에 대해 추가적으로 알고 싶다면 [여기 ](https://opensearch.org/docs/latest/)를 클릭하세요.

### Kibana

Kibana는 Elastic Stack을 기반으로 구축된 무료 오픈 소스 프론트엔드 애플리케이션으로, OpenSearch에서 색인된 데이터를 검색하고 시각화하는 기능을 제공합니다.

이번 워크샵에서는 Kibana를 사용해 로그 데이터를 간단하게 분석해 보겠습니다.

Kibana의 기능 및 특징에 대해 추가적으로 알고 싶다면 [여기 ](https://www.elastic.co/guide/en/kibana/index.html)를 클릭하세요.

## Kinesis + OpenSearch Data Pipeline

![](<../.gitbook/assets/EKS\_workshop-OpenSearch.drawio (3).png>)

* 새로운 Kinesis Delivery Stream을 구축해 데이터를 OpenSearch로 보냅니다.
* Kibana로 OpenSearch에 있는 로그를 분석합니다.
* OpenSearch의 error 데이터는 S3로 저장합니다.
