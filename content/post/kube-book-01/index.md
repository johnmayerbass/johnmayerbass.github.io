---
title: 📕 그림과 실습으로 배우는 쿠버네티스
description: 쿠버네티스에 입문해보자 
slug: book_02
date: 2026-06-21T07:15:00+09:00
categories:
    - Book
tags:
    - Book
    - Kubernetes
comments: true
#draft: true

---

![그림과 실습으로 배우는 쿠버네티스](kube-book.png)

# 그림과 실습으로 배우는 쿠버네티스
> 이 책을 읽게된 계기

## 1.3 나만의 http server 컨테이너 만들어보기
- 멀티스테이지를 활용한 Dockerfile
```bash
FROM golang:1.21 AS builder
WORKDIR /app
COPY . .

ENV CGO_ENABLED=0
RUN go build -o hello

FROM scratch
COPY --from=builder /app/hello /hello
ENTRYPOINT ["/hello"]
```

- 멀티스테이지는 비유하자면 완성품만 넣는것이다.
```bash
COPY --from=builder /app/hello /hello
```
의미는 공장에서 만든 hello 실행파일만 빈 상자로 옮겨는 것과 비슷하다.

---

- 최종 이미지
```bash
/
 └─ hello
```

---

> 왜 이렇게 만드는가?

멀티 스테이지가 아닌 방식으로 그냥 만들면 최종 이미지 안에
1. Go SDK
2. Go 컴파일러
3. 빌드 도구
4. 소스코드 

전부 들어간다. -> 크기가 1GB 가까이 됨

하지만 멀티 스테이지를 쓰면 크기가 5~20MB 수준이다.

> 정리

Go 개발 환경(golang:1.21)에서 프로그램을 빌드하고, 완성된 실행파일만 빈 컨테이너(scratch)에 넣어 배포하기 위해서 이렇게 작성한다.

⬇️

"아, 빌드용 컨테이너와 운영용 컨테이너를 분리해서 이미지를 작고 안전하게 만들려는 거구나."