---
title: 웹 취약점 점검 툴 sonarQube 설치 및 사용법
layout: post
categories: backEnd
order: 4
data: 2025-12-02 14:00
---

# SonarQube 설치 및 사용법

웹 취약점 점검 툴 중 하나인 **SonarQube**의 설치 및 사용법을 정리합니다.

---

## 0. 소개
SonarQube는 코드 품질, 보안 취약점, 버그 등을 점검할 수 있는 웹 기반 분석 도구입니다.

---

## 1. SonarQube 설치 (v9.9.8 기준)
1. 공식 다운로드:  
   - [SonarQube 다운로드](https://binaries.sonarsource.com/?prefix=Distribution/sonarqube/)

2. 설치 후 SonarQube 서버 실행:
   - Windows: `/설치_경로/bin/windows-x86-64/StartSonar.bat` 실행
   - Linux: `/설치_경로/bin/linux-x86-64/sonar.sh start` 실행

3. 기본 로그인 정보:
   - 사용자 계정: `admin`
   - 비밀번호: `admin`

4. 브라우저 접속:
   - http://localhost:9000/ (SonarQube 기본 port 9000)
   - 9000 port 사용중 확인
```bash
netstat -ano | findstr 9000

# kill
taskkill /F /PID [위에서 발견 한 PID 입력]
```
   - 만약 port 를 변경 하고 싶을 시 /설치_경로/conf/sonar.properties 에 port 변경
```bash
sonar.web.port=9001
```



---

## 2. Sonar Scanner 설치 (v7.3.0 기준)
1. 공식 다운로드:  
   [Sonar Scanner 다운로드](https://binaries.sonarsource.com/?prefix=Distribution/sonar-scanner-cli/)

2. 환경 변수 설정:
- `SONAR_SCANNER_HOME` : 설치 경로
- `PATH` : 설치 경로/bin

3. 설치 확인:
```bash
sonar-scanner --version
```

---

## 3. JDK 호환성
1. SonarQube v9.9.8과 Scanner v7.3.0은 JDK 17과 호환됩니다.
2. 설치된 JDK 버전 확인:
```bash
java -version
```

---

## 4. Token 발급 및 프로퍼티 적용

1. SonarQube 웹 UI에서 My Account > Security > Generate Token으로 토큰 발급
- 검색어 입력 우측에 박스 혹은 url 에 /account 입력

<img src="/assets/img/backEnd/account.PNG" />

2. sonar-project.properties 생성 및 설정:
- /설치_경로/conf/sonar-scanner.properties

```bash
sonar.projectKey=YourProjectKey
sonar.host.url=http://localhost:9000
sonar.login=발급받은_토큰
```

3. Scanner 실행:
```bash
sonar-scanner
```

---

## 참고

SonarQube 공식 문서: https://docs.sonarqube.org