---
title: CentOs 설치 및 간단한 설정 
layout: post
categories: os
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
# 80 없을 시 아래 실행

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

cd /usr/local/tomcat-connectors-1.2.49/native/

# configure 명령어를 통해 설치경로 정보(여기서는 아파치 설치 경로/usr/local/apache/bin/apxs)를 포함한 make 파일을 생성합니다.
./configure --with-apxs=/usr/local/apache/bin/apxs

# 정상적으로 make 파일이 생성되었다면 make 파일로 컴파일하고
# make install로 설치를 진행합니다
# 이 두 작업을 통시에 수행하기 위해 make && make install 명령을 사용합니다
# 정상적으로 설치가 완료되면 아파치홈/modules 경로에 mod_jk.so 파일이 생성되게 됩니다.
make && make install
```

<br><br><br><br><br>





<h2 id="ApacheSetting">3. Apache Setting</h2>
<p>httpd-conf 설정</p>

```
cd /usr/local/apache/conf
vi httpd-conf
    # 80 포트 추가
    LISTEN 80

    # 모듈 추가
    LoadModule jk_module modules/mod_jk.so

	# ajp 사용을 위해 mod_jk.con 하단에 추가
    includ conf/mod_jk.conf

	# virtual host 셋팅을 위해 제일 하단에 추가
    includ httpd-vhost.conf

# 저장
:wq
```

<p>mod_jk 환경 셋팅</p>

```
vi mod_jk.conf
<IfModule jk_module>
    JkWorkersFile "conf/workers.properties"
    JkLogFile "logs/mod_jk.log"
    JkLogLevel info
    JkLogStampFormat "[%y %m %d %H:%M:%S] "
    JkRequestLogFormat "%w %V %T"
    jkShmFile logs/modjk.shm
</IfModule>
```

<p>Workers 셋팅</p>

```
vi workers.properties

# worker list
worker.list=worker-lb,status

#client node1
worker.node1.port=18009
worker.node1.host=127.0.0.1
worker.node1.type=ajp13
worker.node1.ping_mode=A
worker.node1.lbfactor=1

#client node2
worker.node2.port=18010
worker.node2.host=127.0.0.1
worker.node2.type=ajp13
worker.node2.ping_mode=A
worker.node2.lbfactor=1

# Load-balancing behavior
worker.worker-lb.type=lb
worker.worker-lb.balance_workers=node1,node2
worker.worker-lb.sticky_session=1
worker.status.type=status
```

<p>Virtual Host 셋팅</p>

```
cd extra/
# ssl 적용 시 httpd-ssl.conf 사용

vi httpd-vhosts.conf
<VirtualHost *:80>
    # 관리자 메일 주소
    # ServerAdmin

    # project 경로
    DocumentRoot "/home/{경로}/ROOT"

    ServerName 127.0.0.1

    # JkMount Tomcat 에서 사용
    JkMount /*          worker-lb
    JkMount /*.jsp      worker-lb
    JkMount /*.json     worker-lb
    JkMount /*.xml      worker-lb
    JkMount /*.do       worker-lb
    JkMount /resources  worker-lb

    # JkUnMount Apache 에서 사용
    JkUnMount /js/*     worker-lb
    JkUnMount /css/*    worker-lb
    JkUnMount /img/*    worker-lb
    JkUnMount /fonts/*  worker-lb
    JkUnMount /upload/* worker-lb

    # Apache 에서 읽을 수 있게 Alias
    Alias /css /home/{경로}/css
    Alias /fonts /home/{경로}/fonts
    Alias /img /home/{경로}/img
    Alias /js /home/{경로}/js
    Alias /template /home/{경로}/template
    Alias /upload /home/{경로}/upload

    ErrorLog "logpath"
    CustomLog "logpath" common

    # Directory
    # Options : 특정 디렉토리에서 사용할 수 있는 서버 기능을 제어한다.
              - All : 모든 Options가 MultiViews에 허용된다.
              - Indexes : 클라이언트가 요청한 디렉토리 경로에 DirectoryIndex 지시자에 설정한 파일이 없을 경우
                          디렉토리 목록을 화면에 표시한다. (디렉토리 리스팅)
              - FollowSymLinks : 설정한 디렉토리에서 심볼릭 링크를 허용한다.
              - 이외에도 다양함, 대중적으로 쓰는것만 적음

    # AllowOverride : .htaccess 파일의 사용 여부를 결정한다.
                    - All | None

    # Require : 해당 디렉토리의 접근 허용 여부를 설정한다.
              - all denied(모든접근 거부) , all granted(모든 접근 허용) 이외에도 IP,도메인으로 셋팅 가능
    
    # Order : 디렉토리 접근 권한을 위한 지시자
            - allow, deny : 디렉토리에 접근에 대해 먼저 허용하고 그 다음이 거부하겠다

    # Allow from : 모든곳에서 들어오는 접속을 허용
                 - all | domain
    
    <Directory />
        Options FollowSymLinks
        AllowOverride None
        Order allow,deny
        Allow from all
    </Directory>

</VirtualHost>

# 저장
:wq

# httpd 재시작
systemctl restart httpd
```

<p>Tomcat Setting</p>

```
cp -R /usr/local/tomcat /home/{경로}
  
cd /home/{경로}/conf
vi server.xml

# 신경써야 할 부분
# 1. 최상단 <Server> 태그의 port : Was Port
# 2. 중간 <Service> 안의 <Connector> 태그 : Ajp Port
# 3. 바로 아래 <Engine> 태그 : load balancing 을 위한 태그
# 4. 제일 하단 <Host> 태그 : 도메인 경로 , war 패키징 여부 , appBase 경로

======================================================================
# 첫 프로젝트라면 그냥 사용해도 무방
<Server port="8005">

# port : workers.properties 에서 작성한 port 사용
# secretRequired : workers.properties 에서 작성 가능 (보안상 작성하는게 좋음)
<Connector protocol="AJP/1.3"
           port="18014"
           redirectPort="8443"
           address="0.0.0.0"
           secretRequired="false"
           />

# jvmRoute workers.properties 에서 작성한 node 사용
<Engine name="Catalina" defaultHost="localhost" jvmRoute="node1">

# 
<Host name="localhost" appBase="webapps" unpackWARs="true" autoDeploy="true">

# 저장
:wq

cd ../bin/
sudo ./startup.sh
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

<br><br><br>



<h2 id="etc">Etc</h2>
<h3>※ 하드웨어</h3>
 - 랙 장비
   - 랙 PC 별 cmos 기능이 다름
   - disk raid1 잡은후 복구 기능확인 필요!!!
   
<br >
<hr >
다음 내용엔 Jenkins Setting 에 관한걸 작성해보겠습니다.
<hr >