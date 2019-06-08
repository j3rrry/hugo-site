---
title: "class vglnk 속성 막기"
date: 2019-02-21T00:42:00Z
summary: "
        포스팅을 하다보니 자동으로 원하지 않는 `link`를 만들고 있는 것을 확인했다.
        Hugo Theme에는 여러 Plugin들이 사용되고 있을 것이므로 무엇이 원인인지 알아봤다.
        "
categories: []
tags: [vglnk, nolink, link, autolink]
featuredImage: ""
featuredImageDescription: ""
dropCap: true
displayInMenu: false
displayInList: true
---

Github Page Theme에는 여러 Plugin들이 적용되어 있다. Posting을 하다보니 원치않는 link를 만들고 있는 것을 확인했다. HTML tag로 a tag가 사용되었으며 자동으로 color는 어두운 색으로 지정된다. 특히 폰트 색이 어두워지기 때문에 곤란해지는 상황이 일어났다. 왜냐하면 내가 사용하는 Code Block의 경우 배경색을 Dark(monokai)로 지정해서 쓰고 있기 때문에 어두운 색인 폰트가 안보이게 된 것이었다.  
  
해결 방법은 두가지로 정리해볼 수 있을 것 같다.

1. class 속성 `nolinks` 주기
2. **(Disqus 사용하는 분) Affiliate links 해제하기**

첫 번째 방법은 HTML tag의 class 속성으로 `nolinks`를 주기만 하면 된다.[^twpower's blog]  
```html
  <body class="single-body nolinks">
```
  
두 번째 방법은 Disqus 사용하시는 분들만 해당할 것 같다.
구글링을 하다가 @ediano의 댓글[^Disqus]을 읽고나서 Disqus의 문제란 것을 알게 되었다.
<a href="https://help.disqus.com/ads/how-to-disable-affiliate-linking" target="_blank">![][affiliateLinksImage]</a>
곧바로 Disqus의 autolink에 대해 검색해보니 Affiliate link라는 것이 있다는 것을 알았다

Admin > Settings > [Advanced][Advanced]로 가서 Affiliate Links 를 체크 해제하면 된다.

[^twpower's blog]: https://twpower.github.io/85-remove-vglnk-in-html-class-attribute
[^Disqus]: https://gist.github.com/alexpeattie/4729247#gistcomment-2829295
[affiliateLinksImage]: https://user-images.githubusercontent.com/19237789/53184282-1df65300-3640-11e9-9889-f5f36499bc5e.png
[Advanced]: https://disqus.com/admin/settings/advanced/
