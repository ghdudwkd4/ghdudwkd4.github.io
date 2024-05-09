---
title: 크롬 확장 프로그램 만드는법
layout: post
categories: daily
order: 2
data: 2024-01-05 18:00
---

# 크롬 확장 프로그램 만드는법
크롬 확장 프로그램을 잘 모르는 사람도 있지만 잘 사용하는 사람도 있다.<br>
나도 한번 만들어 보고 싶어서 간단하게 화면 Blur 처리 하는 걸 만들어봤다.<br><br>

먼저 확장 프로그램 만들기에 필요한 파일은<br>
manifest.json , html , 게시 아이콘용 img 세개만 있어도 만들수가 있다.<br>
나는 Blur 기능을 만들거라 javascript 를 사용했다.<br>

<img src="/assets/img/chrome/extensions.jpg" alt="chrome extensions 개발에 필요한 파일 이미지" />

<br>
Ⅰ. manifest.json 에 사용될 내용은 아래와 같다.

```
{
  "name": "배경 블러 처리 크롬 확장 프로그램",    // 명칭
  "description": "웹 페이지의 배경을 블러 처리해 주는 크롬 확장 프로그램입니다.",     // 설명
  "version": "1.0",                             // 버전
  "manifest_version": 3,                        // 개발 당시 기준 가장 최신 버전
  "background": {                               // 백그라운드에 사용 할 스크립트
    "service_worker": "js/background.js"
  },
  "permissions": [                              // 확장 프로그램에 사용 될 권한
    "storage",                                  // storage 사용권한 (local,managed,session,sync)
    "scripting"                                 // script 사용권한
  ],                                            // chrome developers 에서 action 안써도 툴바 표시가 된다고는 한다.
  "action": {                                   // 구글 검색주소창 옆에 툴바 표시
    "default_popup": "popup.html",              // 툴바에 사용 될 html
    "default_icon": {                           // 툴바에 사용 될 아이콘 img
      "16": "img/icon-16.png",
      "32": "img/icon-32.png",
      "48": "img/icon-48.png",
      "128": "img/icon-128.png"
    }
  },
  "icons": {
    "16": "img/icon-16.png",
    "32": "img/icon-32.png",
    "48": "img/icon-48.png",
    "128": "img/icon-128.png"
  },
  "host_permissions": ["http://*/*","https://*/*"]  // 접근 허용할 host
}
```

<br>
Ⅱ. popup.html 내용

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
</head>
<body>
    <input type="number" min="1" max="9" id="blurNum" />
    <button type="button" id="change">변경</button>

<script src="js/popup.js"></script>
</body>
</html>
```

<br>
Ⅲ. popup.js 내용

```
// blur 처리 할 농도 입력 값 
let blurNum = document.getElementById('blurNum');
// 변경 버튼
let change = document.getElementById('change');

// 1 ~ 9 까지 한자리 숫자만 허용
blurNum.addEventListener('keyup',function () {
    this.value = this.value.replace(/[^0-9]/,'');
    this.value = this.value.length > 1 ? this.value.substr(0,1) : this.value;
})

// 입력한 값으로 화면 블러 처리
change.addEventListener('click', async () => {
    let blur = '0.' + blurNum.value;

    // 해당 탭 SEARCH
    let [tab] = await chrome.tabs.query({ active: true, currentWindow: true });

    // 스크립트 ADD
    chrome.scripting.executeScript({
        target: { tabId: tab.id },
        function: setPageBackgroundBlur,
    });

    // storage sync 셋팅
    chrome.storage.sync.set({ blur: blur });
})

// storage sync 있는 blur 값으로 화면 blur 처리
function setPageBackgroundBlur() {
    chrome.storage.sync.get("blur", ({blur}) => {
        document.getElementsByTagName('html')[0].style.opacity = blur;
    });
}
```

<br>
생각보다 쉽게 확장프로그램을 만들수 있어서 구글에 다시 한번 놀란다....<br>
스크립트에서 ajax 사용도 가능하니 여러 개발로도 사용할 수 있을것 같다.