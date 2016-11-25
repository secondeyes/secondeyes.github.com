# 우아한형제들 개발자 블로그

http://woowabros.github.io/
  
  
  
## 환경설정

개발자 블로그는 마크다운으로 작성하며, 작성된 문서는 jekyll을 통해 정적 블로그(웹사이트)로 만들어집니다.  
따라서 컨텐츠의 작성/배포에는 jekyll의 설치가 선행되어야 합니다.  
> html이나 다른 파일 형식도 컨버터 설치를 통해 사용할 수 있으므로 편한 쪽을 선택하면 되지만 마크다운을 권장합니다.

#### Jekyll의 설치

맥 OS에는 루비가 설치되어있으므로 아래의 명령을 터미널에 입력해줍니다.

```
$ gem install jekyll
```
  
  
  
## 작성하기

#### 로컬 서버 

jekyll은 로컬 브라우저에서의 미리보기를 지원하며, 서버를 띄운 동안 별도로 빌드하지 않아도 자동으로 변경사항을 반영합니다.

**http://localhost:4000/ 에서 로컬에서 컨텐츠를 확인**
```
$ jekyll serve 
```

**http://0.0.0.0:4000  => 특정 IP에서 컨텐츠 확인**
```
$ jekyll serve --host=0.0.0.0
```

#### 저자 등록

저자는 '_data'의 authors.yml 파일에 추가합니다.  
저자 키워드 하위에 저자 이름, 이메일, 그라바타(선택적) 등의 정보를 저장합니다.

```
저자 키워드:
  name: 저자명
  email: author@woowahan.com
  gravar: 
  ...
``` 
[그라바타 등록](https://ko.gravatar.com/)을 할 경우, 등록한 계정을 MD5로 암호화 해줍니다.

#### 컨텐츠 작성

컨텐츠는 '_posts' 경로에 마크다운 파일로 작성하며, 파일명은 YYYY-MM-DD-title.md 형식으로 저장합니다.
* [마크다운 문법](https://guides.github.com/features/mastering-markdown/)  

컨텐츠 상단에 등록할 글의 정보를 입력합니다.  

```
---
layout: post
title:  "컨텐츠 타이틀 - 블로그 메인에 노출"
description: "컨텐츠 설명 - 블로그 메인에 노출"
author: 'authors.yml'에 저장한 저자 키워드
date:   YYYY-MM-DD HH:MM:SS +0900 
categories: "카테고리명"
published: true
---
```
  
  
  
## 추가정보

#### FAQ
* date를 통해 글을 발행할 시점을 정할 수 있습니다.  
* 미래 시점으로 저장하면 published 속성이 true여도 글이 노출되지 않습니다.  
* 글 작성 시 줄바꿈은 문장 끝에 두 개의 스페이스를 넣으면 됩니다.  
* 첨부할 이미지가 있는 경우 'img' 경로에 추가합니다. 
* [페이스북에 링크 공유시 링크 미리보기에 404가 표시된다면?](http://kunny.github.io/etc/2016/02/11/fix_broken_link_in_facebook/)

#### 개인 블로그에서의 활용

위의 내용은 github에 개인 블로그를 생성/운영할 때에도 활용할 수 있습니다.   
> 1개의 계정 당 1개의 github blog를 가질 수 있습니다.  

자세한 가이드는 아래 링크를 참고해주세요.

* [GitHub Pages](https://pages.github.com/)
* [Jekyll Quick-start guide](https://jekyllrb.com/docs/quickstart/)
