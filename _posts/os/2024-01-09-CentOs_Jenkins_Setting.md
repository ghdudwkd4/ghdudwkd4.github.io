---
title: CentOs Jenkins Setting
layout: post
categories: os
order: 1
data: 2024-01-09 10:25
---

# CentOs 에 Jenkins 설치방법

jenkins repo , key

``` 
# sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
# sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
# yum install jenkins
```

<br ><br >
설치 완료 후 설정파일 수정

```
# 파일열고 포트 수정
# sudo vim /etc/sysconfig/jenkins
# JENKINS_PORT="8080"

# 젠킨스 구동
# /etc/init.d/jenkins start
```

<br ><br >
방화벽 작업

```
# 프로세스 확인
# ps -ef | grep Jenkins

# 방화벽 작업 : jenkins 포트 번호 뚫어주기
# iptables -I INPUT 1 -p tcp --dport {뚫어줄 포트 지정} -j ACCEPT
# firewall-cmd --zone=public --add-port={뚫어줄 포트 지정}/tcp --permanent
# firewall-cmd --zone=public --add-service=http --permanent
# firewall-cmd --reload

# 방화벽 접근 불가능시 
# sudo vim /etc/sysconfig/jenkins
# JENKINS_USER="root" jenkins 에서 root 로 변경 후 restart처리.
# /etc/init.d/jenkins restart

# unlock jenkins key 위치
# /var/lib/jenkins/secrets/initialAdminPassword
```

<br ><br >
jenkins 로그인 및 플러그인 설치

```
# jenkins 접속 후 플러그인 설치
# 플러그인 설치가 원활히 안되었을 시 재설치 혹은 나중에 설치 후 jenkins 관리에서 재설치
# 필수 항목 설치를 해야되나 버전때문에 설치가 불가능 할 시 강제로 설치하는 방법이 있다.
# jenkins 플러그인 사이트에서 직접 다운받아
# /var/lib/jenkins/plugins 에 위치 시킨 후 재시작을 하면 적용이 되어 있다.
```

<br ><br >
jenkins maven project 생성

### 새로운 Item 에 Maven project 로 생성

1. General
    - 빌드 이력 유지 기간(일) : 30
    - 보관 할 최대 갯수 : 5
    
2. 소스코드관리
    git , svn 택 1 아래는 svn 예시
    - svn : Repository Url , Credentials 입력
    - Local module directory : .
    - Repository depth : infinity
    - Ignore externals : checked
    - Cancel process on externals fail : checked
    - Check-out Strategy : Use 'svn update' as much as possible
    - Quiet check-out : checked
    - Repository browser : auto
    
3. 빌드 유발
    - 없음

4. 빌드 환경
    - Delete workspace before build starts

5. Pre Steps
    - Invoke top-level Maven targets 로 설정
    - Maven Version 해당하는 버전 선택
    - Goals :  clean install

6. Build
    - Root POM : pom.xml
    
7. Post Steps
    - Run regardless of build result
    - Add post-build step 선택 후 Execute shell 두개 추가
    - 첫번째 Shell 에는 프로젝트 옮기는 Shell.
    - 두번째 Shell 에는 Tomcat Restart Shell.

<br >
첫번째 Shell

```
cd /home/{프로젝트 경로}/bin/
sudo ./shutdown.sh

sudo rm -rf /home/{프로젝트 경로}/webapps/ROOT
sudo rm -rf /home/{프로젝트 경로}/webapps/css
sudo rm -rf /home/{프로젝트 경로}/webapps/fonts
sudo rm -rf /home/{프로젝트 경로}/webapps/img
sudo rm -rf /home/{프로젝트 경로}/webapps/js

sudo cp -R /var/lib/jenkins/workspace/{프로젝트 경로}/target/ROOT /home/{프로젝트 경로}/webapps/ROOT
sudo cp -R /var/lib/jenkins/workspace/{프로젝트 경로}/src/main/webroot/* /home/{프로젝트 경로}/webapps
```

두번째 Shell

```
WAS_HOME="/home/{프로젝트 경로}/A01"

# Process check.
TomcatPID=`ps -ef | grep "A01 " | grep -v grep | awk '{ print $2 }'`

if [ -z "${TomcatPID}" ]
then echo "Empty"
else
  echo "${TomcatPID}"
  sudo kill `ps -ef | grep "A01 " | grep -v grep | awk '{ print $2 }'`
fi

sleep 1

sudo ${WAS_HOME}/bin/shutdown.sh
sudo ${WAS_HOME}/bin/startup.sh
```


<br ><br >
Etc

```
# chown -R root:root /var/cache/jenkins
# chown -R root:root /var/log/jenkins
# chown -R root:root /var/lib/jenkins
 
# 젠킨스 보안관련하여 설치가 진행되지 않을 시 하단 플러그인 을 받아서 넣어주면 된다.
# https://updates.jenkins-ci.org/download/plugins/skip-certificate-check/
```