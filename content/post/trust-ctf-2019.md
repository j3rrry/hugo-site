---
title: "TRUST CTF 2019"
date: 2019-02-18T04:34:37Z
description: ""
categories: ["CTF"]
featuredImage: ""
featuredImageDescription: ""
dropCap: true
displayInMenu: false
displayInList: true
tags: ["CTF", "TRUST", "2019"]
---

퍼너블이 제일 어려웠다고 생각합니다.  왜냐하면 저는 하나도 못풀었으니끼요. 아무튼 재미있었다.  
  
* [MISC / MIC CHECK! 100 / 114 Solves](#mic)  
* [MISC / Easy Taebo 100 / 43 Solves](#e-taebo)  
* [REV / Mess 100 / 31 Solves](#mess)  
* [REV / Hello WorldS! 493 / 5 Solves](#hello)  
  
# MIC CHECK! 100 / MISC / 114 Solves{#mic}
  
디스코드(IRC) : https://discord.gg/ZYyupm8  
`TRUST{Welcome_CTF_Have_FUN!}`  
  
# Easy Taebo 100 / MISC / 43 Solves{#e-taebo}
> https://www.youtube.com/watch?v=evrH9vRDwEw  
> TRUST CTF에서도 태.보.해.  
> nc server.trustctf.<i></i>com 44923  
> Author  : st4nw(조정훈)  
> Download: action_list.txt
  
조혜련의 태보 다이어트 유튜브 영상에서는 `jab`, `hook` 등의 구호에 맞춰서 약 25분동안 운동을 하는 모습이 나온다.
nc로 서버에 접속해보면 다음과 같은 문구가 나온다  
```
+------------------------------------------------------------------------+
| Example)                                                               |
|                                                                        |
| left_jab + left_hook + right_speedball >> @==(^0^)@ @(^0^)@== (^0^)@@@ |
+------------------------------------------------------------------------+

Ready : Taebo starts in 3 seconds

3
2
1

Taebo 1 : left_mid_jab + left_mid_jab + right_hook + left_kick >>

```
각 명령에 따라서 알맞은 이모티콘으로 답하면 되는 일종의 데이터 매니지먼트 프로그래밍 문제이다.
명령에 매칭되는 이모티콘은 문제 설명에 제공되어 있는 `action_list.txt`에 명시되어 있었다.  
  
```python
from pwn import *

r = remote('server.trustctf.com', 44923)

p = log.progress('Taebo')
for i in range(100):
    a01 = re.search(r'Taebo (\d+) : (.*) >>', r.recvuntil('>> \n'))
    p.status('%s%%/100%%' % a01.group(1))

    a02 = re.sub(r'''(left_jab
                    |left_mid_jab
                    |mid_jab
                    |right_mid_jab
                    |right_jab
                    |left_hook
                    |right_hook
                    |left_speedball
                    |right_speedball
                    |left_kick
                    |mid_kick
                    |right_kick)''',
            lambda x: {'left_jab':'@==(^0^)@'     ,
                    'left_mid_jab':'@=(^0^)@'     ,
                    'mid_jab' : '@(^0^)@'         ,
                    'right_mid_jab' : '@(^0^)=@'  ,
                    'right_jab' : '@(^0^)==@'     ,
                    'left_hook' : '@(^0^)@=='     ,
                    'right_hook' : '==@(^0^)@'    ,
                    'left_speedball' : '@@@(^0^)' ,
                    'right_speedball' : '(^0^)@@@',
                    'left_kick' : '@||(^0^)==@'   ,
                    'mid_kick' : '@==(^||^)==@'   ,
                    'right_kick' : '@==(^0^)||@'  ,
                    }[x.group()], a01.group(2), flags=re.X)
    r.sendline(a02.replace(' + ', ' '))
p.success()
print r.recvuntil('}')

# TRUST{w0w_y0u_9o7_4_w0nd3rfu1_b0dy_lik3_m3}
```
총 100번의 태보 명령을 수행하면 Flag를 준다. nc socket 연결에 시간 제한이 있었던건 아니라서 손으로 풀 수도 있을 것 같다..?  
  
# Mess 100 / REV / 31 Solves{#mess}
문자열 검색을 해보면 `S3CRe7PA5sW0rD` 이 보인다. 그걸 입력해 넣으면 된다.  
```python
str1 = 'S3CRe7PA5sW0rD'
str2 = ''
str3 = ''
str4 = ''

for j in str1:
    str2 += chr(ord(j)+5)
for k in str2:
    str3 += chr(ord(k)^5)
for l in str3:
    str4 += chr(ord(l)+5)

print 'TRUST{%s}' % str4
```
`TRUST{bBRWt>UHD\x82^5wQ}`  
  
# Hello WorldS! 493 / REV / 5 Solves{#hello}
> So many "Hello World" programs....  
> dukup11ch1 made 8 programs  
> you just find his programs and write "Yara rule"  
> "Hello World"프로그램이 많아...  
> dukup11ch1가 8개를 만들었어.  
> 그의 프로그램들을 찾고 "Yara rule"을 작성하세요  
> nc server.trustctf.com 5252  
> (Code the rule in one line.) (rule은 한줄로 짜시오.)  
> Author : dukup11ch1(유기환)

```
$ md5sum *
c05ce65e737b09f71ff5ae3edebcdbea  1.exe
cfaa3f5b852ee5cce113b3ccddeb74d0  10.exe
a93b7803a3679e4b212603c50781201d  11.exe
4ad23f830fd77db5ea31972dba0d320d  12.exe
a3c9cfcf8da608cf7674296c2c8eda12  13.exe
f65e03c6b54c42800ce05a5325bba1bf  14.exe
3e636b5823d86e1b0f7d5412a98632b2  15.exe
994447363b6d73f335c40ace54f03e33  16.exe
1637028afe5f22949fd9e923495a3632  2.exe
b9711df23fbd1e1c33e9ff2a7187360d  3.exe
36060d31fa3f628d72400bbd27aef0fe  4.exe
2378968a9c49099dac2d3b631e875625  5.exe
a85e435ef9fdbd798b1c49af1f120db6  6.exe
4fc8a49b8b3d9bb104b8d2fa69ed2356  7.exe
24590148203d1ec7e23e6f2cab13293c  8.exe
7c14bd0f1817100ef10c199427801008  9.exe
```
전부 다른 파일들이었는데 그중에 myfile은 탐지(matched)시키고 notmine은 통과(not matched)시키는 yara rule을 만드는 문제였다.  
  
Stub Code 영역의 차이점을 이용해서 yara rule을 짰다. 오프셋 0x80 ~ 0xEF 구간을 보면 myfile과 notmine이 서로 겹치는 것이 없었다.  
  
yara:  
`rule a { strings: $a = "<,<" $b = { 6C B0 1D } condition: $a at 128 or $b at 128}`  
  
myfile의 Stub Code 영역인 오프셋 0x80에는 두 가지로 나뉜다. `<,<` 로 시작하거나 `6C B0 1D` 로 시작하거나 이다. 그래서 위치 0x80(128)에 `<,<`이거나 `6C B0 1D`인 rule을 만들었다.  

FLAG : `TRUST{I9n0re_PDB_R1CH_I'm_s0rry_TT}`  
