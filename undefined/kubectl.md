# kubectl 설치

## kubectl install

**kubectl**은 쿠버네티스 클러스터에 명령을 내리는 CLI입니다. 쿠버네티스는 오브젝트 생성, 수정 혹은 삭제와 관련한 동작을 수행하기 위해 **쿠버네티스 API**를 사용합니다. 이때, kubectl CLI를 사용하면 해당 명령어가 쿠버네티스 API를 호출해 관련 동작을 수행합니다.

Kubernetes 버전 1.23 출시부터 공식적으로 Amazon EKS AMI에는 container가 유일한 런타임으로 포함됩니다. Kubernetes 버전 1.18–1.21은 Docker를 기본 런타임으로 사용합니다.

1\. kubectl 바이너리 버전은 1.21.5를 설치합니다.&#x20;

```
cd ~
curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.21.5/bin/linux/amd64/kubectl

```

2\. 바이너리에 실행권한을 적용합니다.

```
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl

```

3\. **** kubectl이 정상적으로 설치 되었는지 확인합니다.

```
kubectl version --short --client

```

4\. kubectl 자동완성을 설치합니다.

```
source <(kubectl completion bash)
echo "source <(kubectl completion bash)" >> ~/.bashrc

```
