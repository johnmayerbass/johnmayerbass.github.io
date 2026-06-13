---
title: Blog 제작기 Ep.02 - Github Page 배포
description: Hugo Server와 stack 테마를 이용한 블로그 제작
slug: blog_02
date: 2026-06-13T21:00:00+09:00
categories:
    - Blog
tags:
    - Blog  
comments: true
#draft: true

---
## Progress
### Github Pages 설정
- 깃허브 리파지토리 세팅에서 `Pages`로 이동해서 `Github Actions`를 선택해준다.

### Push
- 작성 글을 배포 한다.
    - main or master 여부는 `git branch` 명령어로 확인한다.
```bash
git add.
git commit -m "first commit"
git push origin master
```

### 배포 확인
- Github Actions 탭에서 배포 상태 확인
    - 성공 시 `https://johnmayerbass.github.io` 접속 후 확인


> 마치며

워드프레스, 노션블로그에서 삽질을하며 고생했던 경험이 도움이 됐던걸까? 

깃허브 블로그 구축은 큰 어려움 없이 비교적 간단하게 마무리가 되었다. 

물론 구체적인 세팅은 좀 더 필요하겠지만 껍데기 보다는 내용에 좀 더 집중하기 위해 이 정도에서 마무리 지어야겠다.

