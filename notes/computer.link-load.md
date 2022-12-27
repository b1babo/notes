---
id: 4avrmav08jnnhtcpa5a13r1
title: Link Load
desc: ''
updated: 1672101151077
created: 1669680803417
---


## got表
> got是一个地址，一般jmp/call [addr]，[addr]包含了真正的外部的地址

```
.got.plt:08049964                               _got_plt segment dword public 'DATA' use32
.got.plt:08049964                               assume cs:_got_plt
.got.plt:08049964                               ;org 8049964h
.got.plt:08049964 78 98 04 08                   off_8049964 dd offset stru_8049878      ; DATA XREF: _init_proc+9↑o
.got.plt:08049964                                                                       ; init+9↑o
.got.plt:08049964                                                                       ; _term_proc+9↑o
.got.plt:08049968 00 00 00 00                   dword_8049968 dd 0                      ; DATA XREF: sub_80483E0↑r
.got.plt:0804996C 00 00 00 00                   dword_804996C dd 0                      ; DATA XREF: sub_80483E0+6↑r
.got.plt:08049970 AC 99 04 08                   off_8049970 dd offset setbuf            ; DATA XREF: _setbuf↑r
.got.plt:08049974 B0 99 04 08                   off_8049974 dd offset printf            ; DATA XREF: _printf↑r
.got.plt:08049978 B4 99 04 08                   off_8049978 dd offset fgets             ; DATA XREF: _fgets↑r
.got.plt:0804997C B8 99 04 08                   off_804997C dd offset alarm             ; DATA XREF: _alarm↑r
.got.plt:08049980 BC 99 04 08                   off_8049980 dd offset puts              ; DATA XREF: _puts↑r
.got.plt:08049984 C0 99 04 08                   off_8049984 dd offset strchr            ; DATA XREF: _strchr↑r
.got.plt:08049988 C4 99 04 08                   off_8049988 dd offset strlen            ; DATA XREF: _strlen↑r
.got.plt:0804998C C8 99 04 08                   off_804998C dd offset __libc_start_main ; DATA XREF: ___libc_start_main↑r
.got.plt:08049990 CC 99 04 08                   off_8049990 dd offset putchar           ; DATA XREF: _putchar↑r
.got.plt:08049990                               _got_plt ends
```

## plt
> plt是一个地址，一般是jmp/call addr，相当于外部符号在本elf的间接地址

```
.plt:08048400                               ; int printf(const char *format, ...)
.plt:08048400                               _printf proc near                       ; CODE XREF: main+4D↓p
.plt:08048400
.plt:08048400                               format= dword ptr  4
.plt:08048400
.plt:08048400 FF 25 74 99 04 08             jmp     ds:off_8049974
.plt:08048400
.plt:08048400                               _printf endp
```