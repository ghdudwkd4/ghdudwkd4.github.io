---
title: wsl 다운로드 
layout: post
categories: os
order: 14
data: 2025-06-09 10:00
---

# wsl 설치 및 사용방법

wsl 은 "Windows Subsystem for Linux"의 약자로 <br>
Windows 운영 체제에서 Linux 환경을 실행할 수 있는 기능을 제공하는 기술

먼저 cmd 를 관리자 모드로 실행 후 아래와 같이 입력
```
# wsl --install
```

install 시 계정 정보 입력 하는게 있는데, root 는 기본적으로 있는 계정이라, <br>
사용을 못하고, 보통 본인 컴퓨터 계정을 예시로 세팅 해준다. <br>

설치 후 아래와 같이 입력 해 2 버전인지 확인
```
(-list, -version)
# wsl -l -v
-------------------------------------------
  NAME              STATE           VERSION
* Ubuntu            Running         2
-------------------------------------------
```

wsl 실행 시 ubuntu 를 기본으로 사용하고 싶다면 아래 명령어로 세팅값을 바꾸면 된다.
```
# wsl --set-default Ubuntu
```

이제 wsl 만 입력해도 ubuntu 로 실행된다.