---
title: "Host On Github"
date: 2019-02-20T06:55:23Z
description: ""
categories: []
tags: []
featuredImage: ""
featuredImageDescription: ""
dropCap: true
displayInMenu: false
displayInList: true
---

Github에 Publish하는 방법은 3 가지가 있다.

1. master
2. gh-pages
3. master의 /docs

# master로 Publish하기
제일 간단한 방법이다.
만약에 Repo 이름을 `username.github.io`로 지었다면 이미 Publish 됐을 것이다.
(Github에서 Refresh하는 시간이 조금 걸리긴 한다..)

Repo 이름을 `ProjName` 으로 했다면 `username.github.io/ProjName`으로 Publish 된다.

# gh-pages로 Publish하기
따로 branch를 만들어야한다.
```
git branch -b gh-pages
```
master branch는 더 이상 필요가 없다.

# /docs로 Publish하기
Jekyll은 Github Page에서 자체적으로 빌드를 지원해준다.[^jekyll's build process]
그러나 이 Publishing 방법은 주로 Hugo나 Hexo같이 Github Page에서 자체 빌드를 지원하지 않는 Static Site Generatoer들이 고려해 볼만하다.
Repo의 최상위 경로에 `/docs` 폴더를 Publish 하는 것이다.
오직 master branch에서만 가능하다.

[^jekyll's build process]: https://help.github.com/articles/about-github-pages-and-jekyll/#jekylls-build-process
