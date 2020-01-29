---
layout: post
title: "도커 이미지에서 Python 스크립트 실행시 UTF-8 출력 에러 해결하기"
tags: ["Docker"]
comments: true
---

도커 이미지에서 Python 스크립트 실행시 UTF-8 출력 에러 해결하기

---

## Question 
``` bash
'ascii' codec can't encode character '\u0410' in position 0: ordinal not in range(128)
```

최근 `Docker` 이미지에서 UTF-8 문자열을 다루는데, 위와 같은 에러를 뱉으면서 UTF-8 문자열을 출력할 수 없는 상황을 겪었었다. 
이 문제를 어떻게 해결할 수 있을까?

## Answer
``` bash
ENV PYTHONIOENCODING=utf-8
```

Dockerfile 안에 `ENV`로 `PYTHONIOENCODING` 을 UTF-8로 Set 해주면 해결된다 :)

---
참고 : <https://stackoverflow.com/questions/51424728/once-again-unicodeencodeerror-ascii-codec-cant-encode>
