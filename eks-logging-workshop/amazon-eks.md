# Amazon EKS 개념

### Amazon EKS란?

Amazon Elastic Kubernetes Service(Amazon EKS)는 클라우드 또는 온프레미스에서 Kubernetes 애플리케이션을 쉽게 실행할 수 있는 관리형 컨테이너 서비스입니다.

Amazon EKS를 사용시 AWS 인프라의 모든 성능, 규모, 안정성 및 가용성뿐만 아니라 AWS 네트워킹 및 보안 서비스(Application Load Balancer(ALB), AWS Identity and Access Management(IAM), AWS Virtual Private Cloud(VPC) )와의 통합에 따른 이점을 활용할 수 있습니다.

### High level 아키텍처&#x20;

![](<../.gitbook/assets/EKS\_workshop.drawio (2).png>)

Amazon EKS는 Kubernetes 컨트롤플레인 및 작업자 노드를 프로비저닝 및 관리함으로써 작동합니다. Kubernetes는 컨테이너를 실행하는 **데이터플레인(Data Plane)** 클러스터와 클러스터에서 컨테이너가 시작된 시간과 위치를 관리하고 그 상태를 모니터링하는 **컨트롤플레인(Control Plane)** 이라는 2가지 주요 구성 요소로 이루어져 있습니다.

Amazon EKS가 없으면 Kubernetes 컨트롤플레인과 데이터플레인 클러스터를 모두 직접 실행해야 합니다. Amazon EKS를 사용할 때는 AWS Console, CLI 또는 API에서 단일 명령을 사용하여 워커노드를 프로비저닝합니다. AWS는 고가용성의 보안 구성으로 Kubernetes 컨트롤플레인 프로비저닝, 크기 조정 및 관리 작업을 처리합니다. 이를 통해 운영 부담을 현저히 줄이고 AWS 인프라를 관리하는 대신 애플리케이션 구축에 집중할 수 있게 됩니다.
