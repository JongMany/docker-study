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

---

## Ubuntu

```
docker run -it --name=sys-container-2 ubuntu:16.04 bash
```

#### OS 버전 확인

```
cat /etc/os-release
```

```
NAME="Ubuntu"
VERSION="16.04.7 LTS (Xenial Xerus)"
ID=ubuntu
ID_LIKE=debian
PRETTY_NAME="Ubuntu 16.04.7 LTS"
VERSION_ID="16.04"
HOME_URL="http://www.ubuntu.com/"
SUPPORT_URL="http://help.ubuntu.com/"
BUG_REPORT_URL="http://bugs.launchpad.net/ubuntu/"
VERSION_CODENAME=xenial
UBUNTU_CODENAME=xenial
```

#### 필요한 툴 설치하기

```
apt update

# ifconfig 설치
apt -y install net-tools
```

#### 확인해보기

```
ifconfig
```

#### 결과

```
eth0      Link encap:Ethernet  HWaddr 02:42:ac:11:00:02
          inet addr:172.17.0.2  Bcast:172.17.255.255  Mask:255.255.0.0
          UP BROADCAST RUNNING MULTICAST  MTU:65535  Metric:1
          RX packets:1360 errors:0 dropped:0 overruns:0 frame:0
          TX packets:671 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:16198336 (16.1 MB)  TX bytes:47918 (47.9 KB)

lo        Link encap:Local Loopback
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)
```

ip주소는 addr:172.17.0.2이다.  
도커는 docker0라는 인터페이스를 갖고 있는데, ip는 172.17.0.1이다. 이 ip를 게이트웨이로 삼아 시퀀스하게 ip를 배정한다.

#### docker ip 확인방법

```
docker network inspect bridge
```

#### 결과

```
[
    {
        "Name": "bridge",
        "Id": "d0510d3516851383786b0800f5de7188cc71d98fdac3a109e766e100faa2947e",
        "Created": "2025-05-08T22:13:58.045440209Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.17.0.0/16",
                    "Gateway": "172.17.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "2deb8a8928259f915a2a1ba313362cc1bdf3f5d2e9220f931f77cad496237c30": {
                "Name": "sys-container-2",
                "EndpointID": "6609bd812d18fb7c9716e19c74d963d62f1bab3fe4f220b84e0face41b2b3b65",
                "MacAddress": "02:42:ac:11:00:02",
                "IPv4Address": "172.17.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {
            "com.docker.network.bridge.default_bridge": "true",
            "com.docker.network.bridge.enable_icc": "true",
            "com.docker.network.bridge.enable_ip_masquerade": "true",
            "com.docker.network.bridge.host_binding_ipv4": "0.0.0.0",
            "com.docker.network.bridge.name": "docker0",
            "com.docker.network.driver.mtu": "65535"
        },
        "Labels": {}
    }
]
```
