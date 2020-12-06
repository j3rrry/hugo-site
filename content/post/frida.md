+++
title = "iOS hooking"
subtitle = "using Frida"

# Add a summary to display on homepage (optional).
summary = "using Frida"

date = "2020-12-03T04:12:48+09:00"
publishDate = "2020-12-03T04:12:48+09:00"
expiryDate = ""

# Authors. Comma separated list, e.g. `["Bob Smith", "David Jones"]`.
authors = []

# Tags and categories
# For example, use `tags = []` for no tags, or the form `tags = ["A Tag", "Another Tag"]` for one or more tags.
tags = ["frida"]
categories = ["frida"]
keywords = ["frida"]

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

IDA로 후킹할 사용자 함수의 상대 offset을 알아낸다.  
frida로 앱 base주소를 얻고 offset을 더해준 주소에 후킹을 걸면 된다.  
```js
var module_base = Module.findBaseAddress('testapp');    // get base addr
var custom3_5fdfd4 = module_base.add(0x5fdfd4);         // add function offset

Interceptor.attach(custom3_5fdfd4, {
    onEnter: function (args) {
        send("[*] custom3()  called");                  // before call
    },
    onLeave: function (retval) {
        //send("[] custom3 ret: " + retval.toString()); // after call
    }
});
```
