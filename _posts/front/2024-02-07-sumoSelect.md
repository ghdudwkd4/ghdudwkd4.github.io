---
title: Multi Select Box Plugin - sumoSelect
layout: post
categories: front
order: 3
data: 2024-02-07 17:50
---

### Multi Select Box Plugin - sumoSelect

사이드 프로젝트를 진행하며 한개의 데이터를 입력 할 때 <br>
특정 컬럼이 여러개 들어가야 되는 상황이 있었다. <br>
다양한 방법이 있겠지만 사용자 입장에서 편리한 방법을 생각하다가 <br>
select 태그 안에서 여러개의 데이터를 선택 및 검색을 할 수 있으면 좋겠다고 생각했다. <br>
역시나 구글에 검색하니 sumo select 라는 플러그인이 이미 있었다. <br>
select tag 안에서 여러개의 데이터 선택과 <br>
초기화, 전체선택, 검색 등 여러가지 기능이 있는데 <br><br>

자세한건 공홈에서 찾아보자 [sumoSelect](https://hemantnegi.github.io/jquery.sumoselect) <br>

이용 방법은 비교적 간단하다. <br>

```
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/jquery.sumoselect/3.4.9/sumoselect.min.css"/>
<script src="https://cdnjs.cloudflare.com/ajax/libs/jquery.sumoselect/3.4.9/jquery.sumoselect.min.js"></script>

---------------------------------------------------------------
script
$('#book').SumoSelect({
    search: true,
    clearAll:true,
    placeholder: '검색어를 입력해주세요',
});
---------------------------------------------------------------
html

<select id="sumo" multiple></select>
---------------------------------------------------------------
```

<br>
select tag 에 multiple 은 필수가 아니니 참고! <br>
multiple 없을 시 다중 선택 X <br><br>

여러가지 옵션과 이벤트가 있으니 공홈에서 참고! <br>

#### 옵션
```
{
    placeholder: 'Select Here',
    csvDispCount: 3,
    captionFormat: '{0} Selected',
    captionFormatAllSelected: ''{0} all selected!'',
    floatWidth: 500,
    forceCustomRendering: false,
    nativeOnDevice: ['Android', 'BlackBerry', 'iPhone', 'iPad', 'iPod', 'Opera Mini', 'IEMobile', 'Silk'],
    outputAsCSV : false,
    csvSepChar : ',', 
    okCancelInMulti: true, 
    isClickAwayOk: false,
    triggerChangeCombined : true,
    selectAll : false,
    selectAllPartialCheck : true,
    search : false,
    searchText : 'Search...',
    searchFn : function(haystack, needle, el){ ... },
    noMatch : 'No matches for "{0}"',
    prefix : '',
    locale :  ['OK', 'Cancel', 'Select All'],
    up : 'false',
    showTitle : 'true',
    max : null,
    renderLi : (li, originalOption) => li,
}
```

<br>

#### 이벤트
```
// Drop down initialized.
sumo:initialized
// Drop down opening
sumo:opening
// Drop down opened
sumo:opened
// Drop down closing
sumo:closing
// Drop down closed.
sumo:closed
// Drop down unloaded.
sumo:unloaded

$('#sumo').on('sumo:opened', function(sumo) {
    // Do stuff here
    console.log("Drop down opened", sumo)
});
```