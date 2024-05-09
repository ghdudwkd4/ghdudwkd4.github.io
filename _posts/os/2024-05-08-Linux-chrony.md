---
title: Linux 한국 서울 시간으로 맞추는 법
layout: post
categories: os
order: 7
data: 2024-05-08 10:00
---

# Linux chrony

Redhat 계열의 Linux를 사용하고 있다면 chrony 패키지가 디폴트로 설치되어 있다.<br>
chrony 데몬 상태 확인 후 비활성화 되어 있다면 활성화 후 3번을 바로 보면된다.<br><br>

## 1. 먼저 chrony 를 설치 한다.
```
# dnf install -y chrony
```

## 2. 활성화 및 서비스 시작
```
# systemctl enable chronyd
# systemctl start chronyd
# systemctl status chronyd
```

## 3. 시간 동기화 받아올 서버 지정
```
# vi /etc/chrony.conf

Line 3 에 pool 2.centos.pool.ntp.org iburst 주석 처리
         #pool 2.centos.pool.ntp.org iburst 

아래 내용 삽입 후 저장
# server time.bora.net iburst

서비스 재시작
# systemctl restart chronyd

동기화 서버 상태 확인
# chronyc sourcestats -v

시간 동기화 확인
# chronyc sources -v

동기화 잘 되고 있는지 확인
# chronyc tracking

지정한 서버와 동기화가 되지 않았을 시
# chronyc -a makestep
```