---
title: Docker 명령어 모음
layout: post
categories: os
order: 11
data: 2024-08-01 15:10
---

# Docker 명령어

컨테이너 생성
```
docker create [image]
```

<br>
컨테이너 시작 → [container]에는 container name 또는 id 사용 가능

```
docker start [container]
```

<br>
컨테이너 생성 및 시작

```
docker run [image]
```

<br>
컨테이너 시작

```
$ docker run \                         
   -i \                                # 컨테이너에 키보드 입력이 필요한경우
   -t \                                # 컨테이너에 TTY할당하여 터미널 이용이 필요한 경우
   --rm \                              # 컨테이너 실행 종료후 자동 삭제가 필요할때
   -d \                                # 백그라운드로 실행하고 싶을 때
   --name hello-world \                # 이름을 지정하고 싶을때
   -p 80:80 \                          # 포트 바인딩을 하고 싶을 때
   -v /opt/example:/example \          # 볼륨 바인딩을 하고 싶을 때
   bbangi/hello-world:latest \         # 실행할 이미지는 그냥 적어주기
   my-command                          # 마지막은 컨테이너 내에서 실행할 명령어

-it는 shell을 실행하기 위한 용도로 한쌍으로 생각하는것이 좋다.
```

<br>
실행중인 컨테이너 상태 확인

```
docker ps
```

<br>
전체 컨테이너 상태 확인

```
docker ps -a
```

<br>
컨테이너 상세 정보 확인

```
docker inspect [container]
```

<br>
컨테이너 일시 중지

```
docker pause [container]
```

<br>
컨테이너 재개

```
docker unpause [container]
```

<br>
컨테이너 종료 (SIGTERM 시그널 전달)

```
docker stop [container]
```

<br>
모든 컨테이너 종료

```
docker stop $(docker ps -a -q)
```

<br>
컨테이너 강제 종료 (SIGKILL 시그널 전달)

```
docker kill [container]
```

<br>
이미지 삭제

```
docker rmi [이미지 id]
```

<br>
컨테이너를 삭제하기 전에 이미지를 삭제할 경우

```
docker rmi -f [이미지 id]
```

<br>
컨테이너 삭제 (실행중이지 X)

```
docker rm [container]
```

<br>
컨테이너 실행 종료 후 자동 삭제

```
docker run --rm ...
```

<br>
컨테이너 강제 종료후 삭제 (SIGKILL 시그널 전달)

```
docker rm -f [container]
```

<br>
중지된 모든 컨테이너 삭제

```
docker container prune
```