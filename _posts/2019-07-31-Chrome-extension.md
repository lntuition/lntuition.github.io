---
layout: post
title: "간단한 Chrome-extension 제작해보기"
tags: [Chrome-extension, Javascript, 네이버 스포츠]
comments: true
---

간단한 Chrome-extension 제작해보기

---

## 시작하면서
회사에 입사하고 나서 한동안은 집-회사만을 반복하는 평일이 지나가더군요. 
덕분에(?) 학생때는 그다지 주기적인 삶을 살지 않았기때문에 챙겨보지 못했던 야구경기를 챙겨볼 수 있게되었습니다.
저는 주로 야구를 네이버 스포츠에 가서 보는데 TV 중계를 틀때마다 항상 광고가 나오곤 합니다. 
그럼 매번 Skip 버튼을 눌러줘야하죠. 
그리고 명색은 개발자인 저는 이런 생각을 합니다.
<center> <b> "이거 자동으로 Skip 해주는 소스를 짤 수 있지 않을까?" </b> </center>

## Manifest
크롬 익스텐션에서 일종의 config 역할을 하는것이 바로 manifest.json 파일입니다. 
Text editor를 이용하여 manifest.json 파일을 만든 후 다음과 같이 아래에 입력해 보죠.

```
{
    "name": "Chrome-extension practice",
    "version": "1.0",
    "description": "Chrome-extension practice",
    "manifest_version": 2,
    "content_scripts": [
        {
            "js": ["main.js"],
            "matches": ["https://sports.naver.com/*"],
            "run_at": "document_end"
        }
    ]
}
```

감이 좋으신 분들은 바로 아시겠지만, 위 Manifest를 통해서 우리가 만들어볼 크롬 익스텐션은 네이버 스포츠에 들어갔을때 document의 로딩이 끝난 후(DOM이 완료된 직후)에 main.js를 실행할 것입니다.
일단은 간단하게 main.js에 `console.log("Hello World");` 를 입력한 후 위에서 만든 manifest와 같이 폴더에 넣어서 저장해 둡시다.

## 개발자 모드로 시작하기
그럼 이제 만들어진 크롬 익스텐션을 크롬에 넣어봅시다.

