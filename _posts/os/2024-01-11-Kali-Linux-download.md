---
title: Kali Linux - download and setting
layout: post
categories: os
order: 4
data: 2024-01-10 15:45
---

# Kali Linux

CentOs 설치 후 여러 작업들을 해보면서 보안쪽에도<br >
관심이 생겨 찾아보다가 칼리 리눅스라는걸 알게되었다.<br >
모의 침투 테스트도 해볼 수 있어 너무나도 흥미가 생겼다.<br >
알게 된 계기는 이만 하고 칼리 리눅스에 대해 알아보겠습니다.<br ><br >

Kali Linux 는 고급 침투 테스트 및 보안 감사를 목표로 하는<br >
오픈 소스 Debian 기반 Linux 배포판이다. <br >
이는 사용자가 주변 활동이 아닌 완료해야 하는<br >
작업에 집중할 수 있도록 하는 일반적인 도구, <br >
구성 및 자동화를 제공함으로써 이를 수행한다.
라고 [공식홈페이지](https://www.kali.org/docs/introduction/what-is-kali-linux/){:target="_blank"} 에 나와있습니다.

기능으로는 600개 이상 침투 테스트 도구가 포함되어있다고 합니다. <br >
오늘은 칼리 리눅스를 다운받고 실행해 보는것으로 마치고 다음에 실제 침투 테스트를 해보겠습니다. <br >

설치는 가상머신을 설치하고 거기에 칼리 리눅스를 설치해보겠습니다. <br >
가상머신이 없을 시 이전 [포스팅](/2024-01/VirtualBox-download)을 보고 오시길바랍니다.

[Kali-Linux VirtualBox](https://www.kali.org/get-kali/#kali-virtual-machines){:target="_blank"}
가상머신 이미지를 다운로드<br >
압축 해제 후 해제한 위치 기억후<br ><br >


VirtualBox 열어서 [추가] 버튼 클릭<br >
<p style="text-align: center;"><img src="/assets/img/os/kaliLinux/kaliLinux_1-1.jpg" alt="Kali-Linux 추가-1" /></p>

<p style="text-align: center;">▼</p>

아까 기억 한 압축파일 해제 한 곳의 [*.vbox] 파일 열기<br >
필자는 실행한적이 있어 Log 도 있구 여러 파일이 있지만 필수로 있어야 하는건<br >
1 , 2 번 표시 된 [ vdi , vbox ] 파일이다.
<p style="text-align: center;"><img src="/assets/img/os/kaliLinux/kaliLinux_1-2.jpg" alt="Kali-Linux 추가-2" /></p>

<p style="text-align: center;">▼</p>

완료가 되면 [설정] 클릭
<p style="text-align: center;"><img src="/assets/img/os/kaliLinux/kaliLinux_1-3.jpg" alt="Kali-Linux 추가-3" /></p>

<p style="text-align: center;">▼</p>

연결 된 후 설정 탭에서 [시스템] -> 메모리 4096 MB 로 변경 <br >
<p style="text-align: center;"><img src="/assets/img/os/kaliLinux/kaliLinux_1-4.jpg" alt="Kali-Linux 추가-4" /></p>

<p style="text-align: center;">▼</p>

[네트워크] 에서 어댑터1 활성화. 어댑터2 활성화 및 호스트 전용 어댑터로 변경<br >
<p style="text-align: center;"><img src="/assets/img/os/kaliLinux/kaliLinux_1-5.jpg" alt="Kali-Linux 추가-5" /></p>

<p style="text-align: center;">▼</p>

<p style="text-align: center;"><img src="/assets/img/os/kaliLinux/kaliLinux_1-6.jpg" alt="Kali-Linux 추가-6" /></p>

<br ><br >

[시작]
<p style="text-align: center;"><img src="/assets/img/os/kaliLinux/kaliLinux_1-7.jpg" alt="Kali-Linux 추가-7" /></p>

<p style="text-align: center;">▼</p>

시작화면
<p style="text-align: center;"><img src="/assets/img/os/kaliLinux/kaliLinux-start-1.jpg" alt="Kali-Linux start-1" /></p>

<p style="text-align: center;">▼</p>

<p style="text-align: center;"><img src="/assets/img/os/kaliLinux/kaliLinux-start-2.jpg" alt="Kali-Linux start-2" /></p>

<p style="text-align: center;">▼</p>

로그인 정보 ID / PW는 kali / kali (kaliLinux 7.* 대 기준)
<p style="text-align: center;"><img src="/assets/img/os/kaliLinux/kaliLinux-start-3.jpg" alt="Kali-Linux start-3" /></p>

<p style="text-align: center;">▼</p>

로그인 후 상단에 보면 터미널 클릭 후 아래 명령어들을 입력
<p style="text-align: center;"><img src="/assets/img/os/kaliLinux/kaliLinux-start-4.jpg" alt="Kali-Linux start-4" /></p>

<p style="text-align: center;">▼</p>

<p style="text-align: center;"><img src="/assets/img/os/kaliLinux/kaliLinux-start-5.jpg" alt="Kali-Linux start-5" /></p>

<p style="text-align: center;">▼</p>


```
# 시스템 업데이트
sudo apt-get update

# 버추얼박스 게스트 에디션 설치
sudo apt install virtualbox-guest-x11


# 네트워크 카드 자동 인식과 IP 자동할당 설정 (아래 내용 신규 추가)
sudo vi /etc/network/interfaces
---------------------
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet dhcp
---------------------

# 네트워크 변경 적용
systemctl restart networking


# 한글 폰트 추가
sudo apt-get install -y fonts-nanum

# 폰트 정보 캐시파일 설정
fc-cache -f -v


# Terminator 설치 (조금더 편리한 Terminal Emulator Tool)
sudo apt-get install -y terminator
```
    
