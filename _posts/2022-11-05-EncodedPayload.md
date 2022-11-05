---
title: EncodedPayload
tags: [DeadFace, ctf]
color: success 
description: Writeup for EncodedPayload from DeadFace CTF event.
---
# Writeup for EncodedPayload

## Description

```text
Buried in your basement you've discovered an ancient tome. The pages are full of what look like warnings, but luckily you can't read the language! What will happen if you invoke the ancient spells here?
```

The 32-bit elf is given us to analyze, running strace shows a `execve` called is made that echoes a flag to us.

```bash
» strace ./encodedpayload     
execve("./encodedpayload", ["./encodedpayload"], 0x7ffe2ee1fd30 /* 56 vars */) = 0
[ Process PID=3547 runs in 32 bit mode. ]
socket(AF_INET, SOCK_STREAM, IPPROTO_IP) = 3
dup2(3, 2)                              = 2
dup2(3, 1)                              = 1
dup2(3, 0)                              = 0
connect(3, {sa_family=AF_INET, sin_port=htons(1337), sin_addr=inet_addr("127.0.0.1")}, 102) = -1 ECONNREFUSED (Connection refused)
syscall_0xffffffffffffff0b(0xffe5e188, 0xffe5e180, 0, 0, 0, 0) = -1 ENOSYS (Function not implemented)
execve("/bin/sh", ["/bin/sh", "-c", "echo HTB{PLz_strace_M333}"], NULL) = 0
[ Process PID=3547 runs in 64 bit mode. ]
brk(NULL)                               = 0x557dea70f000
access("/etc/ld.so.preload", R_OK)      = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 4
fstat(4, {st_mode=S_IFREG|0644, st_size=121560, ...}) = 0
mmap(NULL, 121560, PROT_READ, MAP_PRIVATE, 4, 0) = 0x7fdde970f000
close(4)                                = 0
openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libc.so.6", O_RDONLY|O_CLOEXEC) = 4
read(4, "\177ELF\2\1\1\3\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0@>\2\0\0\0\0\0"..., 832) = 832
fstat(4, {st_mode=S_IFREG|0755, st_size=1905632, ...}) = 0
mmap(NULL, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7fdde970d000
mmap(NULL, 1918592, PROT_READ, MAP_PRIVATE|MAP_DENYWRITE, 4, 0) = 0x7fdde9538000
mmap(0x7fdde955a000, 1417216, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 4, 0x22000) = 0x7fdde955a000
mmap(0x7fdde96b4000, 323584, PROT_READ, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 4, 0x17c000) = 0x7fdde96b4000
mmap(0x7fdde9703000, 24576, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 4, 0x1ca000) = 0x7fdde9703000
mmap(0x7fdde9709000, 13952, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x7fdde9709000
close(4)                                = 0
arch_prctl(ARCH_SET_FS, 0x7fdde970e580) = 0
mprotect(0x7fdde9703000, 16384, PROT_READ) = 0
mprotect(0x557de9409000, 8192, PROT_READ) = 0
mprotect(0x7fdde9757000, 4096, PROT_READ) = 0
munmap(0x7fdde970f000, 121560)          = 0
getuid()                                = 1000
getgid()                                = 1003
getpid()                                = 3547
rt_sigaction(SIGCHLD, {sa_handler=0x557de93fea20, sa_mask=~[RTMIN RT_1], sa_flags=SA_RESTORER, sa_restorer=0x7fdde9570d60}, NULL, 8) = 0
geteuid()                               = 1000
getppid()                               = 3544
brk(NULL)                               = 0x557dea70f000
brk(0x557dea730000)                     = 0x557dea730000
getcwd("/home/zex/Documents/HackTheBox/AfterEvent/EncodedPayload", 4096) = 57
geteuid()                               = 1000
getegid()                               = 1003
rt_sigaction(SIGINT, NULL, {sa_handler=SIG_DFL, sa_mask=[], sa_flags=0}, 8) = 0
rt_sigaction(SIGINT, {sa_handler=0x557de93fea20, sa_mask=~[RTMIN RT_1], sa_flags=SA_RESTORER, sa_restorer=0x7fdde9570d60}, NULL, 8) = 0
rt_sigaction(SIGQUIT, NULL, {sa_handler=SIG_DFL, sa_mask=[], sa_flags=0}, 8) = 0
rt_sigaction(SIGQUIT, {sa_handler=SIG_DFL, sa_mask=~[RTMIN RT_1], sa_flags=SA_RESTORER, sa_restorer=0x7fdde9570d60}, NULL, 8) = 0
rt_sigaction(SIGTERM, NULL, {sa_handler=SIG_DFL, sa_mask=[], sa_flags=0}, 8) = 0
rt_sigaction(SIGTERM, {sa_handler=SIG_DFL, sa_mask=~[RTMIN RT_1], sa_flags=SA_RESTORER, sa_restorer=0x7fdde9570d60}, NULL, 8) = 0
write(1, "HTB{PLz_strace_M333}\n", 21)  = -1 EPIPE (Broken pipe)
--- SIGPIPE {si_signo=SIGPIPE, si_code=SI_USER, si_pid=3547, si_uid=1000} ---
+++ killed by SIGPIPE +++
```

#### Author: <a href="https://twitter.com/zexionx00">zexionx00</a>