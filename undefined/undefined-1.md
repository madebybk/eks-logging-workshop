# 유틸리티 설치

## 기타 유틸리티 설치

1\. GNU gettext, jq, bash-completion 등을 설치 합니다.

```
sudo yum -y install jq gettext bash-completion moreutils
for command in kubectl jq envsubst aws
  do
    which $command &>/dev/null && echo "$command in path" || echo "$command NOT FOUND"
  done

```
