---
title: "Hugo Templating"
date: 2019-02-20T06:26:19Z
description: ""
categories: [hugo, Template]
tags: [hugo, Template]
featuredImage: ""
featuredImageDescription: ""
dropCap: true
displayInMenu: false
displayInList: true
---

.
# Whitespace[^whitespace]
`-`는 whitespace를 제거해준다.

without `-`
```
      <header class="list-header">
        {{ with .Site.Params.headerSubtext }}
        <p class="list-header-subtext">{{ . }}</p>
        {{ end }}
        <h1 class="list-header-title">{{ .Title }}</h1>
```

with `-`
```
      <header class="list-header">
        {{- with .Site.Params.headerSubtext }}
        <p class="list-header-subtext">{{ . }}</p>
        {{ end -}}
        <h1 class="list-header-title">{{ .Title }}</h1>
```

RESULT without:
```
      <header class="list-header">

        <p class="list-header-subtext">K-Shield Jr. CM 2기</p>

        <h1 class="list-header-title">posts</h1>
```

RESULT with:
```
      <header class="list-header">
        <p class="list-header-subtext">K-Shield Jr. CM 2기</p>
        <h1 class="list-header-title">posts</h1>
```

[^whitespace]: https://gohugo.io/templates/introduction/#whitespace
