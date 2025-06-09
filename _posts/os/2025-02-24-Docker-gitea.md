---
title: 기존 사용중인 gitea docker 로 옮기기 
layout: post
categories: os
order: 13
data: 2025-02-24 18:00
---

# Docker 에 gitea 설치

### docker 가 없는 분들은 기존 <a href="/2024-08/Docker-Download">Docker 설치</a> 부터 보시면 좋습니다.

docker compose 를 같이 쓸거라 compose 도 같이 설치 <br />
아래 처럼 docker 관련 검색 compose 설치
```
# yum list installed | grep docker
# yum install docker-compose-plugin.x86_64
```
필자는 /home/docker_compose_data 경로로 docker compose 에 관련 된 내용을 내부적으로 저장 하려고 폴더를 만들었다. <br />
하위에 docker-compose.yml 파일 생성 및 아래 shell 내용 작성

```
# 컨테이너 내용을 호스트에서 관리하기 위해 생성
mkdir -p /home/docker_compose_data/gitea/{data,config,logs}

# docker-compose 파일 생성
vi /home/docker_compose_data/docker-compose.yml
```

아래는 docker-compose.yml 파일 내용
```
version: "3"                    # docker 버전을 명시
services:                       # service group
  gitea:                        # service
    image: gitea/gitea:latest   # 최신버전 gitea 이미지 설치
    container_name: gitea
    restart: always
    environment:
      - USER_UID=1000
      - USER_GID=1000
      - GITEA__APP_NAME="My Gitea"
      - GITEA__server__ROOT_URL=http://user-server-ip:13297/  # 필자는 13297 port 를 사용해야 되서 명시
      - GITEA__database__DB_TYPE=sqlite3                # db sqlite3 사용
    ports:
      - "13297:13297"  # Gitea 웹 UI (13297 -> 13297)    # {호스트 접근 port}:{컨테이너 접근 port}
      - "2222:22"     # Gitea SSH 접속
    volumes:
      - /home/docker_compose_data/gitea/data:/data      # {호스트 접근 경로}:{컨테이너 접근 경로}
      - /home/docker_compose_data/gitea/config:/config
      - /home/docker_compose_data/gitea/logs:/logs
```

작성 후 해당 경로에 위치해 아래와 같이 명령어를 실행

```
cd /home/docker_compose_data

# 버전에 따라 docker-compose 하이픈이 안붙을 수 도 있다.
# -d 옵션은 백그라운드로 실행
docker-compose up -d
```
이제 해당 port 로 웹에서 접근 시 gitea 설정 화면이 나오는데 <br />
우린 기존에 있던 걸 쓸거라 다음 단계로 넘어간다.<br />

이제 기존 서버에서 gitea 레포지토리 가져오기

```
# scp 방법도 있지만 압축 파일 다운로드 방식으로 진행
# /data : 이전 서버 gitea data 가 있는 위치
tar -czvf data.tar.gz /data

# 옮길 서버에 FTP 로 "/home/docker_compose_data/gitea" 경로에 전송 후 압축 해제
tar -xzvf data.tar.gz

# docker gitea 재시작
docker-compose down
docker-compose up -d
```
이제 접속을 해보면 기존에 있던 데이터가 옮겨진걸 확인 할 수 있다.