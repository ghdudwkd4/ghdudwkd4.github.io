---
title: linux 메모리 체크 및 정리
layout: post
categories: os
order: 16
data: 2026-01-28 15:00
---

# linux 메모리 사용량 잡기

### 시스템 점검 이유
- 개발서버 속도 저하
- 잦은 서버 중단

### 모니터링 툴을 활용한 시스템 점검

이전 글에서 <a href="/2026-01/netdata" target="_blank">Netdata</a> 를 소개했는데,
이를 활용해 어떤 프로세서들이 많은 메모리를 사용하는지 체크하려고 합니다.

<br>
netdata 의 메인보드 우측을 보면 메뉴들이 있는데, 아래 세 가지가 중요합니다

<a href="/assets/img/os/netdata/netdata_3.jpg" target="_blank">
    <img src="/assets/img/os/netdata/netdata_3.jpg" />
</a>

- System -> Memory -> RAM <br>
- System -> Memory -> Activity <br>
- System -> Memory -> Swap <br>

여기서도 아래 committed 가 중요한데, 물리적인 RAM 크기보다 더 사용중이라면 무조건 정리가 필요합니다.<br>
- System -> Memory -> Activity -> committed <br>

저는 이미 물리적인 크기를 넘어서, swap 도 꽉 차있던 상태였습니다. <br><br>
메인 상단 metrics 우측에 top 을 확인하여 어떤 프로세스가 memory 를 많이 사용하는지 확인합니다. <br>

<a href="/assets/img/os/netdata/netdata_4.jpg" target="_blank">
    <img src="/assets/img/os/netdata/netdata_4.jpg" />
</a>

확인 시 java 가 압도적으로 많았다.<br>
현재 개발 서버에서 구동중인 app 은 10개가 넘는다.<br>
각 서비스별로 was 가 각각 다른데, <br>
아래 명령어로 메모리를 확인한다.

```
# 1. 먼저 실행 중인 Java 프로세스 목록과 PID 확인
# 명령어로 Xms, Xmx 를 확인 할 수 있는데, 만약 없다면 PID 로 더 자세히 확인
jps -v

# 2. 특정 PID의 메모리 설정값(MaxHeapSize 등)만 확인
# 예: PID가 1234일 때
jinfo -flags 1234
``` 

확인 시 8GB 로 잡혀있었다.. app 이 10개가 넘는데, 못해도 80GB 는 필요했다. <br>
하지만 모든 서비스가 이렇게 많을 필요가 없어서 전체적으로 사이즈를 줄였다. <br>
catalina, profile, setenv.sh 등 방법이 있지만 setenv.sh 설정 파일을 만들어서 사용했다. <br>

```
# WAS의 bin 폴더 이동 후 setenv 파일 생성
vi setenv.sh

# 아래 내용 작성 후 저장
#!/bin/bash
export JAVA_OPTS="-Xms512m -Xmx768m -Xss512k -XX:MaxMetaspaceSize=256m"
```

`chmod +x setenv.sh` 명령어로 권한부여(중요!) <br>
이제 재부팅 후 모니터링 했을 때 메모리 사용량이 내려가는걸 볼 수 있다. <br>
모든 was 를 변경 하니 90% 에서 70% 로 줄었다. <br>