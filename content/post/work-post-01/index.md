---
title: 🖥 폐쇄망 환경에서 네트워크 진단 도구가 필요할 때
description: Netshoot 이미지 활용
slug: work_01
date: 2026-06-26T14:20:00+09:00
categories:
    - Work
tags:
    - Work
    - Docker
comments: true
#draft: true

---

![그림과 실습으로 배우는 쿠버네티스](kube-book.png)

# Netshoot 

> 네트워크 문제를 진단하기 위한 도구들이 모두 들어있는 Docker 이미지

> 필요성

업무 특성상 폐쇄망 서버에서 작업을 하는 경우가 많은데 네트워크 상태를 확인하기 위해 명령어를 날리면 서버 자체에 `ping`, `curl`, `nc`등이 설치 되어있지 않은 경우가 종종 있다. 그러다보니 뭔가 방법이 없나 찾아보던중 네트워크 진단 도구만을 모아놓은 도커 이미지를 발견했다. 

## Docker Hub
https://hub.docker.com/r/nicolaka/netshoot

## Version
v0.15

## 실행
```bash
docker run --rm -it --net host --cap-add NEW_ADMIN --cap-add NET_RAW <이미지>
```