1. 먼저 [chrome://extensions/](chrome://extensions/) 에 들어가서 우측 상단에 있는 개발자 모드를 On 으로 바꿉시다.
1. 좌측 상단의 **압축해제된 확정 프로그램을 로드합니다** 를 클릭한 후 manifest가 들어있는 폴더를 선택합니다.
1. 아래에 manifest의 title이 적혀있는 탭의 새로고침 버튼을 통해 변경사항을 저장할 수 있습니다.

## 테스트하기
이제 우측 상단에 크롬 익스텐션이 추가 됨을 확인하였습니다.
이제 네이버 스포츠를 접속한 후에 F12를 눌러 개발자 도구를 확인해보면 콘솔창에 Hello World가 찍혀 있는 것을 확인해 볼 수 있습니다.
manifest 에서 우리가 네이버 스포츠에 접근했을 시에만 main.js를 수행하도록 정의해 놓았으므로 다른 사이트에 들어갈 경우에는 콘솔창에 아무것도 뜨지 않습니다.

<center>
    <img src="/images/Chrome-extension-1-console.png">
    <b> "이제 크롬 익스텐션을 만들 준비가 완료되었습니다" </b>
</center>

## Main.js
이제 우리는 광고를 스킵하는 코드를 작성하기만 하면됩니다.
제가 생각했을때의 가장 좋은 방법은 **광고 스킵버튼이 등장했을 때 자동으로 광고 버튼을 클릭한다** 였습니다.

하지만 정확히 광고 스킵버튼이 등장했을 때를 캐치하는건 Client 사이드에서는 쉽지 않았습니다.
그래서 방향을 조금 바꾸어서 **광고 스킵버튼이 나타날때 까지 광고 버튼을 클릭** 하는 코드를 작성해 보았습니다.

``` javascript
let autoClickFunc = setInterval(() => {
    let button = document.getElementsByClassName("skipBtn").item(0);
    try {
        if (button.style.display != "none") {
            button.click();
            console.info("Button clicked successfully !");
            clearInterval(autoClickFunc);
        }
    }
    catch (err) {
        if (err.name != "TypeError") {
            console.err(err);
        }
    }
}, 300);

let autoClearFunc = setTimeout(() => {
    clearInterval(autoClickFunc);
}, 30000);
```

0.3s 마다 Skip 버튼을 눌러보는 간단한 코드를 작성하게 되었습니다.
멍청한 코드지만 정상적으로 작동해서 Skip 버튼을 누르는 대신 콘솔창에 Button clicked successfully 를 띄워줍니다.
그런데 뭔가 아쉽네요...

## 확장하기
지금 여기서 더 해볼 수 있는게 무엇일까 생각했을 때 가장 먼저 생각난 기능은 **On/Off 기능** 이였습니다.
해당 기능을 구현하고 조작할 수 있는 인터페이스에 대한 시나리오를 먼저 간단하게 아래와 같이 그려봤습니다.

<center> <img src="/images/Chrome-extension-2-scenario.png"> </center>

## Manifest
```
{
    "name": "Chrome-Auto-scriptor",
    "version": "1.0",
    "manifest_version": 2,
    "content_scripts": [
        {
            "js": ["js/main.js"],
            "matches": ["https://sports.naver.com/*"],
            "run_at": "document_end"
        }
    ],

    "background": {
        "scripts": [
            "js/init.js"
        ],
        "persistent": true
    },
    "browser_action": {},
    "options_ui": {
        "page": "html/option.html",
        "open_in_tab": true
    },
    "permissions": [
        "storage"
    ]
}
```

처음에 작성하였던 Manifest를 위의 시나리오를 구현할 수 있도록 위와 같이 수정하였습니다. 
Option.html 을 통해 사용자 인터페이스를 구현하고 Chrome storage 를 사용하여 현재 Status를 저장할 수 있도록 하였습니다.

## 인터페이스
<center>
    <img src="/images/Chrome-extension-2-interface.png">
    <b> "On 상태를 버튼이 눌린 상태로 표현하여 직관적인 인터페이스를 지향했습니다"</b>
</center>

## Chrome Storage
기능이 On/Off 되어있는지에 대한 상태값을 저장하기 위해서 [Chrome Storage API](https://developers.chrome.com/extensions/storage)를 사용하였습니다.
인터페이스가 켜질때 Chrome storage 에서 값을 가져와서 버튼의 상태를 표시하고 버튼을 클릭할 때 Chrome storage의 값을 수정하였습니다.

``` javascript
(function () {
    // Set page with option value
    const keys = ["isNaverSportsAutoSkip"];
    chrome.storage.sync.get(keys, (result) => {
        for (let i = 0; i < keys.length; ++i) {
            let key = keys[i];
            if (result[key]) {
                document.getElementById(key).classList.add("is-info");
            }
        }
    });

    // Set onclick event
    for (let i = 0; i < keys.length; ++i) {
        let key = keys[i];
        let button = document.getElementById(key);
        button.addEventListener("click", event => {
            button.classList.toggle("is-info");
            chrome.storage.sync.set({ [key]: button.classList.contains("is-info") });
        });
    }
}());
```

또한 Background 에서 호출하는 스크립트에는 Chrome storage 에 해당 key 값의 value가 없을 경우(ex : 익스텐션을 처음 설치하였을 경우)를 대비하여 Default 값을 Set 하는 코드를 작성하였습니다.

``` javascript
(function () {
    // Add Option.html open event
    chrome.browserAction.onClicked.addListener(tabs => {
        chrome.runtime.openOptionsPage();
    });

    // Set default option value
    const keys = ["isNaverSportsAutoSkip"];
    chrome.storage.sync.get(keys, (result) => {
        for (let i = 0; i < keys.length; ++i) {
            let key = keys[i];
            if (result[key] === undefined) {
                chrome.storage.sync.set({ [key]: true });
            };
        }
    });
})();

```

위의 코드들에 대해서 길게 설명을 하지 않는 이유는 제가 만들어논 시나리오를 해결하기 위해 구현한 스크립트기 때문입니다.
혹시나 위에 링크해 놓은 크롬 공식 개발 문서를 보고도 위의 소스가 잘 이해가 가지 않는다면 댓글에 이해가 가지 않는 점을 적어주시면 좀 더 자세히 설명 드리겠습니다 :)

## 끝내면서
사실 글 쓰면서 느끼는건데 그렇게 어려운 점은 많지 않음에도 불구하고 적지 않는 시간이 걸린것 같네요.
말만 번지르르한 **개발자**에서 벗어나기 위해 부단히 노력해야겠습니다.
전체 소스가 들어있는 [Github](https://github.com/lntuition/Toy-Project/tree/master/Chrome-Auto-Scriptor/)는 여기로 들어가실수 있구요, 
Publish된 [Extension](https://chrome.google.com/webstore/detail/chrome-auto-scriptor/ajemfnoiodjboeloinfigmooikjbcccm)은 여기서 확인할 수 있습니다.

<center> <b> "사소한 것에서 또 다른 재미있는 Toy Project를 찾아서 오겠습니다" </b> </center>

---
참고 : [https://developer.chrome.com/extensions/getstarted](https://developer.chrome.com/extensions/getstarted)
