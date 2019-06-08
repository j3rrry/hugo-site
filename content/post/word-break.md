+++
title = "[CSS] 줄바꿈, 단어 단위로"
subtitle = "줄바꿈으로 인한 단어 잘림 현상 극-복"

# Add a summary to display on homepage (optional).
summary = "줄바꿈할 때 단어가 잘려나오면 보기 않좋다. CSS로 단어 단위로 맞춰보자"

date = "2019-05-10T02:37:18+09:00"
publishDate = "2019-05-10T02:37:18+09:00"
expiryDate = ""

# Authors. Comma separated list, e.g. `["Bob Smith", "David Jones"]`.
authors = []

# Tags and categories
# For example, use `tags = []` for no tags, or the form `tags = ["A Tag", "Another Tag"]` for one or more tags.
tags = ["word-break", "blog"]
categories = ["blog"]
keywords = ["word-break", "blog"]

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

```
/* Body text */
p {
  word-break: keep-all;
}
```
참고: https://www.codingfactory.net/10658  
  
문법
```css
word-break: normal | break-all | keep-all | inital | inherit;
```
* normal: 한글의 경우 문자 단위, 영어의 경우 단어 단위.
* break-all: 문자 단위
* keep-all: 문자 단위
* inital: 기본값
* inherit: 부모 요소의 속성값을 상속
