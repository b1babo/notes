---
id: 6czaktejjj80bj1udleo8rj
title: Template
desc: ''
updated: 1672065487519
created: 1672064406757
---

## pwn题模板

```py

from pwn import * 
import os
# ========================================
is_debug = False
is_debug = True
architecture = {"arch":"i386","os":"linux","endian":"little","bits":64}
architecture = {"arch":"amd64","os":"linux","endian":"little","bits":32}
# is_remote = True
is_remote = False
ip = ""
port = ""
target = "./4.1_core"
target_libc = ""
env = []

# ========================================
def _which(p):
    return os.system("which " + p + " >/dev/null 2>&1") == 0



# ========================================
if is_debug:
    context.log_level='debug'
else:
    context.log_level='info'
context(os=architecture["os"],arch=architecture["arch"],bits=architecture["bits"])
elf = ""
libc = ""
if target:
    context.binary = target
if target:
    elf = ELF(target)
if target_libc:
    libc = ELF(target_libc)

if _which("tmux"):
    terminal = ['tmux', 'splitw', '-h']
elif _which('gnome-terminal'):
    terminal = ['tmux', '--', 'sh', '-c']
else:
    terminal = None
context.terminal = terminal

if is_remote:
    while True:
        try:
            io=remote(ip,port)
        except:
            log.info("remote:too many requests,I am tried,sleep one second please")
            sleep(0x10)
            continue
        break
else:
    while True:
        try:
            io=process(target)
        except:
            log.info("local:too many process,I can not malloc any memory")
            log.info("bye~~")
            exit()
        break

def debug(gdbscript="", stop=False):
    if isinstance(io, process):
        if stop:
            pause()
        gdb.attach(io, gdbscript=gdbscript)


# ========================================
stop = pause
S = pause
leak = lambda name, address: log.info("{{}} ===> {{}}".format(name, hex(address)))
s   = io.send
sl  = io.sendline
sla = io.sendlineafter
sa  = io.sendafter
slt = io.sendlinethen
st  = io.sendthen
r   = io.recv
rn  = io.recvn
rr  = io.recvregex
ru  = io.recvuntil
ra  = io.recvall
rl  = io.recvline
rs  = io.recvlines
rls = io.recvline_startswith
rle = io.recvline_endswith
rlc = io.recvline_contains
ia  = io.interactive
ic  = io.close
cr  = io.can_recv

buffer_padding = lambda offset : offset*'A'
# ========================================


# class Libc:
#     def __init__(self,libc_func_name,libc_func_addr):
#         self.libc = LibcSearcher(libc_func_name,libc_func_addr)



# ========================================


S()

debug(gdbscript="b *main\r\nb printf")
ia()
# S()


```