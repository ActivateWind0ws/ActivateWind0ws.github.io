---
title: Secured Transfer
tags: [hacktheboo, ctf]
color: warning 
description: Writeup for Secured Transfer from HackTheBoo CTF event.
---
# Writeup for Secured Transfer

## Description

```text
Ghosts have been sending messages to each other through the aether, but we can't understand a word of it! Can you understand their riddles?
```

In the given file, there's a pcap file and a binary. Analyzation of binary is given below.


## Analysis

The binary works as a connector between two sessions. you need to start the server(binary) to send the files.

### Sender

```bash
» ./securetransfer 127.0.0.1 tmp
Sending File: tmp to 127.0.0.1
ERROR: File too small
```

### Reciever

```bash
» ./securetransfer              
Receiving File
ERROR: Reading secret length
```

The length seems to be error, so we generate few chars with `cyclic` from pwntools.

```bash
» ./securetransfer 127.0.0.1 tmp
Sending File: tmp to 127.0.0.1
File send...

» ./securetransfer
Receiving File
File Received...
aaaabaaacaaadaaaeaaafaaagaaahaaaiaaajaaakaaalaaamaaanaaaoaaapaaaqaaaraaasaaataaauaaavaaawaaaxaaayaaazaabbaabcaabdaabeaabfaabgaabhaabiaabjaabkaablaabmaabnaaboaabpaabqaabraabsaabtaabuaabvaabwaabxaabyaab
```


This seems to work fine now.

Running the same commands with wireshark intercepting locally reveals that the data is actually being encrypted with something.

