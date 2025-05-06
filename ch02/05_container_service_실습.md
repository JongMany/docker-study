`docker pull centos:7`
`docker pull ubuntu:16.04`

- 이미지는 레이어 구조이다.

### 컨테이너 실행 (이미지 기반)

`docker  run -it --name=sys-container-1 centos:7 echo 'welcome fc!'`

### 실행 중인 컨테이너 확인

`docker ps -a`

### 컨테이너 삭제

`docker rm sys-container-1`

### 유저 정보 확인하기

`docker info | grep Username`

---

## centOS

#### centos:7을 bash 쉘로 진입  
`docker  run -it --name=sys-container-1 centos:7 bash`

`cat /etc/os-release`

#### 결과

```
NAME="CentOS Linux"
VERSION="7 (AltArch)"
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="7"
PRETTY_NAME="CentOS Linux 7 (AltArch)"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:centos:centos:7:server"
HOME_URL="https://www.centos.org/"
BUG_REPORT_URL="https://bugs.centos.org/"

CENTOS_MANTISBT_PROJECT="CentOS-7"
CENTOS_MANTISBT_PROJECT_VERSION="7"
REDHAT_SUPPORT_PRODUCT="centos"
REDHAT_SUPPORT_PRODUCT_VERSION="7"
```

exit 명령어로 빠져나올 수 있음 -> exit은 종료를 시켜버림

#### EXIT(0) 상태에서 다시 실행
```
docker start sys-container-1
docker exec -it sys-container-1 bash
```

exit말고 ctrl p + q를 쓰면 stop 상태가 되지 않고 나가진다.