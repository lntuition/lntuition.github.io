---
layout: post
title: "Forked repository 최신화 하기"
tags: ["Git"]
comments: true
---

Github 에서 Forked repository 최신화 하기

---

## Question 
Github 에서 fork 한 repository를 최신화 하려면 어떻게 해야할까?
(매번 까먹어서 검색하는 것 대신에 정리를 해놓으려고...)

## Answer
``` bash
git remote add upstream {ORIGIN REPOSITORY}
git fetch upstream
git merge upstream/master
git push ...
```

원본 repository를 upstream으로 등록해 준 뒤, `fetch` -> `merge`를 한 후 다시 remote로 올려준다.
뜬금 없는 소리긴 한데 `pull` = `fetch` + `merge` 라는 점 혹시 맨 처음에 아무것도 모른채 사용한 사람이 나 혼자만은 아니겠지 :(

---
참고 : <https://json.postype.com/post/210431>
