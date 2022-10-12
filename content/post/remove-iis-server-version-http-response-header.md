+++
title = "[IIS] 헤더 서버 정보 누출 숨기기"
subtitle = "IIS의 응답 헤더인 server 정보를 숨기기"

# Add a summary to display on homepage (optional).
summary = "IIS의 응답 헤더인 server 정보를 숨기기"

date = "2022-10-12T15:23:44Z"
publishDate = "2022-10-12T15:23:44Z"
expiryDate = ""

# Authors. Comma separated list, e.g. `["Bob Smith", "David Jones"]`.
authors = []

# Tags and categories
# For example, use `tags = []` for no tags, or the form `tags = ["A Tag", "Another Tag"]` for one or more tags.
tags = ["IIS"]
categories = ["WEB"]
keywords = ["IIS","Server"]

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

# server 헤더 제거하기
`web.config` 파일을 수정
```xml
<system.webServer>
  <security>
    <requestFiltering removeServerHeader="true" />
  </security>
</system.webServer>
```
&nbsp;  
&nbsp;  
&nbsp;  
  
# X-Powerd-By: ASP.NET 헤더 제거하기
`<system.webServer>` 안에 추가하기
```xml
<httpProtocol>
  <customHeaders>
    <remove name="X-Powered-By" />
  </customHeaders>
</httpProtocol>
```
&nbsp;  
&nbsp;  
&nbsp;  
  
# X-AspNetMvc-Version 헤더 제거하기
`web.config` 파일을 수정
```xml
<system.web>
  <httpRuntime enableVersionHeader="false" />
</system.web>
```

참고: https://saltlee.tistory.com/174