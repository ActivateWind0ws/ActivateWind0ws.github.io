---
title: Inode What You Did Last Summer
tags: [DeadFace, ctf, forensics]
color: primary 
description: Writeup for Inode What You Did Last Summer from DeadFace CTF event.
---

# Writeup for Inode What You Did Last Summer

## Description

```text
One of the security researchers at ESU needs help in their forensics of the ESU attack that occurred on 2022-07-27. They are asking if you can help them find the Inode of the file that was compromised and enabled the attackers to escalate their privileges to root.

Submit the flag as flag{inode_value}.
```

The download zip files has an Ubuntu Generic zip file and a memory dump, which directly hints to add a plugin of that zip file. After adding, we analyze the memory dump with volatilty.

```bash
» python2 /opt/volatility/vol.py --profile=LinuxUbuntu_5_4_0-122-genericx64 -f esu-mem-20220727154029.dmp linux_enumerate_files

---

0xffff959183253cd8                    788070 /opt/backup.py
---
```

# Flag

`flag{0xffff959183253cd8}`

*If the `/opt/backup.py` doesn't make sense, all the challs are related to each other. One of the chall, we have to find the file for the flag.*

#### Author: <a href="https://twitter.com/zexionx00">zexionx00</a>