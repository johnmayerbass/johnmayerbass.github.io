---
title: Blog 제작기 Ep.01 - Hugo Server & 로컬 환경 테스트
description: Hugo Server와 stack 테마를 이용한 블로그 제작
slug: blog_01
date: 2026-06-13T13:00:00+09:00
categories:
    - Blog
tags:
    - Blog  
comments: true
#draft: true

---

## Motivation
> 블로그 시작 이유

어느덧 이 바닥에서 경력을 시작한지 약 3년이란 시간이 흘렀다.<br>
그 동안 후임들도 들어오고 이제 슬슬 일이 어떻게 돌아가는지 보이기도 한다.<br>

최근 후임이 들어와서 느낀 것중 하나가 내가 그동안 일을 하면서 중간중간 이슈 사항이 있을 때마다 개인 <kbd>Notion</kbd> 블로그에 정리해 놓았던 것들이 누군가에게는 꽤나 도움이 된다는 것이다.

그리고 무언가를 공유하는 과정에서 나도 같이 성장하고 더 배우게 된다.

AI가 범람하는 이런 시대에도 말이다.

그래서 그 동안 귀찮다는 핑계로 미뤄왔던 깃허브 블로그를 이제는 정말 시작해야 할 때라고 느꼈다.

> 많은 플랫폼중 깃허브 블로그 선택 이유

사실 내가 귀찮다는 핑계로 미뤄왔다고는 했지만 그동안 많은 시도를 해왔다.

티스토리, 워드프레스 그리고 노션블로그까지..여러 플랫폼에서 블로그를 시작했었다. 단지 지속성이 부족했을 뿐.

처음에는 그동안 개인기록용으로 사용하던 노션 플랫폼이 익숙해서 노션블로그로 마음이 기울었고 심지어 제작까지 다 해놨지만 깃허브 블로그로 최종 결정한 이유는 깃 그리고 깃허브와 친숙해지고 싶어서였다.

또한 플랫폼에 대한 약간의 불신? 때문이기도 하다.  물론 노션이 그럴일은 없으리라 생각하지만<br>

워드프레스로 구축했었던 블로그의 어드민 계정이 날라가서 한창 열심히 썼던 글이 사라졌던 경험을 해보니 데이터 주체가 내가 아니면 어딘지 모르게 약간 불안한 마음이 있다.😂

마찬가지로 노션 블로그는 게시글마다 별도로 백업을 하지 않는 이상 작성된 글은 해당 노션 계정에 귀속이 되지만<br>
깃허브 블로그에 업로드 한 글은 일일이 백업을 하지 않아도 어찌됐든 내 로컬에 md 파일로 남아있으니 조금 더 안심이 됐다.

## Progress
### Github Repository 생성
- 깃헙 로그인 후 `아이디.github.io` 형태의 이름으로 리파지토리를 생성했다.

### Hugo 설치
- 맥 기준으로 Homebrew 사용해서 설치 했다.
```bash
brew install hugo
```

- 설치 확인
```bash
hugo version
```

### Repository Clone
- 로컬에서 원하는 로컬 디렉토리로 이동 후 클론 명령어 실행
```bash
git clone https://github.com/CaiJimmy/hugo-theme-stack-starter.git blog
```

### Github 연결 변경
```bash
git remote remove origin

git remote add origin https://github.com/johnmayerbass/johnmayerbass.github.io.git
```

### 사이트 정보 수정
- `config/_default/xx.toml` 설정 파일 수정

### 첫 글 작성 및 로컬 테스트
- 게시글 작성
```bash
# docker-swarm-recovery 이름의 디렉토리에 index.md 파일 생성
hugo new post/docker-swarm-recovery/index.md
```
- hugo 서버 가동 및 확인
```bash
# 서버 가동
hugo server
# 접속
http://localhost:1313
```













