---
title: Kali Linux - wapiti
layout: post
categories: os
order: 5
data: 2024-01-15 14:31
---

# Kali Linux - wapiti

글 쓰기에 앞서 [Wapiti 깃 홈페이지](https://github.com/wapiti-scanner/wapiti/){:target="_blank"}
를 참고했음을 알려드립니다.

오늘은 칼리 리눅스에 설치 된 여러 모듈중<br >
wapiti 를 이용해 취약점 검사 및 리포트 출력을 해보겠습니다.<br >

## 사용법

우선 기본적인 명령 형식은 아래와 같습니다.

`wapiti` -u <BASE_URL> [options]

다양한 옵션 정보가 있으니 `wapiti` -h 명령어 혹은<br >
wapiti 깃 홈페이지 [옵션 요약글](https://github.com/wapiti-scanner/wapiti/blob/master/doc/wapiti.ronn){:target="_blank"}
을 확인 후 이용하시면 좋습니다..<br >


## 옵션 목록

```
usage: wapiti [-h] [-u URL] [--data data] [--scope {page,folder,domain,url,punk}]
              [-m MODULES_LIST] [--list-modules] [-l LEVEL] [-p PROXY_URL] [--tor]
              [--mitm-port PORT] [-a CREDENTIALS] [--auth-type {basic,digest,ntlm,post}]
              [-c COOKIE_FILE] [--drop-set-cookie] [--skip-crawl] [--resume-crawl]
              [--flush-attacks] [--flush-session] [--store-session PATH]
              [--store-config PATH] [-s URL] [-x URL] [-r PARAMETER] [--skip PARAMETER]
              [-d DEPTH] [--max-links-per-page MAX] [--max-files-per-dir MAX]
              [--max-scan-time SECONDS] [--max-attack-time SECONDS] [--max-parameters MAX]
              [-S FORCE] [--tasks tasks] [--external-endpoint EXTERNAL_ENDPOINT_URL]
              [--internal-endpoint INTERNAL_ENDPOINT_URL] [--endpoint ENDPOINT_URL]
              [--dns-endpoint DNS_ENDPOINT_DOMAIN] [-t SECONDS] [-H HEADER] [-A AGENT]
              [--verify-ssl {0,1}] [--color] [-v LEVEL] [--log OUTPUT_PATH] [-f FORMAT]
              [-o OUTPUT_PATH] [-dr] [--no-bugreport] [--update] [--version]
```

## 옵션 요약

다음은 옵션 요약입니다. 각 옵션에 대한 자세한 내용은 다음 섹션에서 확인할 수 있습니다.

[대상](#target):

  * `-u, --url <URL>`
  * `--data <URL_ENCODED_DATA>`
  * `--scope {page,folder,domain,url,punk}`

[공격 모듈](#attack):

  * `-m <MODULES_LIST>`
  * `--list-modules`
  * `-l, --level <LEVEL>`

[프록시 및 인증 옵션](#proxy):

  * `-p, --proxy <PROXY_URL>`
  * `--tor`
  * `--mitm-port <PORT>`
  * `-a, --auth-cred <CREDENTIALS>`
  * `--auth-user <USERNAME>`
  * `--auth-password <PASSWORD>`
  * `--auth-method {basic,digest,ntlm}`
  * `--form-cred <CREDENTIALS>`
  * `--form-user <USERNAME>`
  * `--form-password <PASSWORD>`
  * `--form-url <URL>`
  * `--form-enctype <ENCTYPE>`
  * `--form-script <FILENAME>`
  * `-c, --cookie <COOKIE_FILE_OR_BROWSER_NAME>`
  * `--drop-set-cookie`

[세션 옵션](#session):

  * `--skip-crawl`
  * `--resume-crawl`
  * `--flush-attacks`
  * `--flush-session`
  * `--store-session <PATH>`
  * `--store-config <PATH>`

[스캔 및 공격 튜닝](#scan):

  * `-s, --start <URL>`
  * `-x, --exclude <URL>`
  * `-r, --remove <PARAMETER>`
  * `--skip <PARAMETER>`
  * `-d, --depth <DEPTH>`
  * `--max-links-per-page <MAX_LINKS_PER_PAGE>`
  * `--max-files-per-dir <MAX_FILES_PER_DIR>`
  * `--max-scan-time <MAX_SCAN_TIME>`
  * `--max-attack-time <MAX_ATTACK_TIME>`
  * `--max-parameters <MAX>`
  * `-S, --scan-force {paranoid,sneaky,polite,normal,aggressive,insane}`
  * `--tasks <TASKS>`

[엔드포인트 옵션](#endPoint):

  * `--external-endpoint <EXTERNAL_ENDPOINT_URL>`
  * `--internal-endpoint <INTERNAL_ENDPOINT_URL>`
  * `--endpoint <ENDPOINT_URL>`
  * `--dns-endpoint <DNS_ENDPOINT_DOMAIN>`

[HTTP 및 네트워크 옵션](#network):

  * `-t, --timeout <SECONDS>`
  * `-H, --header <HEADER>`
  * `-A, --user-agent <AGENT>`
  *  `--verify-ssl {0,1}`

[출력 옵션](#print):

  * `--color`
  * `-v, --verbose <LEVEL>`
  * `--log <OUTPUT_PATH>`

[보고서 옵션](#report):

  * `-f, --format {json,html,txt,xml}`
  * `-o, --output <OUTPUT_PATH>`
  * `-dr, --detailed-report`

[다른 옵션](#etc):

  * `--no-bugreport`
  * `--version`
  * `--update`
  * `-h`

<h2 id="target">대상</h2>

  * `-u, --url <URL>`
    스캔의 기반으로 사용될 URL입니다. 스캔 중에 발견된 모든 URL은 기본 URL 및 해당 스캔 범위와 비교하여 확인됩니다(자세한 내용은 --scope 참조).
    이것이 유일한 필수 인수입니다. URL의 구성표 부분은 http 또는 https여야 합니다.

  * `--data <URL_ENCODED_DATA>`
    특정 POST 요청만 공격해야 하는 경우 이 옵션에 URL 인코딩 문자열을 제공할 수 있습니다. 옵션 에 지정된 URL에 대한 POST 매개변수로 사용됩니다 `-u` .  
    
  * `--scope <SCOPE>`
    검사 및 공격 범위를 정의합니다. 유효한 선택 사항은 다음과 같습니다.
      - url : -u 옵션으로 제공된 정확한 기본 URL만 스캔하고 공격합니다.
      - page : 기본 URL의 경로와 일치하는 모든 URL(모든 쿼리 문자열 변형)을 공격합니다.
      - folder : 기본 URL 값으로 시작하는 모든 URL을 검사하고 공격합니다. 이 기본 URL에는 뒤에 슬래시(파일 이름 없음)가 있어야 합니다.
      - domain : 도메인 이름이 기본 URL의 이름과 일치하는 모든 URL을 검사하고 공격합니다.
      - punk : 도메인에 관계없이 발견된 모든 URL을 검사하고 공격합니다. 해당 범위를 사용하기 전에 두 번 생각하십시오.
    
<h2 id="attack">공격 모듈</h2>

  * `-m, --module <MODULE_LIST>`
    대상에 대해 실행할 공격 모듈(쉼표로 구분된 모듈 이름) 목록을 설정합니다.
    기본 동작(옵션이 설정되지 않은 경우)은 가장 일반적인 모듈을 사용하는 것입니다.
    공통 모듈은 "common" 키워드를 사용하여 지정할 수도 있습니다.
    XXE 모듈과 함께 공통 모듈을 사용하려면 -m common,xxe를 전달하면 됩니다.
    모든 모듈을 활성화하려면 "all" 키워드를 사용하면 됩니다(권장하지는 않음).
    공격을 시작하지 않고 스캔을 시작하려면 빈 값(-m "")을 지정하면 됩니다.
    http 메소드로도 필터링할 수 있습니다(가져오기 또는 게시만 가능). 예를 들어 -m "xss:get,exec:post"입니다.
    
  * `--list-modules`
    간단한 설명과 함께 사용 가능한 Wapiti 모듈 목록을 인쇄한 후 종료합니다.
    
  * `-l, --level <LEVEL>`
    이전 버전에서 Wapiti는 원래 URL에 매개변수가 없더라도 쿼리 문자열에 공격 페이로드를 주입했습니다.
    그런 식으로 취약점을 찾는 데는 성공할 수 있지만 성공하지 못한 데 비해 너무 많은 요청이 발생했습니다.
    이 동작은 이제 이 옵션 뒤에 숨겨져 있으며 -l을 2로 설정하여 다시 활성화할 수 있습니다.
    개발자가 쿼리 문자열 자체를 구문 분석해야 하는 경우 CGI에서 유용할 수 있습니다.
    이 옵션의 기본값은 1입니다.
  

### 지원되는 공격 / 모듈 이름
  
  * SQL 주입(오류 기반, 부울 기반, 시간 기반) 및 XPath 주입
    * sql(오류 기반 및 부울 기반 SQL 주입 감지)
    * timesql(시간 기반 방법으로 탐지된 SQL 주입 취약점)

  * 코드 실행 또는 명령 주입
    * exec
  
  * XSS(교차 사이트 스크립팅)가 반영되고 영구적입니다.
    * xss(XSS 주입 모듈)
    * Permanentxss(이전에 오염된 페이로드를 찾기 위해 xss 모듈 실행 후 전체 대상을 다시 검색)
  
  * 파일 공개 감지(로컬 및 원격 포함, 요구, fopen, readfile...)
    * file(경로 탐색, 파일 포함 등)
    
  * 명령 실행 감지(eval(), system(), passtru()...)
    * ???
  
  * XXE(Xml eXternal Entity) 삽입
    * xxe(XML 외부 엔터티 공격)
    
  * CRLF 주입
    * crlf(HTTP 헤더에 CR-LF 삽입)
    
  * 서버에서 잠재적으로 위험한 파일 검색
    * nikto(URL 존재 여부를 테스트하고 응답을 확인하여 알려진 취약점을 찾습니다)
  
  * 약한 htaccess 구성 우회
    * htaccess
    
  * 서버에서 스크립트 복사본(백업) 검색
    * backup (웹 서버의 스크립트 및 아카이브 사본 검색)
  
  * 쉘쇼크
    * shellshock(Shellshock 공격 테스트, Wikipedia 참조 )
    
  * 폴더 및 파일 열거(DirBuster 유사)
    * Buster (DirBuster 같은 모듈)
    
  * 서버 측 요청 위조(외부 Wapiti 웹사이트 사용을 통해)
    * ssrf(서버측 요청 위조)
  
  * 오픈 리디렉션
    * redirect (오픈 리디렉션)
    
  * 일반적이지 않은 HTTP 메소드(예: PUT) 감지
    * methods(PUT과 같이 일반적이지 않은 사용 가능한 HTTP 메소드를 찾으십시오.)
  
  * 기본 CSP 평가자
    * csp (CSP 부족 또는 약한 CSP 구성 감지)
    
  * 무차별 로그인 양식(사전 목록 사용)
    * brute_login_form(사전 목록을 사용한 무차별 로그인 양식)
  
  * HTTP 보안 헤더 확인
    * http_header(HTTP 보안 헤더 확인)
  
  * 쿠키 보안 플래그 확인(보안 및 httponly 플래그)
    * cookieflags(Secure 및 HttpOnly 플래그 확인)
  
  * CSRF(교차 사이트 요청 위조) 기본 탐지
    * csrf(CSRF로부터 보호되지 않거나 약한 안티-CSRF 토큰을 사용하는 양식 감지)
    
  * Wappalyzer 데이터베이스를 사용한 웹 애플리케이션 지문 인식
  
  * Wordpress 및 Drupal 모듈 열거
    * wp_enum(Wordpress 웹사이트의 플러그인 및 테마 열거)
    * drupal_enum(Drupal 버전 감지)
  
  * 하위 도메인 인수 감지
    * takeover (하위 도메인 인수)
    
  * Log4Shell(CVE-2021-44228) 탐지
    * log4shell(CVE-2021-44228에 취약한 웹사이트 탐지)
  
  * Spring4Shell(CVE-2020-5398) 탐지
    * spring4shell(CVE-2020-5398에 취약한 웹사이트 탐지)
    
  * https 리디렉션 확인
    * https_redirect(https 리디렉션 확인)
  
  * 파일 업로드 취약점 확인
    * upload(파일 업로드 취약점)

<hr >
  
  * Wapiti는 공격에 대해 GET 및 POST HTTP 방법을 모두 지원합니다.
    또한 멀티파트를 지원하고 파일 이름에 페이로드를 삽입할 수 있습니다(업로드).
    예외 사항(예: 500 오류 및 시간 초과)이 발견되면 경고를 표시합니다.
    영구적인 XSS 취약점과 반영된 XSS 취약점을 구분합니다.

<hr >
  
  * 모듈 이름은 "-m" 또는 "--module" 옵션을 사용하여 쉼표로 구분된 목록으로 제공될 수 있습니다.
  
### 찾지 못한 모듈 이름..


앞서 언급한 공격은 다음 모듈 이름과 연결되어 있습니다.
  
  * htp(HashThePlanet 데이터베이스를 사용하는 웹 기술 식별)
  
  * ssl(SSL/TLS 인증서 구성의 보안 평가, SSLyze 필요 )
  
  * wapp (공격 모듈이 아니며 대상에서 사용 중인 버전 및 카테고리로 웹 기술을 검색합니다)
    
    
<h2 id="proxy">프록시 및 인증 옵션</h2>

  * `-p, --proxy <PROXY_URL>`
    주어진 URL은 HTTP 및 HTTPS 요청에 대한 프록시로 사용됩니다. 이 URL은 http, https, 양말 구성표 중 하나를 가질 수 있습니다.
    
  * `--tor`
    Wapiti가 Tor 수신기를 사용하도록 만듭니다(--proxy Socks://127.0.0.1:9050/과 동일).

  * `--mitm-port <PORT>`
    이 옵션을 사용하면 자동화된 크롤러를 사용하여 대상을 탐색하는 대신 지정된 포트에서 수신 대기하는 mitmproxy 인스턴스를 시작합니다.
    차단 프록시를 사용하도록 브라우저를 구성한 다음 대상을 수동으로 탐색하십시오. 완료되면 콘솔에서 Ctrl+C를 누르세요.

  * `-a, --auth-cred <CREDENTIALS>`  
    (더 이상 사용되지 않음) 대상에서 HTTP 인증에 사용할 자격 증명을 설정합니다(아래에서 사용 가능한 방법 참조).
    주어진 값은 login%password 형식이어야 합니다(%는 구분 기호로 사용됨).

  * `--auth-user <USERNAME>`
    대상에서 HTTP 인증에 사용할 사용자 이름을 설정합니다(아래에서 사용 가능한 방법 참조).

    * `--auth-password <PASSWORD>`
    대상에서 HTTP 인증에 사용할 비밀번호를 설정합니다(아래에서 사용 가능한 방법 참조).

  * `--auth-method <TYPE>`
    사용할 인증 메커니즘을 설정합니다. 유효한 선택 사항은 basic, Digest 및 ntlm입니다.
    NTLM 인증을 위해서는 추가 Python 모듈을 설치해야 할 수도 있습니다.

  * `--form-cred <CREDENTIALS>`  
    (더 이상 사용되지 않음) 대상에서 웹 양식 인증에 사용할 자격 증명을 설정합니다.
    주어진 값은 login%password 형식이어야 합니다(%는 구분 기호로 사용됨).

  * `--form-user <USERNAME>`
    대상에서 웹 양식 인증에 사용할 사용자 이름을 설정합니다.

    * `--form-password <PASSWORD>`
    대상에서 웹 양식 인증에 사용할 비밀번호를 설정합니다.

  * `--form-url <URL>`
    설정되지 않은 경우 `--form-data` Wapiti는 지정된 URL에서 로그인 양식을 추출하고 제공된 자격 증명으로 채웁니다.
    그렇지 않으면 원시 자격 증명이 지정된 URL로 직접 전송됩니다.

  * `--form-enctype <ENCTYPE>`
    `--form-data`지정된 콘텐츠 유형을 사용하여 지정된 데이터 보내기 (기본값은 "application/x-www-form-urlencoded")

  * `--form-script <FILENAME>`
    사용자 정의 Python 인증 플러그인 사용

  * `-c, --cookie <COOKIE_FILE_OR_BROWSER_NAME>`
    Wapiti JSON 쿠키 파일에서 쿠키를 로드합니다. 자세한 내용은 wapiti-getcookie(1)를 참조하십시오.
    "chrome" 또는 "firefox"를 값으로 전달하여 브라우저에서 쿠키를 가져올 수도 있습니다(MS Edge는 지원되지 않음).
    
  * `--drop-set-cookie`  
    HTTP 응답에 제공된 쿠키를 무시합니다. 를 사용하여 로드된 쿠키는 `-c`유지됩니다.
    
<h2 id="session">세션 옵션</h2>

Wapiti 3.0.0부터 스캔된 URL, 발견된 취약점 및 공격 상태는 Wapiti 세션 파일로 사용되는 sqlite3 데이터베이스에 저장됩니다.
지정된 기본 URL 및 범위에 대해 이전 스캔 세션이 존재할 때의 기본 동작은 스캔 및 공격 상태를 재개하는 것입니다.
다음 옵션을 사용하면 이 동작을 우회할 수 있습니다.

  * `--skip-crawl`  
    이전 검사를 수행했지만 완료되지 않은 경우 검사를 재개하지 마세요.
    추가 검사 없이 현재 알려진 URL에 대한 공격이 이루어집니다.
    
  * `--resume-crawl`  
    크롤링이 이전에 중지되고 공격이 시작된 경우 기본 동작은 세션이 복원되면 크롤링을 건너뛰는 것입니다.
    세션에서 취약점과 공격을 유지하면서 검색 프로세스를 계속하려면 이 옵션을 사용하십시오.
    
  * `--flush-attacks`  
    발견된 취약점과 어떤 모듈이 어떤 URL을 공격했는지에 대한 모든 정보를 잊어버리십시오.
    검사(크롤링) 정보만 보관됩니다.
    
  * `--flush-session`  
    주어진 범위의 대상에 대한 모든 것을 잊어 버리십시오.
    
  * `--store-session` <PATH>  
    세션(.db 및 .pkl) 파일을 저장하기 위한 대체 경로를 지정합니다.

  * `--store-config` <PATH>  
    특정 모듈(`apps.json` 및 `nikto_db`) 파일을 저장하기 위한 대체 경로를 지정합니다.


<h2 id="scan">스캔 및 공격 튜닝</h2>

  * `-s, --start <URL>`
    어떤 이유로 Wapiti가 기본 URL에서 URL을 전혀(또는 충분한) 찾지 못하는 경우에도 URL을 추가하여 스캔을 시작할 수 있습니다.
    이러한 URL에는 기본 URL과 마찬가지로 깊이가 0으로 지정됩니다.
    이 옵션은 여러 번 호출될 수 있습니다.
    파일 이름을 지정할 수도 있으며 Wapiti는 주어진 파일(UTF-8로 인코딩되어야 함)에서 한 줄에 하나씩 URL을 읽습니다

  * `-x, --exclude <URL>`
    주어진 URL이 스캔되는 것을 방지합니다. 일반적인 용도는 세션 쿠키의 파기를 방지하기 위해 로그아웃 URL을 제외하는 것입니다(-cookie로 쿠키 파일을 지정한 경우).
    이 옵션은 여러 번 적용될 수 있습니다. 매개변수로 지정된 제외 URL에는 기본 패턴 일치를 위한 와일드카드가 포함될 수 있습니다.
    
  * `-r, --remove <PARAMETER>`
    지정된 매개변수가 스캔된 URL에서 발견되면 자동으로 제거됩니다(URL이 편집됨).
    이 옵션은 여러 번 사용할 수 있습니다.
    
  * `--skip <PARAMETER>`
    주어진 매개변수는 URL과 양식에 보관되지만 공격을 받지는 않습니다.
    취약하지 않은 매개변수를 이미 알고 있는 경우 유용합니다.
    
  * `-d, --depth <DEPTH>`
    Wapiti가 웹사이트를 크롤링할 때 발견된 각 URL에 깊이 값을 제공합니다.
    기본 URL 및 추가 시작 URL(-s)에는 깊이가 0으로 지정됩니다.
    해당 URL에서 발견된 각 링크의 깊이는 1입니다.
    기본 최대 깊이는 40이며 매우 큽니다.
    이 제한으로 인해 스캔이 어느 시점에 중지됩니다.
    빠른 스캔을 위해서는 5 이하의 깊이를 권장합니다.
    
  * `--max-links-per-page <MAX>`
    이는 크롤러가 발견한 URL 수를 줄일 수 있는 또 다른 옵션입니다.
    각 웹페이지의 처음 MAX 링크만 추출됩니다.
    동일한 링크가 다른 웹페이지에 나타날 수 있으므로 이 옵션은 실제로 효과적이지 않습니다.
    예를 들어 쿼리 문자열이 없는 웹페이지가 많은 경우와 같이 드문 경우에 유용합니다.
    
  * `--max-files-per-dir <MAX>`
    웹서버에 있는 각 폴더 아래에서 크롤링할 URL 수를 제한하세요.
    경로에 슬래시가 있는 URL이 반드시 Wapiti가 있는 폴더인 것은 아니며 해당 URL을 그대로 처리합니다.
    이전 옵션과 마찬가지로 특정 상황에서만 유용합니다.
    
  * `--max-scan-time <SECONDS>`
    `SECONDS`여전히 실행 중인 경우 몇 초 후에 검색을 중지합니다.
    다른 프로세스에서 스캔을 자동화하는 데 유용해야 합니다(지속적인 테스트).

  * `--max-attack-time <SECONDS>`
    `SECONDS`각 공격 모듈은 여전히 실행 중인 경우 몇 초 후에 중지됩니다.
    다른 프로세스에서 스캔을 자동화하는 데 유용해야 합니다(지속적인 테스트).
    
  * `--max-parameters <MAX>`
    입력 매개변수가 MAX개 이상인 URL 및 양식은 공격 모듈을 시작하기 전에 삭제됩니다.
    
  * `-S, --scan-force <FORCE>`
    URL이나 양식에 더 많은 입력 매개변수가 있을수록 Wapiti가 보내는 요청도 더 많아집니다.
    요청의 합계가 빠르게 증가할 수 있으며 40개 이상의 입력 필드가 있는 양식을 공격하는 데 엄청난 시간이 걸릴 수 있습니다.
    Wapiti는 수학 공식을 사용하여 매개변수 수가 증가할 때 특정 패턴(동일한 변수 이름)에 대해 스캔되는 URL 수를 줄입니다.
    공식은 `maximum_allowed_patterns = 220 / (math.exp(number_of_parameters * factor) ** 2)` 옵션으로 제공한 값에 따라 요인이 내부 값 컨트롤러가 되는 곳입니다.
    사용 가능한 선택은 편집증, 은밀함, 공손함, 정상적, 공격적, 미친 상태입니다.
    기본값은 일반(매개변수 1개에 대해 URL 147개, 5개에 대해 30개, 10개에 대해 5개, 14개 이상에 대해 1개)입니다.
    미친 모드에서는 해당 제한 계산을 제거하면 모든 URL이 공격을 받습니다.
    편집증 모드는 매개변수 1개, 2개에 대해 5개, 3개에 대해 1개 이상의 매개변수로 30개의 URL을 공격합니다.
    
  * `--tasks <TASKS>`
    Wapiti가 사용해야 하는 동시 작업 수를 설정합니다.
    Wapiti는 이를 위해 Python의 asyncio 프레임워크를 활용합니다.
    
<h2 id="endPoint">엔드포인트 옵션</h2>

일부 공격 모듈은 취약점을 확인하기 위해 HTTP 엔드포인트를 사용하고 있습니다.
예를 들어 SSRF 모듈은 엔드포인트 URL을 웹 페이지 인수에 삽입하여 대상 스크립트가 해당 URL을 가져오려고 하는지 확인합니다.
기본 HTTP 엔드포인트는 http://wapiti3.ovh/ 입니다 . 모듈이 작동하려면 대상과 컴퓨터가 해당 끝점에 참여할 수 있어야 한다는 점을 명심하십시오.
내부 침투 테스트에서는 이 엔드포인트가 대상에 액세스할 수 없으므로 자체 엔드포인트를 설정하는 것이 좋습니다.  

  * `--internal-endpoint <URL>`
    외부 끝점과 다른 내부 끝점을 지정할 수 있습니다.
    내부 엔드포인트는 Wapiti에서 공격 결과를 가져오는 데 사용됩니다.
    NAT 뒤에 있는 경우 로컬 서버의 URL일 수 있습니다(예: http://192.168.0.1/ ) .
    
  * `--external-endpoint <URL>`
    엔드포인트 URL(취약성이 있는 경우 대상이 가져올 URL)을 설정합니다.
    자체 엔드포인트를 사용하면 NIDS 또는 WAF에 걸릴 위험을 줄일 수 있습니다.

  * `--endpoint <URL>`
    이 옵션은 내부 및 외부 끝점 URL을 모두 동일한 값으로 설정합니다.

  * `--dns-endpoint <DNS>`
    이 옵션은 log4shell 공격 모듈에 사용할 DNS 엔드포인트를 지정합니다.
    기본값은 dns.wapiti3.ovh입니다.
    
<h2 id="network">HTTP 및 네트워크 옵션</h2>

  * `-t, --timeout <SECONDS>`
    실패를 고려하기 전에 HTTP 응답을 기다리는 시간(초)입니다.
    
  * `-H, --header <HEADER>`
    Wapiti에서 보낸 모든 요청에 ​​삽입할 사용자 정의 HTTP 헤더를 설정하세요. 이 옵션은 여러 번 사용할 수 있습니다.
    값은 표준 HTTP 헤더 라인이어야 합니다(매개변수와 값은 : 기호로 구분됨).
    
  * `-A, --user-agent <AGENT>`
    Wapiti의 기본 동작은 TorBrowser와 동일한 User-Agent를 사용하여 표준 웹사이트나 .onion 웹사이트를 크롤링할 때 눈에 띄지 않게 만드는 것입니다.
    하지만 일부 제한 사항을 우회하도록 변경해야 할 수도 있으므로 이 옵션이 여기에 있습니다.
    
  * `--verify-ssl <VALUE>`
    Wapiti는 기본적으로 인증서 유효성 검사를 신경 쓰지 않습니다. 해당 옵션에 1을 값으로 전달하여 해당 동작을 변경할 수 있습니다.

    
<h2 id="print">출력 옵션</h2>

Wapiti는 상태를 표준 출력으로 인쇄합니다. 다음 두 가지 옵션을 사용하면 출력을 조정할 수 있습니다.

  * `--color`  
    출력은 정보의 심각도에 따라 색상이 지정됩니다(빨간색은 중요, 주황색은 경고, 녹색은 정보).
    
  * `-v, --verbose <LEVEL>`
    출력의 자세한 수준을 설정하십시오. 가능한 값은 Quiet(O), Normal(1, 기본 동작) 및 Verbose(2)입니다.

  * `--log <OUTPUT_PATH>`
    콘솔에서 정보를 얻는 것 외에도 출력을 로컬 파일에 기록할 수도 있습니다.
    디버그 정보도 해당 파일에 저장되므로 이 옵션은 주로 Wapiti를 디버깅하는 데 사용해야 합니다.

<h2 id="report">보고서 옵션</h2>

Wapiti는 공격 프로세스가 끝나면 보고서를 생성합니다. 다양한 형식의 보고서를 사용할 수 있습니다.

  * `-f, --format <FORMAT>`
    보고서 형식을 설정하십시오. 유효한 선택은 json, html, txt 및 xml입니다.
    반응성을 높이기 위해 HTML 보고서를 다시 작성했지만 발견된 취약점이 많으면 여전히 실행 불가능합니다.
    
  * `-o, --output <OUTPUT_PATH>`
    보고서가 생성될 경로를 설정합니다.

  * `-dr, --detailed-report`  
    HTTP 응답(헤더 및 본문)이 보고서에 표시됩니다.
    
<h2 id="etc">다른 옵션</h2>

  * `--version`  
    Wapiti 버전을 인쇄한 후 종료하세요.
    
  * `--no-bugreport`  
    Wapiti 공격 모듈이 포착되지 않은 예외로 인해 충돌하는 경우 Wapiti 신뢰성을 향상시키기 위해 버그 보고서가 생성되어 분석을 위해 전송됩니다. 보고서의 내용만 유지됩니다.
    해당 옵션을 사용하면 보고서가 전송되지 않도록 할 수 있습니다.

  * `--update`  
    특정 Wapiti 모듈을 업데이트하고(`apps.json` 및 `nikto_db` 파일의 새 버전을 다운로드한 후) 종료하세요.
    `--store-config`와 결합하여 다운로드한 파일을 저장할 위치를 지정할 수 있습니다.

  * `-h, --help`  
    자세한 옵션 설명을 표시합니다. 자세한 내용은 이 맨페이지에서 확인할 수 있습니다.
    
## 특허

Wapiti는 GNU General Public License(GPL) 버전 2의 적용을 받습니다. 자세한 내용은 LICENSE 파일을 읽어보세요.

## COPYRIGHT

저작권 (c) 2006-2023 Nicolas Surribas.


## 모듈 사용 예

sql,xss,xxe 등등 모든 모듈사용해서 공격
`wapiti -u http://sites/ -m all`

sql,xss 모듈 공격
`wapiti -u http://sites/ -m sql,xss`

공격 없이 리포트만 작성
`wapiti -u http://sites/ -m ""`




