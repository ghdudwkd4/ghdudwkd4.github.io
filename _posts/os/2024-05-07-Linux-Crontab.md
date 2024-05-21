---
title: Linux 스케줄러 CronTab
layout: post
categories: os
order: 6
data: 2024-05-07 15:40
---

# Linux CronTab 사용법

## Cron, Crontab 이란

<p>
    Cron : 일반적으로 스케줄 서비스의 이름이며,<br >
           소프트웨어 데몬으로 유닉스 계열에서<br >
           시간 기반 작업 스케줄로 소프트웨어 환경을 설정 및 관리하고<br >
           관리자가 원하는 시간, 날짜에 주기적으로 실행할 수 있도록 해준다.<br >
</p>
<p>
    Crontab : crontab은 작업 목록 및 cron 데몬에 대한 명령어들이 보관된 위치에 저장되어 있다.
</p>
<br >

```
# ┌───────────── min (0 - 59)
 # │ ┌────────────── hour (0 - 23)
 # │ │ ┌─────────────── day of month (1 - 31)
 # │ │ │ ┌──────────────── month (1 - 12)
 # │ │ │ │ ┌───────────────── day of week (0 - 6) (0 to 6 are Sunday to Saturday, or use names; 7 is Sunday, the same as 0)
 # │ │ │ │ │
 # │ │ │ │ │
 # * * * * *  command to execute
```

<p> < Crontab 스케줄 구조 > </p> <br>

#### crontab 시간 적용방법

|분(0-59)|시(0-23)|일(1-31)|월(1-12)|요일(0-7)|쉘경로|
|:---:|:---:|:---:|:---:|:---:|:---:|
|1  |11 |3  |*  |*  |shell_path|

<br>


### ※ 주의사항
명령어 작성법과, cronTab 설정파일 위치에 있는 설정 폴더가 따로 있다. <br/>
두가지 방법을 적절히 사용하되 명령어에 없다고 스케줄러가 없는건 아니다. <br/>

1. crontab 명령어로 작업
    1. crontab -l : crontab 목록 나열 <br >
    2. crontab -e : crontab 수정 <br >
    3. crontab -r : crontab 삭제 <br >
    4. 자세한 옵션을 확인하려면 info crontab 으로 확인바랍니다.<br ><br >
        2번을 실행 시 (vi) 화면이 열림<br >
        아래와 같이 해당하는 시간에 맞춰 작성한다.<br ><br >
        ex) 1. 0 2 * * * /쉘파일 위치/backup.sh<br >
               매일 새벽 두시에 쉘파일 실행<br ><br >
        ex) 2. 45,5 10 * * 1-5 /쉘파일 위치/backup.sh<br >
               월~금요일 매월 매일 10시 5분과 45분에 실행<br ><br >
                
2. crontab 설정파일에서 작업
    1. 위치             : 필자는 /etc 하위에 위치해있다.
    2. /etc/cron.d      : 소프트웨어 패키지를 설치할 때 필요한 주기적 작업을 등록하는 공간으로 사용.
    3. /etc/crontab     : 관리자가 직접 지정한 작업을 설정하며 , 임의의 사용자 권한으로 실행할 수 있다.<br>
                          시스템 관련 작업을 등록하는 곳이다.
    4. /etc/cron.hourly : 시간
    4. /etc/cron.daily  : 일별
    5. /etc/cron.weekly : 주별
    6. /etc/cron.monthly: 월별
    