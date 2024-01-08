---
title: Mysql Backup
layout: post
categories: database
order: 1
data: 2024-01-08 10:09
---

# MySql BackUp Shell 작성법

### 아래 backup.sh 와 같이 쉘을 작성하고 crontab 을 걸어두면 날짜별로 백업 및 일정 기간 뒤의 자료들은 삭제 하게된다. 

#### backup.sh

```
#!/bin/bash
. ~root/.bash_profile
DATE=$(date +%Y%m%d)
USER=user
PASS=1234
DATABASE=TEST

BACKUP_DIR=/home/mysql_backup/
mysqldump -u $USER -p$PASS $DATABASE > $BACKUP_DIR"test_db_backup_"$DATE.sql

find $BACKUP_DIR -ctime +6 -exec rm -f {} \;
```

<br >


1. mysqldump : mysql 백업 명령어<br >
    mysql -u [계정명] -p[패스워드] [백업받을 데이터베이스] > [백업 경로][백업파일 명].sql <br ><br >

2. find : 백업 용 지울때<br >
    find [백업 경로] -ctime [삭제지정 기간] -exec rm -f {} \;

3. crontab : 스케줄러<br >
    crontab -e<br >
    (vi)화면이 열림<br >
    아래와 같이 해당하는 시간에 맞춰 작성한다.<br ><br >
    ex) 0 2 * * * /home/mysql_backup/backup.sh<br >
        매일 새벽 두시에 실행
    
<br>
#### crontab 시간 적용방법

|분(0-59)|시(0-23)|일(1-31)|월(1-12)|요일(0-7)|쉘경로|
|:---:|:---:|:---:|:---:|:---:|:---:|
|1  |11 |3  |*  |*  |shell_path|
