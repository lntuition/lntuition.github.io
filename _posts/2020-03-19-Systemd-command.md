---
layout: post
title: "자주 쓰는 Systemd 명령어들"
tags: ["Linux"]
comments: true
---

자주 쓰는 Systemd 명령어들

---

## 개요
최근 일을 하면서 systemd를 쓸일이 꽤나많은데 생각보다 명령어에 익숙하지가 않다...
systemd에 대해서는 추후에 한번 더 공부후 정리를 하는 것으로 하고 여기서는 간단히 자주 쓰는 명령어들만 일단 정리해 놓으려고 한다.

## 명령어
| 개요 | 명령어 | 비고|
|------|-------|-----|
| 실행 |`systemctl try-restart ...`  | 기초적인 start, stop 보다는 active 일때만 재시작하는 기능 |
| 조회 |`systemctl list-units ...`   | grep 명령어와 조회해서 사용하면 매우 유용하다 |
| 상태 |`systemctl status ...`       | 말할 필요가 없다...  |
|의존성|`systemctl list-dependencies`| --after와 --before를 적절히 사용하면 매우 유용하다 |

---
참고 : <https://nickjoit.tistory.com/142>
