+++
title = "Facebook CTF 2019"
subtitle = ""

# Add a summary to display on homepage (optional).
summary = "fbctf 2019 matome"

date = "2019-06-03T14:55:25+09:00"
publishDate = "2019-06-03T14:55:25+09:00"
expiryDate = ""

# Authors. Comma separated list, e.g. `["Bob Smith", "David Jones"]`.
authors = []

# Tags and categories
# For example, use `tags = []` for no tags, or the form `tags = ["A Tag", "Another Tag"]` for one or more tags.
tags = []
categories = []
keywords = []

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

Facebook CTF 2019

# EASTER EGG  
{{< figure alt="easer_egg_25" src="https://user-images.githubusercontent.com/19237789/58808134-5c92a500-8654-11e9-9643-93fda29d1305.png" width="500px" >}}

https://www.fbctf.com/careers 페이지에 텍스트를 마우스로 드래그해보면 숨어있는 문자가 뿔뿔이 흩어져있다.
그걸 모아보면 `fb{we're_hiring}`
```diff
$ egrep -io '<span style="color:white">.</span>' careers
<span style="color:white">f</span>
<span style="color:white">b</span>
<span style="color:white">{</span>
<span style="color:white">w</span>
<span style="color:white">e</span>
<span style="color:white">'</span>
<span style="color:white">r</span>
<span style="color:white">e</span>
<span style="color:white">_</span>
<span style="color:white">h</span>
<span style="color:white">i</span>
<span style="color:white">r</span>
<span style="color:white">i</span>
<span style="color:white">n</span>
<span style="color:white">g</span>
<span style="color:white">}</span>
$ egrep -io '<span style="color:white">.</span>' careers | sed 's/<[a-z=": ]*>//' | sed 's/<\/span>//' | tr -d '\n'
fb{we're_hiring}
```

pwnable  
babylist : `#UAF`

중국어 Web위주 ProductsManager events hr_admin_module , pdfme, rceservice, secret_note_keeper : https://github.com/w181496/CTF/tree/master/fbctf2019  
Regexicide : https://github.com/utcoalition/Public-CTF-Writeups  
pwnable r4nk : https://ctftime.org/writeup/15478  
pwnable overfloat : https://made0x78.com/ctfs-fbctf19-overfloat/  
GoN member babylist(pwn), keybaseish, kpets, otp_server(pwn), raddest_db(pwn), rank(pwn) : https://github.com/pr0cf5/CTF-writeups/tree/master/2019/fbctf  
homework_assignment_1337, Products manager, : https://k3ramas.blogspot.com/2019/06/facebook-ctf-2019.html  
easer egg : https://github.com/Hong5489/fbctf2019

[17:35] <toblu> I'm interested in how to solve SOMBRERO ROJO (part 1). Maybe part 2 as well. Is there a writeup anywhere?  
[17:36] <fluxchief> toblu: re sombrero 2: there was a off-by-one that needed to be patched + the binary path can't be too long otherwise it will overflow some buffer
