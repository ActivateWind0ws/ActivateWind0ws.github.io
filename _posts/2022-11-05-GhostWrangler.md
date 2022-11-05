---
title: Ghost Wrangler
tags: [DeadFace, ctf]
color: warning 
description: Writeup for Ghost Wrangler from DeadFace CTF event.
---
# Writeup for Ghost Wrangler

## Description

```text
Who you gonna call?
```

A 64-bit elf is given in downloaded file. Just running the program says that the flag is trapped in some weird text. Even opening in ghidra, the UI shows it very different. Opening the binary is radare2 gives some values that is xored with `0x13`. XORing it again with `0x13` gives the original value.

```bash
» ./ghost 
|                                       _| I've managed to trap the flag ghost in this box, but it's turned invisible!
Can you figure out how to reveal them?
```

## Flag

```bash
[0x7fd93245d090]> pdf @sym.get_flag
            ; CALL XREF from main @ 0x561d7a2d31cf(x)
┌ 109: sym.get_flag ();
│           ; var int64_t var_4h @ rbp-0x4
│           ; var int64_t var_10h @ rbp-0x10
│           0x561d7a2d3155      55             push rbp
│           0x561d7a2d3156      4889e5         mov rbp, rsp
│           0x561d7a2d3159      4883ec10       sub rsp, 0x10
│           0x561d7a2d315d      bf29000000     mov edi, 0x29           ; ')' ; 41
│           0x561d7a2d3162      e8e9feffff     call sym.imp.malloc     ;  void *malloc(size_t size)
│           0x561d7a2d3167      488945f0       mov qword [var_10h], rax
│           0x561d7a2d316b      488b45f0       mov rax, qword [var_10h]
│           0x561d7a2d316f      ba29000000     mov edx, 0x29           ; ')' ; 41
│           0x561d7a2d3174      be00000000     mov esi, 0
│           0x561d7a2d3179      4889c7         mov rdi, rax
│           0x561d7a2d317c      e8bffeffff     call sym.imp.memset     ; void *memset(void *s, int c, size_t n)
│           0x561d7a2d3181      c745fc000000.  mov dword [var_4h], 0
│       ┌─< 0x561d7a2d3188      eb2a           jmp 0x561d7a2d31b4
│      ┌──> 0x561d7a2d318a      8b45fc         mov eax, dword [var_4h]
│      ╎│   0x561d7a2d318d      4898           cdqe
│      ╎│   0x561d7a2d318f      488d158a0e00.  lea rdx, obj._          ; 0x561d7a2d4020 ; "[GQh{'f}g wLqjLg{ Lt{#`g&L#uLpgu&Lc'&g2n%s\r|\x1b[4m%*.c\x1b[24m| I've managed to trap the flag ghost in this box, but it's turned invisible!\nCan you figure out how to reveal them?\n"
│      ╎│   0x561d7a2d3196      0fb60410       movzx eax, byte [rax + rdx]
│      ╎│   0x561d7a2d319a      83f013         xor eax, 0x13           ; 19
│      ╎│   0x561d7a2d319d      89c1           mov ecx, eax
│      ╎│   0x561d7a2d319f      8b45fc         mov eax, dword [var_4h]
│      ╎│   0x561d7a2d31a2      4863d0         movsxd rdx, eax
│      ╎│   0x561d7a2d31a5      488b45f0       mov rax, qword [var_10h]
│      ╎│   0x561d7a2d31a9      4801d0         add rax, rdx
│      ╎│   0x561d7a2d31ac      89ca           mov edx, ecx
│      ╎│   0x561d7a2d31ae      8810           mov byte [rax], dl
│      ╎│   0x561d7a2d31b0      8345fc01       add dword [var_4h], 1
│      ╎│   ; CODE XREF from sym.get_flag @ 0x561d7a2d3188(x)
│      ╎└─> 0x561d7a2d31b4      8b45fc         mov eax, dword [var_4h]
│      ╎    0x561d7a2d31b7      83f827         cmp eax, 0x27           ; 39
│      └──< 0x561d7a2d31ba      76ce           jbe 0x561d7a2d318a
│           0x561d7a2d31bc      488b45f0       mov rax, qword [var_10h]
│           0x561d7a2d31c0      c9             leave
└           0x561d7a2d31c1      c3             ret
[0x7fd93245d090]>
```

```
https://gchq.github.io/CyberChef/#recipe=XOR(%7B'option':'Hex','string':'0x13'%7D,'Standard',false)&input=W0dRaHsnZn1nIHdMcWpMZ3sgTHR7I2BnJkwjdUxwZ3UmTGMnJmcybiVzXHJ8XHgxYls0bSUqLmNceDFiWzI0bXw
```

```text
HTB{h4unt3d_by_th3_gh0st5_0f_ctf5_p45t!}
```

#### Author: <a href="https://twitter.com/zexionx00">zexionx00</a>