```text
» tshark -r test.pcapng -T fields -e data



d000000000000000

fbb355bba3993a2bc84cb9bafb819e49c8a325ebff6b5adabe6c2288ca5d725ef76502b2da3f45a1f9c492515fb292b055613d39a978a677d416744e9c535dc2d9550bf1150fc59fe87609de5d896dd33ff3fededf3d06d0ef7ea9b7cf902ecd752e7e310fa060fc2b7d77e085112a0e460f15dc95b3484e8c249c694fb3b712a3936622c9043b2b0d695481295ac93a96bd20635c033dc47517452a3a2132a04a5f05bbb65e6e37959df1448a85113fe920470c3eeb30a0615b858c9f2b3ff4e6ca678ef8eb3ea14ef5acc7ca39b260

» unhex fbb355bba3993a2bc84cb9bafb819e49c8a325ebff6b5adabe6c2288ca5d725ef76502b2da3f45a1f9c492515fb292b055613d39a978a677d416744e9c535dc2d9550bf1150fc59fe87609de5d896dd33ff3fededf3d06d0ef7ea9b7cf902ecd752e7e310fa060fc2b7d77e085112a0e460f15dc95b3484e8c249c694fb3b712a3936622c9043b2b0d695481295ac93a96bd20635c033dc47517452a3a2132a04a5f05bbb65e6e37959df1448a85113fe920470c3eeb30a0615b858c9f2b3ff4e6ca678ef8eb3ea14ef5acc7ca39b260                                                                
U:+LIȣ%kZھl"]r^e?EĒQ_Ua=9xwtNS]U
iT)Z: c\=uE*:!2J_^n7D? G        şv      ]m?=~ϐ.u.~1`+}w*FܕHN$iOf";+
                        >0a[+?g>N9`
```

## Binary Analysis

Looking at the functions of binary, there's few decrypt function. Setting a break point at those reveals a password, more like a key.

```bash
pwndbg> info functions 
All defined functions:

Non-debugging symbols:
0x0000000000001230  __cxa_finalize@plt
0x0000000000001240  printf@plt
0x0000000000001250  memset@plt
0x0000000000001260  ftell@plt
0x0000000000001270  inet_pton@plt
0x0000000000001280  close@plt
0x0000000000001290  puts@plt
0x00000000000012a0  fseek@plt
0x00000000000012b0  htons@plt
0x00000000000012c0  read@plt
0x00000000000012d0  malloc@plt
0x00000000000012e0  fopen@plt
0x00000000000012f0  EVP_DecryptInit_ex@plt
0x0000000000001300  free@plt
0x0000000000001310  listen@plt
0x0000000000001320  EVP_DecryptFinal_ex@plt
0x0000000000001330  connect@plt
0x0000000000001340  EVP_CIPHER_CTX_new@plt
0x0000000000001350  socket@plt
0x0000000000001360  fread@plt
0x0000000000001370  EVP_aes_256_cbc@plt
0x0000000000001380  __stack_chk_fail@plt
0x0000000000001390  EVP_CIPHER_CTX_free@plt
0x00000000000013a0  fclose@plt
0x00000000000013b0  bind@plt
0x00000000000013c0  htonl@plt
0x00000000000013d0  OPENSSL_init_crypto@plt
0x00000000000013e0  EVP_EncryptFinal_ex@plt
0x00000000000013f0  write@plt
0x0000000000001400  EVP_DecryptUpdate@plt
0x0000000000001410  accept@plt
0x0000000000001420  EVP_EncryptInit_ex@plt
0x0000000000001430  EVP_EncryptUpdate@plt
pwndbg> break *EVP_DecryptInit_ex
Breakpoint 1 at 0x12f0
pwndbg> r                         
Starting program: /home/zex/Documents/HackTheBox/AfterEvent/SecuredTransfer/securetransfer 
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".
Receiving File                                                                                 
                                               
Breakpoint 1, 0x00007ffff7e3eed0 in EVP_DecryptInit_ex () from /lib/x86_64-linux-gnu/libcrypto.so.1.1
LEGEND: STACK | HEAP | CODE | DATA | RWX | RODATA
────────────────────────────────────────────────────────────────────[ REGISTERS / show-flags off / show-compact-regs off ]────────────────────────────────────────────────────────────────────
*RAX  0x555555574e90 ◂— 0x0
 RBX  0x0              
*RCX  0x7fffffffdbc0 ◂— 'supersecretkeyusedforencryption!'
 RDX  0x0                                    
*RDI  0x555555574e90 ◂— 0x0                                                                                                                                                                   
*RSI  0x7ffff7f7fd40 ◂— 0x10000001ab
*R8   0x555555556008 ◂— 'someinitialvalue'
*R9   0x7ffff7cb2be0 (main_arena+96) —▸ 0x555555574f30 ◂— 0x0
 R10  0x0
*R11  0xb0
*R12  0x555555555440 ◂— endbr64 
 R13  0x0
 R14  0x0
 R15  0x0
*RBP  0x7fffffffdbf0 —▸ 0x7fffffffdc60 —▸ 0x7fffffffdc80 —▸ 0x555555555ea0 ◂— endbr64 
*RSP  0x7fffffffdb78 —▸ 0x5555555557a1 ◂— cmp    eax, 1
*RIP  0x7ffff7e3eed0 (EVP_DecryptInit_ex) ◂— xor    r9d, r9d
─────────────────────────────────────────────────────────────────────────────[ DISASM / x86-64 / set emulate on ]─────────────────────────────────────────────────────────────────────────────
 ► 0x7ffff7e3eed0 <EVP_DecryptInit_ex>       xor    r9d, r9d
   0x7ffff7e3eed3 <EVP_DecryptInit_ex+3>     jmp    EVP_CipherInit_ex@plt                <EVP_CipherInit_ex@plt>
    ↓
   0x7ffff7d42440 <EVP_CipherInit_ex@plt>    jmp    qword ptr [rip + 0x260c52]    <EVP_CipherInit_ex>
    ↓
   0x7ffff7e3e9e0 <EVP_CipherInit_ex>        push   r15
   0x7ffff7e3e9e2 <EVP_CipherInit_ex+2>      mov    r15, rdx
   0x7ffff7e3e9e5 <EVP_CipherInit_ex+5>      push   r14
   0x7ffff7e3e9e7 <EVP_CipherInit_ex+7>      push   r13
   0x7ffff7e3e9e9 <EVP_CipherInit_ex+9>      mov    r13, r8
   0x7ffff7e3e9ec <EVP_CipherInit_ex+12>     push   r12
   0x7ffff7e3e9ee <EVP_CipherInit_ex+14>     mov    r12, rcx
   0x7ffff7e3e9f1 <EVP_CipherInit_ex+17>     push   rbp
──────────────────────────────────────────────────────────────────────────────────────────[ STACK ]───────────────────────────────────────────────────────────────────────────────────────────
00:0000│ rsp 0x7fffffffdb78 —▸ 0x5555555557a1 ◂— cmp    eax, 1
01:0008│     0x7fffffffdb80 ◂— 0x0
02:0010│     0x7fffffffdb88 —▸ 0x555555574db0 ◂— 0x0
03:0018│     0x7fffffffdb90 ◂— 0xd000000000
04:0020│     0x7fffffffdb98 —▸ 0x555555574cd0 ◂— 0x2b3a99a3bb55b3fb
05:0028│     0x7fffffffdba0 —▸ 0x7ffff7cae880 (_IO_helper_jumps) ◂— 0x0
06:0030│     0x7fffffffdba8 ◂— 0xc /* '\x0c' */
07:0038│     0x7fffffffdbb0 —▸ 0x555555556008 ◂— 'someinitialvalue'
────────────────────────────────────────────────────────────────────────────────────────[ BACKTRACE ]─────────────────────────────────────────────────────────────────────────────────────────
 ► f 0   0x7ffff7e3eed0 EVP_DecryptInit_ex
   f 1   0x5555555557a1
   f 2   0x555555555d6f
   f 3   0x555555555e7d
   f 4   0x7ffff7b06d0a __libc_start_main+234
──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
pwndbg>
```

### Key

`supersecretkeyusedforencryption!`

A very obvious thing would be decoding any data from the pcap with this key.

```bash
» tshark -r trace.pcap -T fields -e data  



2000000000000000
5f558867993dccc99879f7ca39c5e406972f84a3a9dd5d48972421ff375cb18c

```

It happens to be AES-CBC(256 encryption), so we decode it with cyberchef, values as `key` as `supersecretkeyusedforencryption!`, IV as `someinitialvalue` and get flag back.


```
https://gchq.github.io/CyberChef/#recipe=AES_Decrypt(%7B'option':'UTF8','string':'supersecretkeyusedforencryption!'%7D,%7B'option':'UTF8','string':'someinitialvalue'%7D,'CBC','Hex','Raw',%7B'option':'Hex','string':''%7D,%7B'option':'Hex','string':''%7D)&input=NWY1NTg4Njc5OTNkY2NjOTk4NzlmN2NhMzljNWU0MDY5NzJmODRhM2E5ZGQ1ZDQ4OTcyNDIxZmYzNzVjYjE4Yw
```

#### Author: <a href="https://twitter.com/zexionx00">zexionx00</a>