---
layout: post
title: "Pull Request를 local로 가져오기"
tags: ["Git"]
comments: true
---

Github에서 Pull Request를 local로 가져오기

---

## Question 
Github에서 Pull Request를 local로 가져오려면 어떻게 해야할까?
(매번 까먹어서 검색하는 것 대신에 정리를 해놓으려고...)

## Answer
``` bash
git pull origin pull/{PR NUMBER}/head:{BRANCH NAME}
```

PR 에 대한 브랜치는 원격 저장소의 `refs/pull/{PR NUMBER}/head` 로 존재하고 있다.
즉 기존에 연결되어 있던 `origin` 원격저장소의 브랜치만 땡겨오면 쉽게 해결할 수 있다 !

---
참고 : <https://blog.outsider.ne.kr/1204>
