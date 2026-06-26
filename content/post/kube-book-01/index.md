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

실무에서 도커를 사용하다보면 항상 따라오는게 쿠버네티스이다. 우리 회사 서비스 규모에서는 굳이 쿠버네티스를 사용할 환경이 아니지만 실무와는 별개로 도커는 아는데 쿠버네티스는 모른다는게 항상 맘에 걸렸었다. 그래서 서점에서 가장 쉬워보이는 쿠버네티스 책으로 쿠버네티스 학습을 시작했다.   

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

## 5.3 kubectl 명령어로 상세 정보 출력하기
### 디버그용 사이드카 컨테이너 : kubectl debug
```bash
# 명령어
kubectl debug --stdin --tty <디버그 대상 pod 이름> --image=<디버그용 컨테이너 이미지> --target=<디버그 대상의 컨테이너 이름> --namespace default -- sh

# 사용 예
kubectl debug --stdin --tty myapp --image=curlimages/curl:8.4.0 --target=hello-server --namespace default -- sh
```

### 디버깅용 pod 생성
- 클러스터 내부에서 IP 주소로 접속이 되는지를 확인한다.
```bash
# 로그인용 pod 생성
kubectl run curlpod --image=curlimages/curl:8.4.0 -- sleep infinity

# 확인
kubectl get pod

NAME      READY   STATUS    RESTARTS      AGE
curlpod   1/1     Running   0             11m

# 접속할 pod의 ip 주소 확인
kubectl get pod myapp -o wide -n default

# 로그인용 pod에 로그인
kubectl -n default exec --stdin --tty curlpod -- /bin/sh
or
kubectl exec -it curlpod -- sh

# 통신 확인
curl <myapp Pod의 IP>:8080
```

### kubectl edit을 통한 매니페스트 편집
- 간단히 수정할순 있지만 가급적 정식 배포 절차를 거쳐 파일을 수정하자.
```bash
kubectl edit pod myapp -n default

# 변경사항 확인
kubectl get pod myapp -o yaml -n default
```

## 편리한 터미널 사용을 위한 팁
### kubectl 별명 설정
```bash
# alias 설정
alias k=kubectl
```
### 약어 확인
```bash
kubectl api-resources
```

