---
title: 리눅스 2>%1 란 무엇일까?
layout: post
categories: os
order: 8
data: 2024-01-05 13:57
---

# 리눅스 파일 디스크립터

보통 에러 , 로그 처리할때 많이 사용되는 스크립트 코드 2>&1 이것은 무엇일까?<br>

Linux 에서는 실행되는 프로그램에게 '파일 디스크립터' 라는 것을 할당해준다. <br>
기본적으로 할당되는 파일 디스크립터는 총 3개로, 아래와 같다. <br>

<hr>
<table>
    <colgroup>
        <col style="width: 30%;" >
        <col style="width: auto;" >
    </colgroup>
    <thead>
        <tr>
            <th>파일 디스크립터</th>
            <th>정의</th>
        </tr>
    </thead>
    <tfoot style="text-align: center;">
        <tr>
            <td>0</td>
            <td>표준 입력 (standard input)</td>
        </tr>
        <tr>
            <td>1</td>
            <td>표준 출력 (standard output)</td>
        </tr>
        <tr>
            <td>2</td>
            <td>표준 에러 (standard error)</td>
        </tr>
    </tfoot>
</table>
<hr>



여기서 알 수 있는게 <br>
[ 1 ] 은 표준 출력, <br>
[ 2 ] 은 표준 에러라는걸 알 수 있다. <br><br>

```
# 2>&1
```

여기서 [ 2 ] 는 표준 에러라는걸 확인했구, <br><br>
[ > ] 는 2 를 &1 로 오버라이딩 한다는 의미이다. <br>
[ &1 ] 은 표준 출력을 의미한다. <br><br>
그러면 [ 2>&1 ] 이 코드의 의미는 표준에러가 발생되면, <br>
표준 출력으로 리다이렉트 해서 내보낸다는 의미이다. <br><br>
결과는 표준에러와 표준출력이 catalina.sh 파일에 같이 출력된다. <br>
