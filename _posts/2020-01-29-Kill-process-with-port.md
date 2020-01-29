---
layout: post
title: "해당 포트를 사용하는 프로세스 일괄 끝내기"
tags: ["Linux"]
comments: true
---

해당 포트를 사용하는 프로세스 일괄 끝내기

---

## Question 
``` bash
ERROR: for container  Cannot start service container: 
driver failed programming external connectivity on endpoint container
...
Error starting userland proxy: listen tcp 0.0.0.0:5432: bind: address already in use
```

최근 `Docker`를 사용하다가 컴퓨터를 빡종(!) 하면 위처럼 재부팅 후에도 도커로 열어놓은 프로세스가 포트를 점유하고 있는 경우를 발견했다.
`Docker ps` 로는 검색이되지 않던데, 이 문제를 어떻게 해결할 수 있을까?

## Answer
``` bash
sudo lsof -t -i {PORT} | xargs sudo kill -9
```
`lsof` 명령으로 해당 포트를 점유한 프로세스를 찾아준 후, `xargs`로 `kill` command 에 넘겨준다.

---
참고 : <https://lureout.tistory.com/646>
