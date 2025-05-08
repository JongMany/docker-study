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

#### Grep 패턴으로 컨테이너 외부에서 ip 주소 확인해보기

```
docker inspect sys-container-1 | grep -i ipaddress
```

```
"SecondaryIPAddresses": null,
"IPAddress": "172.17.0.2",
        "IPAddress": "172.17.0.2",
```

---

## Nginx image를 통해 구현된 컨테이너

- Nginx은 WebServer 용도로 많이 사용
- Nginx는 Reverse proxy 구현 가능
- Nginx는 API 트래픽 처리가 가능한 고급 http 기능 보유, API Gateway 구현 가능

#### 이미지 Pull

```
docker pull nginx:1.25.0-alpine
```

이미지는 Dockerfile로 만들게 된다.

#### 이미지 히스토리 확인

```
docker image history nginx:1.25.0-alpine
```

```
IMAGE          CREATED         CREATED BY                                       SIZE      COMMENT
2e776a66a355   23 months ago   /bin/sh -c set -x     && apkArch="$(cat /etc…   30.9MB
<missing>      23 months ago   /bin/sh -c #(nop)  ENV NJS_VERSION=0.7.12        0B
<missing>      23 months ago   /bin/sh -c #(nop)  CMD ["nginx" "-g" "daemon…   0B
<missing>      23 months ago   /bin/sh -c #(nop)  STOPSIGNAL SIGQUIT            0B
<missing>      23 months ago   /bin/sh -c #(nop)  EXPOSE 80                     0B
<missing>      23 months ago   /bin/sh -c #(nop)  ENTRYPOINT ["/docker-entr…   0B
<missing>      23 months ago   /bin/sh -c #(nop) COPY file:e57eef017a414ca7…   16.4kB
<missing>      23 months ago   /bin/sh -c #(nop) COPY file:36429cfeeb299f99…   12.3kB
<missing>      23 months ago   /bin/sh -c #(nop) COPY file:5c18272734349488…   12.3kB
<missing>      23 months ago   /bin/sh -c #(nop) COPY file:7b307b62e82255f0…   8.19kB
<missing>      23 months ago   /bin/sh -c set -x     && addgroup -g 101 -S …   7.12MB
<missing>      23 months ago   /bin/sh -c #(nop)  ENV PKG_RELEASE=1             0B
<missing>      23 months ago   /bin/sh -c #(nop)  ENV NGINX_VERSION=1.25.0      0B
<missing>      2 years ago     /bin/sh -c #(nop)  LABEL maintainer=NGINX Do…   0B
<missing>      2 years ago     /bin/sh -c #(nop)  CMD ["/bin/sh"]               0B
<missing>      2 years ago     /bin/sh -c #(nop) ADD file:e51d4089e73ad6dee…   8.14MB
```

EXPOSE 80 = 가상의 격리 공간에서 80번을 방화벽에서 오픈했다는 뜻 = 80번 포트로만 패킷을 전달할 수 있다.

#### 실행

```
docker run -d -p 8001:80 --name=webserver1 nginx:1.25.0-alpine

e412942e271421e100889f28bf5bfe77700408e0e36dd23938ecfadc3fa941d4
```

```
docker ps -a
CONTAINER ID   IMAGE                             COMMAND                   CREATED          STATUS                        PORTS                  NAMES
e412942e2714   nginx:1.25.0-alpine               "/docker-entrypoint.…"   37 seconds ago   Up 36 seconds                 0.0.0.0:8001->80/tcp   webserver1
```

- 0.0.0.0:8081->80/tcp: 퍼블릭(모든 ip)에서 들어오는 8001번으로 패킷을 전달하면 컨테이너 내부의 80번에 들어오게 된다.

##### 포트만 따로 확인하는 방법

```
docker port webserver1
80/tcp -> 0.0.0.0:8001
```

#### nginx가 제대로 동작되는지 확인해보기

```
curl localhost:8001

<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

#### Port 활성화 여부 확인

```
sudo lsof -i :8001

COMMAND     PID   USER   FD   TYPE             DEVICE SIZE/OFF NODE NAME
com.docke 95984 devlee   53u  IPv6 0x9a80b8db06d5179c      0t0  TCP *:vcom-tunnel (LISTEN)
```

```
ps -ef | grep 95984

  501 95984 95977   0  7:13AM ??         0:30.19 /Applications/Docker.app/Contents/MacOS/com.docker.backend run
  501 95994 95984   0  7:13AM ??         0:00.02 com.docker.dev-envs -watchdog
  501 95996 95984   0  7:13AM ??         0:00.28 com.docker.build
  501 95997 95984   0  7:13AM ??         0:08.82 /Applications/Docker.app/Contents/MacOS/Docker Desktop.app/Contents/MacOS/Docker Desktop --reason=open-tray --analytics-enabled=true --name=dashboard
  501 96003 95984   0  7:13AM ??         0:00.53 /Applications/Docker.app/Contents/MacOS/com.docker.virtualization --kernel /Applications/Docker.app/Contents/Resources/linuxkit/kernel --cmdline init=/init loglevel=1 root=/dev/vdb rootfstype=erofs ro vsyscall=emulate panic=0 eth0.dhcp eth1.dhcp linuxkit.unified_cgroup_hierarchy=1 console=hvc0   virtio_net.disable_csum=1 vpnkit.connect=connect://2/1999 --boot /Applications/Docker.app/Contents/Resources/linuxkit/boot.img --disk /Users/devlee/Library/Containers/com.docker.docker/Data/vms/0/data/Docker.raw --networkType gvisor --macaddr-filename-prefix /Users/devlee/Library/Containers/com.docker.docker/Data/vms/0/macaddr --cpus 10 --memoryMiB 16384 --console-log /Users/devlee/Library/Containers/com.docker.docker/Data/log/vm/console.log --console /Users/devlee/Library/Containers/com.docker.docker/Data/vms/0/console.sock --api /Users/devlee/Library/Containers/com.docker.docker/Data/virtualization.sock --watchdog --virtiofs /Users --virtiofs /Volumes --virtiofs /private --virtiofs /tmp --virtiofs /var/folders --rosetta
  501 17150 96982   0  8:01AM ttys009    0:00.00 grep --color=auto --exclude-dir=.bzr --exclude-dir=CVS --exclude-dir=.git --exclude-dir=.hg --exclude-dir=.svn --exclude-dir=.idea --exclude-dir=.tox --exclude-dir=.venv --exclude-dir=venv 95984
```

Docker Proxy는 NAT와 NAPT 기술을 갖고 있다.
