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

## 2.2 쿠버네티스 클러스터 만들고 지우기
- 설치시에는 현재 환경에 맞게 설치를 하여야 한다.
  - `uname -m` 명령어로 환경을 확인한다.

- kubectl 설치
```bash
# 설치
sudo curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/arm64/kubectl"

sudo chmod +x kubectl
sudo mv kubectl /usr/local/bin/

# 확인
kubectl version --client

# 결과
Client Version: v1.36.2
Kustomize Version: v5.8.1
```

- kind 설치
> kind란?

Kubernetes IN Docker. 즉, Docker 컨테이너 안에 Kubernetes 클러스터를 만들어주는 도구이다. Docker 컨테이너를 가짜 서버처럼 사용한다.

```bash
# 설치
sudo curl -Lo ./kind https://kind.sigs.k8s.io/dl/latest/kind-linux-arm64

chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind

# 확인
kind version

# 결과
kind v0.33.0-alpha+a3f5175037b51b go1.26.3 linux/arm64
```

## 4.2 Pod 만들기
### 쿠버네티스 클러스터 구축
```bash
# 이미지를 지정하여 클러스터 구축
kind create cluster --image=kindest/node:v1.29.0

# 연결 확인
kubectl cluster-info --context kind-kind
```

### Pod 만들기 전 쿠버네티스 클러스터 준비 확인
```bash
kubectl get nodes

# 결과
NAME                 STATUS   ROLES           AGE   VERSION
kind-control-plane   Ready    control-plane   10m   v1.29.0
```

### 매니페스트 사용
> 매니페스트란?

쿠버네티스에게 원하는 상태를 선언하는 YAML 파일  
➡️  일종의 설계도
```bash
# myapp.yml
apiVersion: v1
kind: Pod
metadata:
  name: myapp
  labels:
    app: myapp
spec:
  containers:
    - name: hello-server
      image: blux2/hello-server:1.0
      ports:
        - containerPort: 8080
```

### 매니페스트를 쿠버네티스 클러스터에 적용
```bash
# 적용 전 Pod가 없다는 것을 확인
kubectl get pod --namespace default

# 결과
No resources found in default namespace.

# 매니페스트 적용
kubectl apply --filename chapter-04/myapp.yml --namespace default

# Pod 확인
kubectl get pod --namespace default

# 결과
NAME    READY   STATUS    RESTARTS   AGE
myapp   1/1     Running   0          9m22s
```

## 5.2 kubectl 로 현황 파악하기
### 리소스 확인하기
```bash
kubectl get pod -n default

# output 옵션
kubectl get pod -o wide -n default

# yaml 형식으로 리소스 정보 출력
kubectl get pod myapp -o yaml -n default
```

### 리소스 상세정보 출력
```bash
kubectl describe pod myapp -n default
```

### 컨테이너의 로그 출력
```bash
kubectl logs myapp -n default
```