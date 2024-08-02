---
title: Docker mariaDB 설치
layout: post
categories: os
order: 12
data: 2024-08-01 16:10
---

# Docker mariaDB 설치

### 1-1. Docker Image 다운로드

```
docker pull mariadb

# 특정 버전 원할 시 검색 후 pull
docker search mariadb

docker search mariadb:10.9
```

<br>


### 1-2. Docker Image 잘 받아졌는지 확인

```
docker images
```

<br>


### 1-3. MariaDB 컨테이너를 실행

```
docker run --name maria-db -p 3307:3306 -e MYSQL_ROOT_PASSWORD={비밀번호} -d mariadb
```

<br>
1. docker run : docker image에서 container를 생성
2. -name maria-db : maria-db라는 이름을 가진 컨테이너를 생성
3. -p 3307:3306 : 해당 컨테이너의 포트의 인바운드 포트를 3307, 아웃바운드를 3306으로 설정
4. -e : 컨테이너 내 변수를 설정
5. MYSQL_ROOT_PASSWORD={비밀번호} : ROOT의 암호를 설정할 수 있음 (ex: MYSQL_ROOT_PASSWORD=1234)
6. -d mariadb : mariadb라는 이미지에서 분리하여 컨테이너를 생성

<br>

### 1-4. 실행중인 도커 컨테이너 확인

```
docker ps
```

<br>
<a target="_blank" href="/assets/img/os/docker/docker-6.PNG"><img src="/assets/img/os/docker/docker-6.PNG" /></a>

<br>
개인 PC 에 도커 설치 후 mariaDB 를 설치했다면 개인 PC 에서 Sql 툴을 열어 접속을 해볼수있고,<br>
직접 이미지에 접속해 확인 할 수 있다.<br>

### 2-1. 도커 이미지에 접속

```
docker exec -it maria-db /bin/bash
```

<br>
<a target="_blank" href="/assets/img/os/docker/docker-7.PNG"><img src="/assets/img/os/docker/docker-7.PNG" /></a>


<br>
현재 시점으로 이미지에 접속 후 mariaDB 설정 파일을 보려고 하니 vi 명령어가 작동하지 않았다.<br>
그리고 centOs 가 아닌 ubuntu 였다.<br>
vi 명령어가 안된다면 없는것이니 설치만 해주면 된다.<br>

```
apt-get update

apt-get install vim
```

<br>
이제 아래처럼 mariadb 에 접속하면 된다!

<br>
<a target="_blank" href="/assets/img/os/docker/docker-8.PNG"><img src="/assets/img/os/docker/docker-8.PNG" /></a>

