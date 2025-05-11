## Portainer

### 이미지 다운로드

```
docker pull portainer/portainer-ce
```

### portainer_data를 공유하는 volume 생성

```
docker volume create portainer_data
```

### 컨테이너 실행

```
docker run -d -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data --restart=always portainer/portainer-ce
```

컨테이너 확인

```
docker ps -a

CONTAINER ID   IMAGE                             COMMAND                  CREATED             STATUS                           PORTS                                        NAMES
598aaaa07513   portainer/portainer-ce            "/portainer"             26 seconds ago      Up 26 seconds                    8000/tcp, 9443/tcp, 0.0.0.0:9000->9000/tcp   elated_villani
```
