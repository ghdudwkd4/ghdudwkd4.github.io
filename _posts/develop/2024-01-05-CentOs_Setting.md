---
title: CentOs 설치 및 간단한 설정 
layout: post
categories: Develop
order: 1
data: 2024-01-05 13:57
---

순서
 - [0.CentOs 설치](#CoInstall)
 - [1.Apache 및 Apache 에 필요한 파일 설치](#apacheInstall)
 - [2.Java , Tomcat , Tomcat-connector 설치](#JavaTomInstall)
 - [3.Apache Setting](#ApacheSetting)
 - [4.Vi color Setting](#ViColorSetting)
 - [5.Etc](#etc)<br><br>

 
<h2 id="CoInstall">0. CentOs 설치</h2>
1. 언어 한글 변경<br>

2. 시간 아시아/서울 변경<br>

3. [설치 목적지] 선택 설치하려는 디스크 선택
	- 저장소 구정 custom 선택 완료
	- 표준 파티션 변경
	- 자동생성 클릭 및 완료<br>
4. 설치시작<br>

5. root 암호 설정<br>

6. 하단 다운로드 마무리 되면 재부팅 시작
	- 재부팅 시 부팅순서가 USB 일 경우 CentOs 로 변경<br>
	없을 시 OS 들어가는 부팅메뉴가 있을듯<br><br>

7. GUI 환경 일 시 우측 상단 설정에서 네트워크 변경
	- 실제 사용 할 렉 장비쪽 네트워크 안쓰는 IP 확인 후 고정IP 로 변경.
	- 작업이 완료되면 원격접속 가능하게 SSH 부터 변경<br>
	(기본 20 이나 보안상 바꿔주는게 좋다)
	- semanage port -a -t ssh_port_t -p tcp 2022<br>
	( SELINUX 보안상 해당 PORT 만 풀어주기)
	- vi /etc/ssh/sshd_config 파일 PORT 변경
	- firewall-cmd --list-all 확인 후 2022 없을 시 추가
	- firewall-cmd --permanent --zone=public --add-port=2022/tcp<br><br>

8. disk 추가 시
	1. console 실행 후 fdisk -l 로 disk 및 Device 확인
	2. Device 있을 경우 데이터가 남아있는 디스크!  
	(파티션만 나눈 경우 || raid 로 나눈 경우) 초기화 필요
	3. [ mkfs.xfs , mkfs.ext4 (xfs 가 확장성이 높음) ] mkfs.xfs /dev/sd*
	4. 파티션 나누기 fdisk /dev/sd*
	5. 신규 파티션을 xfs 시스템으로 변경 3번과 동일
	6. 마운트 하기 mount /dev/sd**
	7. df -Th 마운트 확인
	8. 재부팅 후에도 마운트 해제 안되도록 설정
		- blkid UUID 확인 및 복사
		- vi /etc/fstab 에 맞춰서 작성

※ 마운트 해제 umount /dev/sd**<br><br><br><br>




<h2 id="apacheInstall">1. Apache 및 Apache 에 필요한 파일 설치</h2>
```
※ Apache는 2.4부터 apr, apr-util이 빠졌다. 별도로 설치
/usr/local/src 위치하기 위해 wget(web get)으로 설치함, dnf 혹은 yum 으로 설치 시 다른 곳에 설치..

※ 설치 전 CenOs 는 일반적인 지원 목록에서 없어져 기존 운영체제에서 새 Stream 배포판으로 이전
sudo sed -i -e "s|mirrorlist=|#mirrorlist=|g" /etc/yum.repos.d/CentOS-*
sudo sed -i -e "s|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g" /etc/yum.repos.d/CentOS-*

dnf update

※ 패키지 설치
dnf install -y gcc gcc-c++ pcre-devel expat-devel
```

```
cd /usr/local/src
```

<h3>1. httpd : 2.4.58</h3>

```
wget https://downloads.apache.org//{버전에 맞는 tar.gz 파일}
tar -xvf apr-{버전에 맞는 tar.gz 파일}
```

<h3>2. apr : 1.7.4</h3>

```
wget https://downloads.apache.org//apr/apr-{버전에 맞는 tar.gz 파일}
tar -xvf apr-{버전에 맞는 tar.gz 파일}
```

<h3>3. apr-util : 1.6.3</h3>

```
wget https://downloads.apache.org//apr/apr-util-{버전에 맞는 tar.gz 파일}
tar -xvf apr-util-{버전에 맞는 tar.gz 파일}
```

<h3>4. 압축해제한 파일들을 apache소스의 srclib안으로 넣어준다.</h3>

```
mv apr-1.7.4 httpd-2.4.58/srclib/apr
mv apr-util-1.6.3 httpd-2.4.58/srclib/apr-util
```

<h3>5. pcre : 8.45</h3>

```
wget https://sourceforge.net/projects/pcre/files/pcre/{버전}/pcre-{버전에 맞는 tar.gz 파일}
tar -xvf pcre-{버전에 맞는 tar.gz 파일}
```

<h3>6. configure </h3>
[configure 컴파일 설치 과정 간단 설명]
1. 설치 파일 디렉터리로 접근합니다.

2. configure
    configure 명령어를 이용해 현재 디렉터리(설치 파일 디렉터리)에서 
    configure라는 이름을 갖는 스크립트 파일을 찾고 이를 실행하여 Makefile을 생성합니다.
    이 과정에서 설치 시 필요한 필요한 도구가 다 있는지, 라이브러리는 다 있는지 같은 것들을 체크해줍니다.
    옵션을 이용하여 설치 경로(--prefix)등을 지정할 수 있습니다.
    configure 단계에서 에러를 내며 멈출 경우 설치를 위한 준비(필수 라이브러리 등)가 잘 되어있는지 다시 확인합니다.
    configure 옵션을 잘 못해서 다시 시작하고 싶을 때 make distclean(초기 압축을 풀었던 상태로 되돌려 줌) 명령어를 입력하면 configure 설정을 모두 제거해줍니다.

3. make
    make 명령어로 생성된 Makefile을 이용하여 프로그램 코드를 컴파일하고 실행 가능한 형태로 만듭니다.
    Setup파일을 만든다고 보면 됩니다.
    이 과정에서 모든 실행파일들은 여러분이 소프트웨어를 압축 해제한 임시 디렉터리에 생성됩니다.
    에러가 날 경우 make clean 명령어를 입력 후 다시 make 명령어를 입력해 컴파일합니다.
    컴파일: 소스파일을 사용자가 실행 가능한 파일로 만들어 주는 과정
    make 유틸리티가 동작하기 위해서는 Makefile이라는 파일이 현재 작업공간에 존재해야 합니다.

4. make install
    make install을 이용해 make를 통해 만들어진 설치 파일(Setup)을 설치를 합니다.
    (위에서 임시 디렉터리에 생성된 실행파일들을 최종 디렉터리에 복사합니다. 이에 따라 사용자가 실행이 가능한 형태가 됩니다.)
<br><br>

<h4>6-1. PCRE 컴파일 설치<br></h4>

```
cd pcre-8.45
./configure --prefix=/usr/local/src
make && make install
```

<h4>6-2. Apache HTTP Server(httpd) 컴파일 설치</h4>

```
cd ../httpd-2.4.58/
./configure --prefix=/usr/local/apache --with-included-apr --with-pcre=/usr/local/src

make && make install
```

<br>
<h3>7. systemctl 에 등록해두기</h3>

```
vi /usr/lib/systemd/system/httpd.service

[Unit]
Description=Apache Service

[Service]
Type=forking
#EnvironmentFile=/usr/local/apache/bin/envvars
PIDFile=/usr/local/apache/logs/httpd.pid
ExecStart=/usr/local/apache/bin/apachectl start
ExecReload=/usr/local/apache/bin/apachectl graceful
ExecStop=/usr/local/apache/bin/apachectl stop
KillSignal=SIGCONT
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

<h3>8. 포트 확인 및 오픈</h3>

```
firewall-cmd --zone=public --list-all
80 없을 시 아래 실행
firewall-cmd --permanent --add-port=80/tcp
firewall-cmd --reload
```

<h3>9. // 서비스 시작</h3>

```
systemctl status httpd
systemctl start httpd
systemctl status httpd
```

※ ip:80 접속 시 It works! 나오면 성공
<br><br><br><br><br>





<h2 id="JavaTomInstall">2. Java , Tomcat , Tomcat-connector 설치</h2>

```
cd /usr/local/
```

<h3>1. Java open jdk 1.8 다운로드 // dnf 명령어로 설치함</h3>

```
1-1. dnf search java (devel 설치 해야 javac 가 있음)
     dnf install -y java-1.8.0-openjdk-devel.x86_64
     which javac (javac 위치 확인 있으면 정상)
1-2. 자바 환경변수 설정
     which javac 로 확인 한 링크의 원본 파일 위치 확인
     readlink -f /usr/bin/javac (혹은 그냥 find / -name java 로 찾아도 됨)
     vi /etc/profile 하단에
     - JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.312.b07-2.el8_5.x86_64
     - PATH=$PATH:$JAVA_HOME/bin
     - export JAVA_HOME PATH
     나와서 source /etc/profile
```

<h3>2. Tomcat // wget 로 설치함</h3>

```
wget https://downloads.apache.org/tomcat/tomcat-9/v9.0.84/bin/apache-tomcat-9.0.84.tar.gz
tar zxvf apache-tomcat-9.0.84.tar.gz
mv apache-tomcat-9.0.84 tomcat9
```

<h3>3. ajp 연동시 필수 Tomcat-connector // wget 로 설치함</h3>

```
wget https://downloads.apache.org/tomcat/tomcat-connectors/jk/tomcat-connectors-1.2.49-src.tar.gz
tar zxvf tomcat-connectors-1.2.49-src.tar.gz
```

<br><br><br><br><br>





<h2 id="ApacheSetting">3. Apache Setting</h2>

```
cd /usr/local/apache/conf
vi httpd-conf
	LISTEN 80 : 포트 추가
	하단에 includ httpd-vhost.conf

!!!!아직 작성중!!!!
- mod_jk 모듈 설치도 필요해 보임
- svn , jenkins 설치에 관한것도 필요해 보임
jenkins 기존 설치 방법대로 설치 후 다운그레이드는 war 파일만 변경

cd extra/

※ ss -atnlp 실행중인 port 확인?


jenkins 설정 파일 위치
cd /usr/lib/systemd/system
```

<br><br><br><br><br>





<h2 id="ViColorSetting">4. Vi color Setting</h2>
<h3>ssh 호스트 , 디렉터리 컬러 변경</h3>

```
vi ~/.bashrc
제일 하단에 작성
export PS1='\033[01;31m[\u\[\033[01;32m\]@\h \[\033[01;34m\w]\[\033[00m\]\$ '
export LS_COLORS="di=00;36"
```

<h3>shell script 컬러 및 옵션 변경</h3>

```
vi ~/.bashrc
alias vi='vim' 작성 후 저장
source ~/.bashrc 적용

vi /home/.vimrc 파일 생성 하단 작성 후 저장
syntax enable
syntax on                               ## 하이라이트
filetype on                             ## 파일종류 자동인식
set autoindent                          ## 자동 들여쓰기
set background=dark                     ## 배경 컬러
set cindent                             ## C언어 자동들여쓰기
set history=100                         ## 명령어 기록
set hlsearch                            ## 검색어 강조
set number                              ## 행 번호 표시
set paste!                              ## 계단현상 제거 (붙여넣기)
set shiftwidth=4                        ## 들여쓰기 설정
set showmatch                           ## 괄호의 짝? 을 표시해줌
set statusline=%h%F%m%r%=[%l:%c(%p%%)]  ## 상태정보라인 구성
set smartindent                         ## 스마트한 들여쓰기
set tabstop=4                           ## 탭(tab) 간격
set textwidth=80                        ## 자동줄 바꿈 길이
set title                               ## 현재 수정중인 파일명 표시
set ruler                               ## 좌표 표시
colo koehler                            ## 색상 테마
```

<br><br><br><br><br>





<h2 id="etc">Etc</h2>
<h3>※ 하드웨어</h3>
 - 랙 장비
   - 랙 PC 별 cmos 기능이 다름
   - disk raid1 잡은후 복구 기능확인 필요!!!