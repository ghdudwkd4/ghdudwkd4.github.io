---
title: script 캡슐 방식
layout: post
categories: Develop
order: 2
data: 2024-01-05 17:30
---

### 깔끔한 스크립트 캡슐화 로직

사실 JAVA 를 배우고, 사용함에 있어 OOP(객체지향)라는 것을 많이들 접했을것이다.<br >
이걸 Front 에서 작업 할때도 사용 할 수 있는데,<br >
우리 회사는 사용을 안하고 있어서 OOP 를 알려주며,<br >
사용방법도 널리 알려주었다. (나름 뿌듯)<br >
아래 코드만 보아도 capsule.init() 을 사용하면,<br >
어디서든 capsule 에 있는 기능을 가져다가<br >
사용 할 수 있다는 너무나도 큰 장점이 있다.<br >
But .. 직접 전파하고 경험해본 결과 복잡하다는 의견이 다수였다 ..<br >
장단점이 매우 뚜렷하다 ..<br ><br >

```
'capsule.js'
<script>
    var capsule = (function(){

        function 기능함수() {
            business logic ...
        }
        
        return {
            init : function() {
                // 기능함수 사용 가능
                init 할 logic ...
            },
            input : function() {
                // 기능함수 사용 가능
                기능함수();
                business logic ...
            },
            another : function() {
                // 기능함수 사용 가능
                기능함수();
                business logic ...
            },
        }
    })();
</script>

---------------------------------------------------------

'Test-1 페이지'
<script src="/js/capsule.js"></script>
<script>
    // capsule 함수 호출!!!
    capsule.init();

    // capsule 함수의 기능 사용!!!
    capsule.input();
</script>

```