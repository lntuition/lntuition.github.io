---
layout: post
title: "간단한 Chrome-extension 제작해보기 (1)"
tags: [Chrome-extension, Javascript, 네이버 스포츠]
comments: true
---

간단한 Chrome-extension 제작해보기 (1) - Manifest 편

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

<br>
다음에는 본격적으로 Javascript 코드 작성과 manifest 수정을 통해 크롬 익스텐션을 좀 더 확장해보도록 합시다.

---
참고 : [https://developer.chrome.com/extensions/getstarted](https://developer.chrome.com/extensions/getstarted)