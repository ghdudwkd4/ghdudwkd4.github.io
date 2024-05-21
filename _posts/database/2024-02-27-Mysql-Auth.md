---
title: Mysql 권한 처리
layout: post
categories: database
order: 2
data: 2024-02-27 15:20
---

# MySql 사용자 권한 처리 방법

* 사용자 생성
* 권한 부여
* 기타

```
1. 사용자 생성
CREATE USER '새로운사용자'@'호스트' IDENTIFIED BY '비밀번호';
    호스트
        - 'localhost' : 내부접근만 가능
        - '%' : 외부접근 허용

2. 권한 부여
GRANT 권한1, 권한2 ON 데이터베이스이름.* TO '사용자이름'@'호스트';
    권한
        - SELECT , INSERT 등등 여러개 가능
        - 데이터베이스이름
            - TEST.* : 해당 데이터베이스 전체
            - TEST.TEST_TABLE : 특정 테이블에만 추가

3. 권한 부여까지 작업이 되었으면 영구적으로 반영하기 위해 아래 SQL 작성.
FLUSH PRIVILEGES;
```

```
기타
일정 시간 동안 연속된 연결 실패로 인해
해당 호스트의 접속을 일시적으로 차단시 해결 방안
1. error 로그 확인 - 'block' 이란 단어로 찾으면 됨
2. 차단이 확인 되면 root 계정으로 접속 후 아래 쿼리 실행
    UNBLOCK_USER '차단된_IP_주소';

별개로 현재 최대 에러 접속 제한수를 확인하려면 아래 쿼리 실행
SHOW VARIABLES LIKE '%error%'; -- max_connect_errors 확인 
```
