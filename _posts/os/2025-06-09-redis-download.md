---
title: redis 설치 및 테스트 
layout: post
categories: os
order: 15
data: 2025-06-09 11:00
---

# redis 설치 및 테스트

redis 는 빠른 캐시/세션 저장용 in-memory db 입니다.

※ wsl 환경에서 작업 할거라 docker desktop 있다는 가정하에 작성

docker desktop 실행 후

Setting -> Resources -> WSL integration -> Enable integration with additional distros

Ubuntu 체크

cmd 관리자 권한으로 실행 후 아래와 같이 설정 업데이트
```
# wsl

# sudo apt update && sudo apt upgrade -y
```

docker 동작 확인
```
# docker version
# docker compose version
```
여기서 오류가 나면 위 docker desktop 에서 WSL integration 이 안되어있을 가능성이 높습니다.

redis 말고도 다른것들도 많이 테스트 하기 위해 docker_home 이라는 폴더를 만들어 보기 쉽게 정리했습니다.
```
# sudo mkdir /home/{계정명}/docker_home
# cd /home/{계정명}/docker_home
```

redis 를 docker compose 설치
```
# sudo vi docker-compose.yml
```

작성 한 다음줄의 공백 여부에 따라 명령이 달라지니 공백이 이상하면 정리 필요
```
version: '3.8'

services:
  redis:
    image: redis:7
    container_name: redis
    ports:
      - "6379:6379"
```

실행
```
# docker-compose up -d
```

redis 확인
```
# docker ps -a
# docker exec -it redis redis-cli
```

redis cli 에 들어가면 다음처럼 키-값을 넣고 확인 가능
```
# set testKey hello
# get testKey
```

설치 및 테스트는 끝 다음글에서는 스프링부트에서 연동하여 확인하는 법을 작성하겠습니다.