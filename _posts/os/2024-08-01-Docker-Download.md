---
title: Linux CentOs Docker 설치
layout: post
categories: os
order: 10
data: 2024-08-01 13:10
---

# Docker 설치

### 1-0. docker 이전 버전 삭제

```
sudo remove docker \
            docker-client \
            docker-client-latest \
            docker-common \
            docker-latest \
            docker-latest-logrotate \
            docker-logrotate \
            docker-engine
```

### 1-1. yum-utils 업데이트
기존 yum-utils 최신으로 업데이트

```
sudo yum update
sudo yum install -y yum-utils
```

<br>

### 1-2. docker-ce repo 추가
Docker 를 설치 할 수 있도록 저장소 추가

```
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

<br>

### 1-3. docker 설치
최신 버전의 docker 설치

```
sudo yum install docker-ce docker-ce-cli containerd.io -y
```

<br>

아래 오류 발생 할 수 있음 그럴땐 메세지에 나오듯 명령어 추가

<a target="_blank" href="/assets/img/os/docker/docker-1.PNG"><img src="/assets/img/os/docker/docker-1.PNG" /></a>

<br>

`--allowerasing` 을 사용해 충돌 되는걸 삭제하고 설치했습니다.

<a target="_blank" href="/assets/img/os/docker/docker-2.PNG"><img src="/assets/img/os/docker/docker-2.PNG" /></a>

<br>

이렇게 오류가 생겼을 시 docker 가 제대로 설치 안 된 경우가 있습니다.<br>
아래 명령어를 실행해 다시 한번 체크합니다.<br>

```
sudo yum list installed | grep docker

# 아래 세가지는 꼭 있어야 된다.
# docker-ce / docker-ce-cli / containerd.io
```

<br>

<a target="_blank" href="/assets/img/os/docker/docker-3.PNG"><img src="/assets/img/os/docker/docker-3.PNG" /></a>


### 1-4. docker 시작 및 서비스 등록

```
# 버전확인
docker -v

# 서비스 시작.
sudo systemctl start docker

# 부팅 시 서비스 자동 시작.
sudo systemctl enable docker

# 서비스 상태.
sudo systemctl status docker
```

<br>

### 1-5. docker 상태 확인 및 Root Dir 변경

도커가 설치되어 기본적으로 도커의 컨테이너나 데이터들이 쓰일 디렉토리는 Docker Root Dir 의 값 /var/lib/docker 이다. <br>
보통 OS의 디스크와 DATA의 디스크를 나눠쓰기 때문에 OS 디스크가 아닌 <br>
DATA 디스크에 저장 되게 변경해준다. <br>

아래 명령어를 쳐서 docker 기본 정보를 확인하고, Root Dir 위치를 확인.

```
docker info
```

<br>

<a target="_blank" href="/assets/img/os/docker/docker-4.PNG"><img src="/assets/img/os/docker/docker-4.PNG" /></a>

<br>

이제 Root Dir 경로를 변경하겠습니다. <br>
/home 하위에 만들었습니다. <br>

```
sudo mkdir /home/docker

sudo vi /etc/docker/daemon.json
```

<br>
daemon.json 에 아래와 같이 입력 후 저장.

```
{
        "data-root": "/home/docker/"
}
```

<br>
docker 재부팅

```
systemctl restart docker
```

<br>

이제 아래와 같이 확인해 보면 여러 폴더 및 파일이 생성이 되있는걸 볼 수 있습니다.
<a target="_blank" href="/assets/img/os/docker/docker-5.PNG"><img src="/assets/img/os/docker/docker-5.PNG" /></a>



