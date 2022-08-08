# Cloud9 에 패키지 설치

## AWS CLI Upgrade

[AWS Command Line Interface ](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-welcome.html)는 command-line shell의 명령을 사용하여 AWS 서비스와 상호 작용할 수 있는 오픈 소스 툴입니다. AWS Cloud9는 기본적으로 AWS CLI가 설치되어 있습니다.

1\. 아래의 명령어를 통해, AWS CLI 버전을 업그레이드합니다.

```
# AWS CLI Upgrade
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
source ~/.bashrc
aws --version
# AWS CLI 자동완성 설치 
which aws_completer
export PATH=/usr/local/bin:$PATH
source ~/.bash_profile
complete -C '/usr/local/bin/aws_completer' aws

```

