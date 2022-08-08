# EKS 구성

## eksctl을 통해 EKS Cluster 생성

1\. 아래와 같이 eksctl을 설치하고 버전을 확인합니다.

```
# eksctl 설정 
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin

# eksctl 자동완성 - bash
. <(eksctl completion bash)
eksctl version

```

2\. 앞서 생성된 VPC 정보 확인 결과값을 `vpc_subnet.txt` 에 저장합니다.

```
cd ~/environment/
#VPC ID export
export vpc_ID=$(aws ec2 describe-vpcs --filters Name=tag:Name,Values=eksapp | jq -r '.Vpcs[].VpcId')
echo $vpc_ID

#Subnet ID, CIDR, Subnet Name export
aws ec2 describe-subnets --filter Name=vpc-id,Values=$vpc_ID | jq -r '.Subnets[]|.SubnetId+" "+.CidrBlock+" "+(.Tags[]|select(.Key=="Name").Value)'
echo $vpc_ID > vpc_subnet.txt
aws ec2 describe-subnets --filter Name=vpc-id,Values=$vpc_ID | jq -r '.Subnets[]|.SubnetId+" "+.CidrBlock+" "+(.Tags[]|select(.Key=="Name").Value)' >> vpc_subnet.txt
cat vpc_subnet.txt

# VPC, Subnet ID 환경변수 저장 
export PublicSubnet01=$(aws ec2 describe-subnets --filter Name=vpc-id,Values=$vpc_ID | jq -r '.Subnets[]|.SubnetId+" "+.CidrBlock+" "+(.Tags[]|select(.Key=="Name").Value)' | awk '/eksapp-PublicSubnet01/{print $1}')
export PublicSubnet02=$(aws ec2 describe-subnets --filter Name=vpc-id,Values=$vpc_ID | jq -r '.Subnets[]|.SubnetId+" "+.CidrBlock+" "+(.Tags[]|select(.Key=="Name").Value)' | awk '/eksapp-PublicSubnet02/{print $1}')
export PublicSubnet03=$(aws ec2 describe-subnets --filter Name=vpc-id,Values=$vpc_ID | jq -r '.Subnets[]|.SubnetId+" "+.CidrBlock+" "+(.Tags[]|select(.Key=="Name").Value)' | awk '/eksapp-PublicSubnet03/{print $1}')
export PrivateSubnet01=$(aws ec2 describe-subnets --filter Name=vpc-id,Values=$vpc_ID | jq -r '.Subnets[]|.SubnetId+" "+.CidrBlock+" "+(.Tags[]|select(.Key=="Name").Value)' | awk '/eksapp-PrivateSubnet01/{print $1}')
export PrivateSubnet02=$(aws ec2 describe-subnets --filter Name=vpc-id,Values=$vpc_ID | jq -r '.Subnets[]|.SubnetId+" "+.CidrBlock+" "+(.Tags[]|select(.Key=="Name").Value)' | awk '/eksapp-PrivateSubnet02/{print $1}')
export PrivateSubnet03=$(aws ec2 describe-subnets --filter Name=vpc-id,Values=$vpc_ID | jq -r '.Subnets[]|.SubnetId+" "+.CidrBlock+" "+(.Tags[]|select(.Key=="Name").Value)' | awk '/eksapp-PrivateSubnet03/{print $1}')
echo "export vpc_ID=${vpc_ID}" | tee -a ~/.bash_profile
echo "export PublicSubnet01=${PublicSubnet01}" | tee -a ~/.bash_profile
echo "export PublicSubnet02=${PublicSubnet02}" | tee -a ~/.bash_profile
echo "export PublicSubnet03=${PublicSubnet03}" | tee -a ~/.bash_profile
echo "export PrivateSubnet01=${PrivateSubnet01}" | tee -a ~/.bash_profile
echo "export PrivateSubnet02=${PrivateSubnet02}" | tee -a ~/.bash_profile
echo "export PrivateSubnet03=${PrivateSubnet03}" | tee -a ~/.bash_profile
source ~/.bash_profile

```

3\. 앞서 저장한 AWS 리전과 KMS Key ARN을 확인합니다. ARN은 `MASTER_ARN.txt` 파일로 저장합니다.

```
#사용자 AWS Region
echo $AWS_REGION

#사용자 KMS Key ARN
echo $MASTER_ARN
cat master_arn.txt

```

4\. Shell 변수에 VPC, EKS Cluster 등 값을 입력하고, 정상적으로 값이 출력되는지 확인합니다.

```
export ekscluster_name="eksapp"
export eks_version="1.21"
export instance_type="m5.xlarge"
export public_mgmd_node="managed-frontend-workloads"
export private_mgmd_node="managed-backend-workloads"
export publicKeyPath="/home/ec2-user/environment/eksapp.pub"
echo ${ekscluster_name}
echo ${AWS_REGION}
echo ${eks_version}
echo ${PublicSubnet01}
echo ${PublicSubnet02}
echo ${PublicSubnet03}
echo ${PrivateSubnet01}
echo ${PrivateSubnet02}
echo ${PrivateSubnet03}
echo ${MASTER_ARN}
echo ${instance_type}
echo ${public_mgmd_node}
echo ${private_mgmd_node}
echo ${publicKeyPath}

```

