# EKS Ingress

![Kubernetes Ingress](../../.gitbook/assets/ingress.png)

## Ingress Controller란?

인그레스(Ingress)는 클러스터 외부에서 쿠버네티스 안으로 접근하는 요청들을 어떻게 처리할지 정의해둔 규칙 모음입니다. 클러스터 외부에서 접근해야 할 URL을 사용할 수 있도록 하고, 로드밸런싱, TLS/SSL 인증서 처리, 도메인 기반 가상 호스팅도 제공합니다. Ingress는 이런 규칙들을 정의해둔 자원이고, 실제로 규칙들을 동작시키는 것은 Ingress Controller입니다.

### AWS Load Balancing (Load Balancer Controller)

Kubernetes ingress를 생성할 때 애플리케이션 트래픽을 로드 밸런싱하는 AWS Application Load Balancer(ALB)가 프로비저닝됩니다. 수신 리소스는 ALB를 구성하여 HTTP 또는 HTTPS 트래픽을 L7 layer 클러스터 내 다른 포드로 라우팅합니다. ALB 수신 컨트롤러는 EKS Cluster에서 실행 중인 프로덕션 워크로드에서 지원됩니다.

AWS Load Balancer Controller는 다음과 같은 트래픽 모드를 지원합니다.

* **인스턴스**(default): 클러스터 내의 노드를 ALB의 대상으로 등록합니다. ALB에 도달하는 트래픽은 서비스를 위해 NodePort로 라우팅 된 다음 포드로 프록시됩니다.
* **IP**: Pod를 ALB의 대상으로 등록합니다. ALB에 도달하는 트래픽은 서비스를 위해 Pod로 직접 라우팅 됩니다. 이 트래픽 모드를 사용하려면 `alb.ingress.kubernetes.io/target-type: ip` 주석을 지정해야 합니다.
