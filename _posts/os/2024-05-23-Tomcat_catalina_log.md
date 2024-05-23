---
title: Linux Tomcat catalina 로그 날짜별 생성
layout: post
categories: os
order: 9
data: 2024-05-23 12:00
---

# Tomcat Catalina.out Log 날짜별 생성
로그 날짜별 생성하는건 여러 방법이 있다.
- rotatelogs , cronolog , logrotate

<br>
여기서는 Apache 의 rotatelogs 를 사용했다.

<br>
Ⅰ. 먼저 rotatelogs 의 위치를 확인 후 잘 메모해 놓은다.

```
# which rotatelogs
```

<img src="/assets/img/os/tomcat_log_1.JPG" />

<br>
Ⅱ. 톰켓의 catalina.sh 파일을 열어 두 곳을 수정한다.
- :set number 를 이용하여 400 ~ 600 번대 사이에 위치한 부분을 주석 처리 한다. (몇번째 줄에 위치하는건 버전마다 다름)
- $CATALINA_OUT" 2>&1 로 검색을 하면 찾기 쉽다.

```
# :set number
# /"$CATALINA_OUT" 2>&1 "&"
# # "$CATALINA_OUT" 2>&1 "&"
```

<img src="/assets/img/os/tomcat_log_2.JPG" />

<br>
Ⅲ. 아까 [ Ⅰ ] 번 에서 찾았던 rotatelogs 위치를 아래와 같은 코드로 넣어준다.

```
2>&1 "&" | /usr/local/apache/bin/rotatelogs "$CATALINA_OUT"-%Y.%m.%d 86400 540 &
```

<br>
※ 위 명령어 해석<br>
- 2>&1 : 이전 글인 "[리눅스 2>%1 란 무엇일까?](/2024-05/File_descriptor)" 를 참고하면 된다.
- /usr/local/apache/bin/rotatelogs : rotatelogs 를 이용해 로그를 쌓는다.
- %Y.%m.%d : y.m.d 형식의 날짜로 포멧
- 86400 : 86400 은 초 단위로 로그를 분할하는 시간이며 1일(24시간)을 의미 (60 * 60 * 24 = 86400)
- 540 : GMT 표준시를 한국 기준으로 조정함을 의미
