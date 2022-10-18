---
title: Cracking NTLM
tags: [DeadFace, ctf, cracking]
color: warning 
description: Writeup for Cracking NTLM from DeadFace CTF event.
---
# Writeup for Cracking NTLM

## Description

*Took first blood as well :p*

```text
DEADFACE member lilith got into our Domain Controller and dumped our hashes and claims she got an accounts credentials! Can you figure out what username and password she may have gotten?
```

We're provided with SAM and SYSTEM of a compromised DC. Downloading it and running secretsdump locally gives the compromised username and hashes. Crack the hashes and one of them is flag in a format of `flag{username_password}`.

```bash
» secretsdump.py -sam sam -system system local > tmp
» cat tmp | grep aad3 | awk -F\: '{print $4}' > hashes
» john --wordlist=/opt/SecLists/Passwords/Leaked-Databases/rockyou.txt hashes
Using default input encoding: UTF-8
Loaded 7 password hashes with no different salts (NT [MD4 256/256 AVX2 8x3])
Warning: no OpenMP support for this hash type, consider --fork=4
Press 'q' or Ctrl-C to abort, almost any other key for status
                 (?)
adminadmin       (?)
2g 0:00:00:00 DONE (2022-10-18 16:08) 3.076g/s 22067Kp/s 22067Kc/s 110909KC/s  Ttwwl789..*7¡Vamos!
Warning: passwords printed above might not be all those cracked
Use the "--show --format=NT" options to display all of the cracked passwords reliably
Session completed.
» cat ~/.john/john.pot
$NT$31d6cfe0d16ae931b73c59d7e0c089c0:
$NT$3e126da93e034356d4e8cc3e0dd24357:adminadmin
» grep 3e126da93e034356d4e8cc3e0dd24357 tm
itadmin:1001:aad3b435b51404eeaad3b435b51404ee:3e126da93e034356d4e8cc3e0dd24357:::
```

And we have flag now.

# Flag

`flag{itadmin_adminadmin}`

#### Author: <a href="https://twitter.com/zexionx00">zexionx00</a>

