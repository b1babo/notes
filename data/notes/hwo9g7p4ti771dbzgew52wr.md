
# 格式化字符串漏洞
## 格式化输出函数和格式字符串用法

### 格式化占位符

`%[parameter][flags][field width][.precision][length]type`

- parameter: n$,表示对应第几个参数，参数可以不同顺序输出，可被使用多次，从第1开始数，不包括格式化字符串本身
```c
printf("%2$d %2$#x; %1$d %1$#x",16,17)//"17 0x11; 16 0x10"
``` 
- type
```
%d/i：4byte,解释为数值
%u: 4byte,无符号，解释为数值
%s: 4byte,解释为字符串指针，有解引用的效果
%x/X: 4byte,解释为数值，打印16进制
%c: 1byte,解释为值
%p: 4byte，解释为数值
%n: 不输出字符，把成功输出的字符个数（这个百分号之前的字符个数），将对应的参数解释为整型指针，并写入所指内存
```
- length
```
hh: 1byte
h: 2byte
l: 4byte
ll: 8byte
```
### 格式字符串用法
```c
//fmt_str_1.c
// gcc -m32 -g fmt_str_1.c -o fmt_str_1_32
#include<stdio.h>
#include<stdlib.h>
void main() {
    char *format = "%s";
    char *arg1 = "Hello World!\n";
    printf(format, arg1);
    printf("%03d.%03d.%03d.%03d\n", 127, 0, 0, 1);    // "127.000.000.001"
    printf("%.2f\n", 1.2345);   // 1.23
    printf("%#010x\n", 3735928559);   // 0xdeadbeef
    int n =0;
    printf("%s%n\n", "01234", &n);  // n =5 
    printf("%d\n",n);  // n = 5
}
```

#### 调用printf时栈结构
```c
  0x56592222 <main+53>    lea    eax, [ebx - 0x1fc9]
   0x56592228 <main+59>    mov    dword ptr [ebp - 0x10], eax
   0x5659222b <main+62>    sub    esp, 8
   0x5659222e <main+65>    push   dword ptr [ebp - 0x10]
   0x56592231 <main+68>    push   dword ptr [ebp - 0x14]
 _ 0x56592234 <main+71>    call   printf@plt                    <printf@plt>
        format: 0x56593008 __ 0x48007325 /* '%s' */
        vararg: 0x5659300b __ 'Hello World!\n'
 
   0x56592239 <main+76>    add    esp, 0x10
   0x5659223c <main+79>    sub    esp, 0xc
   0x5659223f <main+82>    push   1
   0x56592241 <main+84>    push   0
   0x56592243 <main+86>    push   0
```

```c
──────────────────────────────────────────────────────────[ STACK ]──────────────────────────────────────────────────────────
00:0000│ esp 0xffce6b50 —▸ 0xffce6b84 ◂— '%08x.%08x.%08x.%08x.%08x'
01:0004│     0xffce6b54 ◂— 0x1
02:0008│     0xffce6b58 ◂— 0x88888888
03:000c│     0xffce6b5c ◂— 0xffffffff
04:0010│     0xffce6b60 —▸ 0xffce6b7a ◂— 'ABCD'
05:0014│     0xffce6b64 —▸ 0xffce6b84 ◂— '%08x.%08x.%08x.%08x.%08x'
06:0018│     0xffce6b68 —▸ 0xf7f92b50 —▸ 0xf7f92af0 —▸ 0xf7f5e3e0 —▸ 0xf7f92990 ◂— ...
07:001c│     0xffce6b6c —▸ 0x80491f4 (main+30) ◂— add    ebx, 0x2e0c
────────────────────────────────────────────────────────[ BACKTRACE ]────────────────────────────────────────────────────────
 ► f 0 0x804925f main+137
   f 1 0xf7d82ed5 __libc_start_main+245

```

### 漏洞原理
> 如果格式化字符串可控，怎么样的可控，可以导致什么效果，都必须清楚，效果可以分为几类：出错（奔溃），副作用（写内存，读内存），绕过某种判断，不按预期输出结果（导致其他用这个输出结果的地方存在问题
> 如果输入参数可控，配合相应的格式化字符串对参数的解释，可导致任意内存读写


- 格式字符串要求的参数和实际提供的参数不匹配，格式化字符串%的个数对应参数的个数，对于32位程序会取栈中的值作为参数，对于64位程序，取决于第几个参数
- 已经push的参数类型（如果是数值类型，则直接将值复制到栈中，如果是字符串，则将地址复制到栈中）和不匹配的格式化字符格式，如果printf函数的参数可控
- 格式化字符串提供了读，写原语
- 读地址是否可控，读内容是否可控，是否可打印
- 写地址是否可控，写内容是否可控




### 如何利用
#### 读原语
```c
//fmt_str_2.c
//echo 0 > /proc/sys/kernel/randomize_va_space
// gcc -m32 -g fmt_str_2.c -fno-stack-protector -no-pie -o fmt_str_2_32
#include<stdio.h>
void main() {
    char format[128];
    int arg1 = 1, arg2 = 0x88888888, arg3 = -1;
    char arg4[10] = "ABCD";
    scanf("%s", format);
    printf(format, arg1, arg2, arg3, arg4);
    printf("\n");
}
```

- 使用%s有解引用的效果，可以打印*p，如果p可控，则可以造成任意地址读，因此需要对输入参数可控，参数在栈中
- 参数是第几位



#### 写原语


```c
//fmt_str_2.c
//echo 0 > /proc/sys/kernel/randomize_va_space
// gcc -m32 -g fmt_str_2.c -fno-stack-protector -no-pie -o fmt_str_2_32
#include<stdio.h>
void main() {
    char format[128];
    int arg1 = 1, arg2 = 0x88888888, arg3 = -1;
    char arg4[10] = "ABCD";
    scanf("%s", format);
    printf(format, arg1, arg2, arg3, arg4);
    printf("\n");
}
```



#### 64位利用







### 如何挖掘漏洞
#### 代码审计
- 对格式化输出函数敏感，梳理格式化字符串是否可控
#### fuzz









## 参考
[wiki](https://zh.wikipedia.org/wiki/%E6%A0%BC%E5%BC%8F%E5%8C%96%E5%AD%97%E7%AC%A6%E4%B8%B2)

[ctf-all-in-one](https://firmianay.gitbook.io/ctf-all-in-one/3_topics/pwn/3.1.1_format_string)

[ctf-wiki](https://ctf-wiki.org/pwn/linux/user-mode/fmtstr/fmtstr-intro/)