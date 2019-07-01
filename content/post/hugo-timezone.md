---
title: "Hugo Timezone and Date Format"
date: 2019-02-23T02:17:15+0900
description: ""
categories: []
tags: []
keywords: []
featuredImage: ""
featuredImageDescription: ""
dropCap: true
displayInMenu: false
displayInList: true
---

Hugo에서는 config.toml에서 timezone을 설정하지 않는듯 하다(제가 모르는걸수도)
그래서 어떻게 하느냐? Hugo는 Server system 시간을 따라간다는 것이다.
제타위키의 리눅스 시간대 설정 <a href="https://zetawiki.com/wiki/리눅스_KST_시간대_설정#.EB.B0.A9.EB.B2.95_2" target="_blank">방법 2</a>를 통해서 서버의 시간대를 설정해주면 된다.
```bash
ln -sf /usr/share/zoneinfo/Asia/Seoul /etc/localtime
```

다 끝났다.
추가적으로 archetypes에 date를 조금 손봐주는 것은 해도 되고 안해도 된다.

```yaml
date: {{ dateFormat "2006-01-02 15:04:05 -0700" .Date }}
```
<a href="https://gohugo.io/functions/dateformat/" target="_blank">dateFormat</a>이란 것을 사용했는데
```yaml
{{ dateFormat "포맷" "시간" }}
```
구문이 위와 같으며
```markup
2006:  년
01:    월
02:    일
15:    시
04:    분
05:    초
-0700: 타임존
MST:   타임존
Mon:   요일
```
각각의 숫자의 의미가 정해져 있습니다. 왜 이렇게 특정 숫자로 매칭을 시킨건지는 모르겠네요.
<a href="https://gohugohq.com/howto/hugo-dateformat/" target="_blank">예시</a>를 확인해보세요.
