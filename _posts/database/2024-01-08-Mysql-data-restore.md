---
title: Mysql 데이터 복구
layout: post
categories: database
order: 3
data: 2024-05-09 10:50
---

# Mysql 데이터 복구 하는법
1. [기본 작업](#nomal)
2. [테이블도 없고 스키마도 모르고 ibd 파일만 있을 시](#special)

Mysql 환경 
- mysql  Ver 8.0.22 for Linux on x86_64 (MySQL Community Server - GPL)
- InnoDB ( 8버전부터는 거의다 InnoDB 일것이다. )

기존에는 (8 버전 이하) 테이블 스키마가 저장된 파일(.frm) 데이터가 저장된 파일(.ibd) 등 여러개로 나눠져있었으나<br>
8 버전부터는 .ibd 파일 하나로 합쳐졌다.

※ 모든 작업을 하기에 앞서 데이터 백업은 필수.<br>
※ (백업 .ibd) 혹은 (데이터가 있다고 생각되는 .ibd) 파일이 있어야 한다.<br>
※ 작업 하기에 앞서 알아야 할 점.<br>

MySQL은 기본적으로 테이블의 무결성 체크를 해준다.<br>
때문에 Mysql 이 복구하려는 테이블을 관리하고 있다면<br>
.ibd 파일을 통해 데이터를 복구할 수 없다.<br><br>

MySQL은 tablespace라는 물리적인 공간에서 테이블을 관리하고 있다.<br>
다행히도 Mysql 에서는 tablespace 에서 일시적으로 테이블을 관리하지 않도록 수정할 수 있다.<br>

<h2 id="nomal">작업 방법</h2>
Ⅰ. 서버에서 root 계정으로 Mysql 접속 후<br>
Mysql 이 tablespace 에서 관리하지 않도록 변경

```
# mysql -u root -p

# use {데이터베이스};

tablespace 에서 관리하지 않도록 변경.
참고. 변경 시 .ibd 파일은 삭제됨!
# ALTER TABLE {테이블명} DISCARD TABLESPACE;

# exit
```

<br>
Ⅱ. 백업 .idb 파일을 현재 운영중인 데이터베이스 폴더안에 복사한다.

```
경로는 각자 맞는 경로로 수정.
p 옵션은 모드,소유권,권한도 같이 복사. 필자는 권한 소유권 등.
만약 root 소유권에 읽고 쓰는 권한도 없다면 변경해줘야한다.
 
# cp -p {테이블명}.ibd /var/lib/mysql/{데이터베이스}
```

<br>
Ⅲ. tablespace 에서 다시 관리하도록 변경

```
# mysql -u root -p

# use {데이터베이스};

# ALTER TABLE {테이블명} IMPORT TABLESPACE;

조회 한번 해보면 정상적으로 조회 될것이다.
```

<hr>
<h2 id="special">만약 운영중인 데이터베이스에 테이블이 없고 스키마도 모를경우.</h2>
mysql 8 버전부터는 ibd2sdi 라는 명령어가 생겼다. (8버전부터인지는 불확실..)<br>
자세한 내용은 [Mysql 공홈에 레퍼런스](https://dev.mysql.com/doc/refman/8.0/en/ibd2sdi.html) 를 참고.<br>

Ⅰ. *.ibd 파일에서 스키마 정보를 취득.

```
ibd2sdi {테이블명}.ibd > {테이블명}.json
vi {테이블명}.json
```

<br>
Ⅱ. 취득한 스키마로 테이블 생성 후 [기본 작업](#nomal) 순서를 그대로 진행하면 된다.<br><br>

※ 단 테이블 크기가 조금 크면<br>
Lost connection to MySQL server during query 에러가 발생한다고 하는데<br>

필자도 에러가 발생했지만 테이블 생성할때 인코딩값이 잘못되어서 문제였다..
