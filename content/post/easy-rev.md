+++
title = "Easy Rev"
subtitle = "codegate2019 Open CTF"

# Add a summary to display on homepage (optional).
summary = "codegate2019 Open CTF"

date = "2019-03-29T19:50:20+09:00"
publishDate = "2019-03-29T19:50:20+09:00"
expiryDate = ""

# Authors. Comma separated list, e.g. `["Bob Smith", "David Jones"]`.
authors = []

# Tags and categories
# For example, use `tags = []` for no tags, or the form `tags = ["A Tag", "Another Tag"]` for one or more tags.
tags = ["codegate", "CTF", "rev", "ssl"]
categories = ["CTF"]
keywords = ["codegate", "CTF", "rev", "ssl"]

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

# easy_rev
> Easy reversing!!!
> 
> plz you would solve in ubuntu 16.04
>
> by pwnwiz
>
> [easy_rev.zip](http://octf.hacklab.kr/files/f1a12e5ed7884339dd4b108272ca70eb/easy_rev.zip)

MD5: e3eda0a4cacd2927973bca03df2197c9  
  
<br>  

```
$ file easy_rev flag
easy_rev: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 2.6.32, BuildID[sha1]=d6ab8e0c86636e8331cc465ae54a5013598dd79e, not stripped
flag:     openssl enc'd data with salted password
```
하나는 바이너리이고 다른 하나는 암호화된 flag이다.  
  
<br>  
  
```
void __cdecl main(int argc, const char **argv, const char **envp)
{
  signed int i; // [sp+8h] [bp-48h]@1
  int v6[10]; // [sp+10h] [bp-40h]@1

  puts("==========================================");   // banner
  puts("       NEWBIE REV1 right here !!");
  puts("solve the magic I putted, and get the flag");
  puts("==========================================");
  printf(">> ");
  for ( i = 0; i < 10; ++i )
    scanf("%d", &v6[i]);                                // 10개의 정수 입력
  puts("++++++++++++++++++++++++++++++++++++++++++");
  puts("Let's See the result!!!!");
  puts("++++++++++++++++++++++++++++++++++++++++++");
  if ( swap(v6) == 10 )                                 // 성공
  {
    puts(">> Yes, You got right ( IF YOU CERTAINLY INSERTED EXACTLY 10 NUMBERS )");
    puts(">> You just need to 'add' all the no for every index. That sum is key for flag file !!");
    puts(">> (flag file is encryted aes-256-cbc of openssl)");
  }
  else                                                  // 실패
  {
    puts(">> Nope :< BETTER TRY HARDER THAN THAT");
  }
}
```
정확히 바이너리가 원하는 10개의 정수를 입력해야 성공이다.
  
<br>  
  
```
int __fastcall swap(int *a1)
{
  int result = 0; // [sp+18h] [bp-48h]@1
  int v4 = 3; // [sp+1Ch] [bp-44h]@1
  int i; // [sp+24h] [bp-3Ch]@1
  int j; // [sp+28h] [bp-38h]@9
  int k; // [sp+2Ch] [bp-34h]@12
  int v9[10] = {0x4f, 4, 0x24, 0x274, 0x75, 0x3e, 0x99a, 0xffffff9b, 0x29, 0xef}; // [sp+30h] [bp-30h]@1

  for ( i = 0; i < 10; ++i )    // a1의 인덱스i에 따라 서로 다른 값과 xor
  {
    if ( i % 3 == 1)
      v4 -= i;
    else if ( i % 3 == 2 )
      v4 += i;
    else
      v4 *= i;
    a1[i] ^= v4;
  }

  for ( j = 0; j < 10; ++j )    // 0xF 와 xor
    a1[j] ^= 0xF;

  for ( k = 0; k < 10; ++k )    // v9와 모두 같아야 함
  {
    if ( a1[k] == v9[k] )
      ++result;
  }
  return result;
}
```
swap 함수는 정수형 배열 `v9`를 만들고 두 가지 연산을 한다.
{{< figure src="https://user-images.githubusercontent.com/19237789/55238845-ccfa0000-5278-11e9-986f-ca0d4e19e526.png" alt="loop1.png" title="<그림1> loop 1" height="60%" width="60%" >}}
`a1`의 인덱스(i)에 따라 서로 다른 값과 xor 연산하고 0xF 와 한번더 xor 연산을 한 값이 정수형 배열 `v9`와 같아야 한다.  
  
<br>
  
```
64 -12 42 632 -123 53 2445 -123 63 1
```
위의 10개의 숫자를 넣으면 
```
$ ./easy_rev
==========================================
       NEWBIE REV1 right here !!
solve the magic I putted, and get the flag
==========================================
>> 64 -12 42 632 -123 53 2445 -123 63 1
++++++++++++++++++++++++++++++++++++++++++
Let's See the result!!!!
++++++++++++++++++++++++++++++++++++++++++
>> Yes, You got right ( IF YOU CERTAINLY INSERTED EXACTLY 10 NUMBERS )
>> You just need to 'add' all the no for every index. That sum is key for flag file !!
>> (flag file is encryted aes-256-cbc of openssl)
```
10개의 숫자를 더한 값이 flag 파일의 key라고 알려준다.  
  
<br>

```
>>> sum([64, -12, 42, 632, -123, 53, 2445, -123, 63, 1])
3042
```
key = 3042

<br>

```
$ openssl enc -d -aes-256-cbc -in flag -k 3042
flag{R2versing_1s_b4sed_0n_H4cking_:)}
```
ubuntu 16.04에서 복호화했다.  
`flag{R2versing_1s_b4sed_0n_H4cking_:)}`
