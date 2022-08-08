# 실습 리소스 정리

### Kubernetes 자원 삭제&#x20;

```
## 생성된 Kubnernetes 자원 삭제

kubectl delete namespace amazon-cloudwatch
kubectl delete namespace log-generator

```

### EKS Cluster 삭제

```
eksctl delete cluster --name eksapp

```

### VPC 삭제

```
aws cloudformation delete-stack --stack-name "eksapp"

```

### Cloud9 삭제

AWS Console -> Cloud9 -> 생성된 Cloud9 삭제

### Kinesis 삭제

AWS Console -> Kinesis -> 생성된 Kinesis Data Stream과 Kinesis Delivery Stream 삭제

### Glue 삭제

AWS Console -> Glue -> 생성된 Database와 Table 삭제

### S3 Bucket 삭제

AWS Console -> S3 -> 생성된 S3 Bucket 모두 삭제

### OpenSearch 삭제

AWS Console -> OpenSearch -> 생성된 Domain Service 삭제
