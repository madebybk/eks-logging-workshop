# Table of contents

* [EKS Logging Workshop](README.md)
  * [Kubernetes(k8s) 개념](eks-logging-workshop/kubernetes-k8s.md)
  * [Amazon EKS 개념](eks-logging-workshop/amazon-eks.md)
* [실습 환경 구축](undefined/README.md)
  * [실습 유의사항 및 계정 준비](undefined/undefined/README.md)
    * [AWS 계정으로 시작](undefined/undefined/aws.md)
  * [Cloud9 환경 구성](undefined/cloud9.md)
  * [Cloud9 에 패키지 설치](undefined/cloud9-1.md)
  * [kubectl 설치](undefined/kubectl.md)
  * [유틸리티 설치](undefined/undefined-1.md)
  * [Shell 환경변수 저장](undefined/shell.md)
  * [KMS Key 생성](undefined/kms-key.md)
* [EKS 구현](eks/README.md)
  * [네트워크 구성](eks/undefined/README.md)
    * [CloudFormation](eks/undefined/cloudformation.md)
  * [EKS Cluster 생성](eks/eks-cluster/README.md)
    * [EKS 구성](eks/eks-cluster/eks.md)
  * [EKS Ingress](eks/eks-ingress/README.md)
    * [AWS Load Balancer Controller 구축](eks/eks-ingress/aws-load-balancer-controller.md)
* [CloudWatch Logs](cloudwatch-logs/README.md)
  * [Fluent Bit](cloudwatch-logs/fluent-bit.md)
  * [CloudWatch Logs로 로그 전송](cloudwatch-logs/cloudwatch-logs.md)
* [Kinesis로 Data Pipeline 구성](kinesis-data-pipeline/README.md)
  * [Kinesis Data Stream](kinesis-data-pipeline/kinesis-data-stream.md)
  * [Glue](kinesis-data-pipeline/glue.md)
  * [Kinesis Firehose](kinesis-data-pipeline/kinesis-firehose.md)
  * [Athena로 데이터 분석](kinesis-data-pipeline/athena.md)
* [OpenSearch](opensearch/README.md)
  * [OpenSearch Service Domain 생성](opensearch/opensearch-service-domain.md)
  * [Kinesis Firehose 생성](opensearch/kinesis-firehose.md)
  * [Kibana로 데이터 Visualization 하기](opensearch/kibana-visualization.md)
* [최종 Logging Pipeline](logging-pipeline.md)
* [실습 리소스 정리](undefined-1.md)
