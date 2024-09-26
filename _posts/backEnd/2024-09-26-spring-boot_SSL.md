---
title: spring boot SSL 인증서 적용
layout: post
categories: backEnd
order: 1
data: 2024-09-26 15:00
---

개발 및 유지보수 하던 사이트 하나가 가비아 웹 호스팅 서버 이용하다가<br>
클라우드 서버로 변경하는 작업이 있었다.<br>
기존 호스팅 서버 사용 할 때는 가비아에서 해줬던거 같다..(기존 작업자가 아님..)<br>
클라우드로 이관하면서 SSL 인증서 적용한 방법을 기록해봅니다.<br><br>

Ⅰ. 기존 가비아 SSL 인증서 파일들 다운로드(key , 인증서 , 체인 , 루트)<br><br>
Ⅱ. 클라우드 서버(CentOS 사용중)에서 인증서 파일 병합
- <code>cat cert.crt - chain.crt - root.crt > sslmerge.crt</code>입력
- 엔터 한 번 입력
- <code>ctrl + D</code> 입력

위 예시는 인증서 + 체인 + 루트 파일을 전부 병합 한거다.<br>
순서는 꼭 지켜야 한다.<br>
개인키를 같이 병합해도 되나, 제대로 읽히지 않는경우가 많아 제외하고 뒤에 p12 변환 할 때 한다.<br><br>

Ⅲ. p12 병합

- <code>openssl pkcs12 -export -in sslmerge.crt -inkey ssl.key -out ssl.p12</code> 입력
- 키파일 비밀번호 입력
- 생성되는 p12 파일의 보호 비밀번호 입력 및 확인

<br>
Ⅳ. p12 파일 설치

- 프로젝트 최상단 혹은 리소스 경로에 p12 파일을 위치 시킨다. (나는 리소스 경로에 위치시켰다.)
- application.properties 혹은 yml 에 p12 설정을 추가.

```
server.port=443
server.ssl.enabled=true
server.ssl.key-store=classpath:ssl/ssl.p12
server.ssl.key-store-type=PKCS12
server.ssl.key-store-password=패스워드
server.ssl.protocol=TLS
server.ssl.enabled-protocols=TLSv1.2
server.ssl.ciphers=ECDHE-RSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHERSA-AES128-SHA:ECDHE-RSA-AES256-SHA:ECDHE-RSA-AES128-SHA256:ECDHE-RSA-AES256-SHA384
```
생각보다 간단했다.