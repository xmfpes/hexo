---
title: Hexo를 이용해 Github 블로그 생성하기
date: 2017-09-19 15:08:16
tags: 
- Hexo
- Blog
- GitHub
- Node.js
categories: Hexo
thumbnail: /images/hexo.png
---

## **Node.js Hexo를 이용해 블로그 제작하기**

![hexo](/images/hexo.png)

Hexo와 Github를 이용해 개인 블로그를 생성하고 관리해보자.
Hexo는 Node.js 기반으로 되어 있다.

## STEP 1. Hexo CLI 설치 및 블로그 생성
```bash
$ npm install hexo-cli -g
$ hexo init blog
$ cd blog
$ npm install
```
## STEP 2. _config.yum 설정 파일 수정
설정 파일을 통해 사이트 정보, git 정보 등을 셋팅할 수 있다.

사이트 정보
```yum
# Site
title: myBlogName
subtitle: myBlogSubTitle
description: myBlogDescription
author: my Name
language:
timezone:
```

github 설정 정보
```
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repo: https://github.com/xmfpes/xmfpes.github.io.git
  branch: master
```


## STEP 3. Custom Theme 적용 및 disqus 적용

overdose 테마를 사용해서 블로그 테마를 적용한다.
테마를 클론 해 온다.

overdose 테마는 jade/sass를 사용하므로 설치
```
$ git clone https://github.com/HyunSeob/hexo-theme-overdose.git themes/overdose

$ npm install --save hexo-renderer-jade hexo-renderer-bourbon
```

```
$ cd themes/overdose;npm run clone
```

```
$ cp _config.yml.example _config.yml
```

셋팅 완료 후 root _config.yml 파일에서 테마 수정
```
theme: overdose
```

disqus 댓글 적용을 위해 root _config.yml 파일에서 disqus shortname 지정
```
# _config.yml of base, not theme config
# Please put your shortname of disqus here.
disqus_shortname: your_disqus_shortname
```
## STEP 4. github에 배포하기

/blog 경로에서
hexo-deployer-git 패키지 설치
```bash
npm install hexo-deployer-git --save
```

Hexo generate 통해 정적 리소스 생성
```
$ hexo generate
```
배포
```
$ hexo deploy
```

아래와 같이 Generate와 Deploy를 동시에 실행 할 수도 있다
```
$ hexo deploy --generate
```

## STEP 4. 포스팅 및 카테고리, 태그 생성
아래의 명령어를 통해 새 글을 생성
```
hexo new "Title"
```
/source/_posts 경로에 해당 글 md파일이 생성된다.

태그와 카테고리 적용은 아래와 같이 하고, 하단부에 글의 내용을 작성한다
```
---
title: Hexo 블로그 만들기
date: 2017-09-19 14:30:46
tags: Hexo
categories: Hexo
---

```