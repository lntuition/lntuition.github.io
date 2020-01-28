---
layout: post
title: "하위 폴더의 내용 일괄 수정하기"
tags: ["Linux"]
comments: true
---

하위 폴더의 내용 일괄 수정하기

---

## Question 
전체 폴더를 돌면서 내용을 수정해야할 경우, 어떻게 하면 하위 폴더의 내용을 일괄로 수정할 수 있을까?

## Answer
``` bash
find . -name "{NAME}" -exec perl -pi -e 's/{FROM}/{TO}/g' {} \;
```
`find` 명령으로 해당하는 파일들을 찾아준 후, Regex를 통해 해당 내용을 변경해주면 된다 :)

---
참고 : <https://recipes4dev.tistory.com/156>
