+++
title = "Quickstart Hugo Academic Theme"
subtitle = ""

# Add a summary to display on homepage (optional).
summary = ""

date = "2019-02-25T20:25:20+09:00"
publishDate = "2019-02-25T20:25:20+09:00"
expiryDate = ""

# Authors. Comma separated list, e.g. `["Bob Smith", "David Jones"]`.
authors = []

# Tags and categories
# For example, use `tags = []` for no tags, or the form `tags = ["A Tag", "Another Tag"]` for one or more tags.
tags = ["hugo", "quickstart"]
categories = ["hugo"]
keywords = ["hugo", "quickstart"]

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["deep-learning"]` references 
#   `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
# projects = ["internal-project"]

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder. 
[image]
  # Caption (optional)
  caption = ""

  # Focal point (optional)
  # Options: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight
  focal_point = ""

draft = false
+++

본 포스팅에서는 hugo-academic 테마를 이용하여 Github Page에 Publishing 하는 방법을 간단하게 다룹니다.

```
$ hugo new site my-site
$ cd my-site
$ git init
$ git submodule add https://github.com/gcushen/hugo-academic.git themes/academic
$ cp -r themes/academic/exampleSite/. .

$ # Test http://localhost:1313
$ hugo server -D # -b "192.168.0.2" --bind="0.0.0.0"

$ # github에 commit하기
$ git remote add origin https://github.com/j3rrry/my-site.git
$ git add .
$ git commit -m 'first commit'
$ git push origin master
```
<br>
# Publishing
사이트를 Publishing 하기 위해서 `config/_default/config.toml` 을 수정해 줘야합니다.
```toml
baseURL = "https://j3rrry.github.io/"
theme = "academic"
```
제일 중요한 두 가지가 `baseURL`[^conf]과 `theme`.

* `baseURL`은 url을 넣어주면 된다. (Academic v4.0.0은 url 작성 시 맨 뒤에 `/`를 추가하도록 하고 있다.)  
* `theme`를 지정해주지 않으면 Hugo 기본 테마가 적용된다. `themes/` 디렉토리 안에 있는 테마 이름과 동일하게 지정해주면 된다.

Github Page에서는 세 가지 방법으로 Publishing 할 수 있습니다.[^gh-publishing]
그 중에서 첫 번째 방법인 `master` branch로 Hugo site를 Publishing 해보겠습니다.

* `j3rrry.github.io`를 이름으로 갖는 레포지토리 만들어 놓기

먼저 빌드를 합니다.
```
$ hugo
$ ls public/
```
`public/` 디렉토리 안에 빌드된 사이트가 만들어진다. 빌드된 것을 Publishing 하는 것이다.

```
$ cd public/
$ git init
$ git remote add origin https://github.com/j3rrry/j3rrry.github.io.git
$ git add .
$ git commit -m 'first commit'
$ git push origin master
```
앞으로도 계속 새로운 포스팅을 하거나 테마를 수정한 다음에는 빌드한 후 `j3rrry.github.io` 레포지토리에 commit을 하면 `https://j3rrry.github.io`에 적용될 것이다.

<br>

<hr>
참고1: https://gohugo.io/getting-started/quick-start/  
참고2: https://sourcethemes.com/academic/docs/install/  

[^conf]: https://gohugo.io/getting-started/configuration/#all-configuration-settings
[^gh-publishing]: https://help.github.com/en/articles/configuring-a-publishing-source-for-github-pages
[Netlify]: https://netlify.com
