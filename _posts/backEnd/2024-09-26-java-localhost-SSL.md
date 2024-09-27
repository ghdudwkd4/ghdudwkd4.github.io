---
title: java keytool localhost 용 ssl 생성
layout: post
categories: backEnd
order: 1
data: 2024-09-26 10:00
---

프로젝트 하다보면 local 에서 ssl 인증서가 필요한 경우가 있다.<br>
이번에 작성 할 내용은 제목과 같이 localhost 에서 사용 할 ssl 인증서를 생성 할 것이다.<br>
※ JDK 에는 keytool 이 같이 설치되어 있다. 이걸 이용해 SSL 인증서를 생성한다.

<br>

Ⅰ. Java 개발자라면 JDK 가 있을것이다.<br>
있다는 가정하에 명령 프롬프트를 열어 ${JAVA_HOME}/bin 으로 이동한다.
```
cd ${JAVA_HOME}/bin
```
<br>
Ⅱ. 인증서 생성 keytool 명령어
```
keytool -genkey -alias tomcat -storetype PKCS12 -keyalg RSA -keysize 2048 -keystore ssl.p12 -validity 4000
```
<br>
Ⅲ. 비밀번호 입력 및 인증서 세팅<br>
localhost 에서 사용 할 거라 대충 적었다.<br>
<a href="/assets/img/backEnd/ssl.PNG"><img src="/assets/img/backEnd/ssl.PNG" /></a>

Ⅲ. 이제 ${JAVA_HOME}/bin 폴더에 가보면 ssl.p12 라는 파일이 생성 되었을것이다.
<a href="/assets/img/backEnd/ssl.PNG"><img src="/assets/img/backEnd/ssl2.PNG" /></a>