5\. Shell profile에 값을 저장합니다.

```
# ekscluster name, version, instance type, nodegroup label 환경변수 설정.  
echo "export ekscluster_name=${ekscluster_name}" | tee -a ~/.bash_profile
echo "export eks_version=${eks_version}" | tee -a ~/.bash_profile
echo "export instance_type=${instance_type}" | tee -a ~/.bash_profile
echo "export public_mgmd_node=${public_mgmd_node}" | tee -a ~/.bash_profile
echo "export private_mgmd_node=${private_mgmd_node}" | tee -a ~/.bash_profile
echo "export publicKeyPath=${publicKeyPath}" | tee -a ~/.bash_profile
source ~/.bash_profile

```

6\. EKS ClusterConfig yaml 파일을 생성합니다.

```
# eksctl yaml 실행 
cat << EOF > ~/environment/eksapp/eksapp-cluster.yaml
---
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig

metadata:
  name: ${ekscluster_name}
  region: ${AWS_REGION}
  version: "${eks_version}"  
vpc: 
  id: ${vpc_ID}
  subnets:
    public:
      PublicSubnet01:
        az: ${AWS_REGION}a
        id: ${PublicSubnet01}
      PublicSubnet02:
        az: ${AWS_REGION}b
        id: ${PublicSubnet02}
      PublicSubnet03:
        az: ${AWS_REGION}c
        id: ${PublicSubnet03}
    private:
      PrivateSubnet01:
        az: ${AWS_REGION}a
        id: ${PrivateSubnet01}
      PrivateSubnet02:
        az: ${AWS_REGION}b
        id: ${PrivateSubnet02}
      PrivateSubnet03:
        az: ${AWS_REGION}c
        id: ${PrivateSubnet03}
secretsEncryption:
  keyARN: ${MASTER_ARN}

managedNodeGroups:
  - name: managed-ng-public-01
    instanceType: ${instance_type}
    subnets:
      - ${PublicSubnet01}
      - ${PublicSubnet02}
      - ${PublicSubnet03}
    desiredCapacity: 3
    minSize: 3
    maxSize: 6
    volumeSize: 50
    volumeType: gp3 
    amiFamily: AmazonLinux2
    labels:
      nodegroup-type: "${public_mgmd_node}"
    ssh: 
      publicKeyPath: "${publicKeyPath}"
    iam:
      attachPolicyARNs:
      withAddonPolicies:
        autoScaler: true
        cloudWatch: true
        ebs: true
        fsx: true
        efs: true
        
  - name: managed-ng-private-01
    instanceType: ${instance_type}
    subnets:
      - ${PrivateSubnet01}
      - ${PrivateSubnet02}
      - ${PrivateSubnet03}
    desiredCapacity: 3
    privateNetworking: true
    minSize: 3
    maxSize: 9
    volumeSize: 50
    volumeType: gp3 
    amiFamily: AmazonLinux2
    labels:
      nodegroup-type: "${private_mgmd_node}"
    ssh: 
      publicKeyPath: "${publicKeyPath}"
    iam:
      attachPolicyARNs:
      withAddonPolicies:
        autoScaler: true
        cloudWatch: true
        ebs: true
        fsx: true
        efs: true
        
cloudWatch:
    clusterLogging:
        enableTypes: ["api", "audit", "authenticator", "controllerManager", "scheduler"]
EOF

```

7\. eksctl을 통해 EKS Cluster를 생성합니다. 생성 yaml file은 이전에 git으로 clone한 [repository](https://github.com/madebybk/eksapp)에 확인해 볼 수 있습니다. Cluster 생성은 20분 정도 소요될 수 있습니다.

```
# eksctl로 cluster 만들기 
eksctl create cluster --config-file=/home/ec2-user/environment/eksapp/eksapp-cluster.yaml
 
```

8\. 정상적으로 Cluster가 생성되었는지 확인합니다.

```
kubectl get nodes

```

정상적 출력 예시:

```
Administrator:~/environment $ kubectl get nodes
NAME                                              STATUS   ROLES    AGE   VERSION
ip-10-11-10-216.ap-northeast-2.compute.internal   Ready    <none>   3d    v1.21.12-eks-5308cf7
ip-10-11-23-60.ap-northeast-2.compute.internal    Ready    <none>   3d    v1.21.12-eks-5308cf7
ip-10-11-34-110.ap-northeast-2.compute.internal   Ready    <none>   3d    v1.21.12-eks-5308cf7
ip-10-11-59-164.ap-northeast-2.compute.internal   Ready    <none>   3d    v1.21.12-eks-5308cf7
ip-10-11-67-7.ap-northeast-2.compute.internal     Ready    <none>   3d    v1.21.12-eks-5308cf7
ip-10-11-92-58.ap-northeast-2.compute.internal    Ready    <none>   3d    v1.21.12-eks-5308cf7
```

9\. AWS Console에서도 생성된 리소스들을 확인해 봅니다.

* 네트워크(VPC, Subnet, Internet Gateway, NAT Gateway, Route Table 등)
* 컴퓨팅 자원(EC2 Worker Node)
* CloudFormation
