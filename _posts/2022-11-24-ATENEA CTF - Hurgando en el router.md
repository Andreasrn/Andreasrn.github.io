---
layout:     post
title:      ATENEA | Hurgando en el router
date:       2022-11-24 21:00:00
summary:    Writeup to solve "Hurgando en el router" challenge under forensics category. This challenge is now retired and is worth 0 points.
categories: CTF
thumbnail: flag
tags:
 - CTF
 - Atenea
 - Router
 - Cracking
 - John
 - Password
---

The challenge is presented as follows:
> Durante la investigación de un incidente de seguridad se ha obtenido el firmware de un router TP-LINK WA801ND. Para superar este reto deberás obtener la contraseña de root del dispositivo.

We are expected to retrieve the root's password. For the resolution we are provided with a disc image belonging to the mentioned router.

![Router files](/images/ctf_hurgandorouter/2022-11-24 21.41.43.jpg)

This challenge should be straightforward for those with little experience in cracking, linux systems and/or ctfs. Therefore we will take the oportunity of talking about linux users and passwords.

### /etc/passwd and /etc/shadow

When talking about linux systems, /etc/passwd is a file which contains user accounts and login information. It contains a row per user, and includes information such as account name, groups, home directory and so on.

But what about passwords? Well, these are stored in its sibling file /etc/shadow. This file, which is only accessible by root, contains more sensitive account information such as password hash or expiration date.

### Back to the challenge

Having explained the relevant forensic artifacts, we will be most likely able to access the password hash and crack it.

First of all, we will check whether we have access to both files and whether the password hash is present: lucky us!

![/etc/passwd and /etc/shadow files](/images/ctf_hurgandorouter/files.jpg)

Fortunately _john_ has everything we need for an easygoing cracking session. First of all we must create two files: One containing our _/etc/passwd_ root's row (will be named _passwd_) and another containing the _/etc/shadow_ root's row (will be named _shadow_). Once we have both files prepared, we will use command _unshadow_ to combine both file's information into one single file that john can parse and crack.

```bash
┌──(kali㉿kali)-[~/challenge]
└─$ vim passwd
                                                                                                                                                  
┌──(kali㉿kali)-[~/challenge]
└─$ vim shadow                                                   
                                                                                                                                                  
┌──(kali㉿kali)-[~/challenge]
└─$ unshadow passwd shadow > unshadowed.txt
Created directory: /home/kali/.john
                                                                                                                                                  
┌──(kali㉿kali)-[~/challenge]
└─$ ls 
passwd  shadow  unshadowed.txt
```

Now we can execute john on our recently created _unshadowed.txt_ file with a wordlist. This time _rockyou.txt_ was enough.

```bash
┌──(kali㉿kali)-[~/challenge]
└─$ john --wordlist=~/rockyou.txt unshadowed.txt 
Using default input encoding: UTF-8
Loaded 1 password hash (sha512crypt, crypt(3) $6$ [SHA512 256/256 AVX2 4x])
Cost 1 (iteration count) is 5000 for all loaded hashes
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
0g 0:00:00:11 0.24% (ETA: 16:43:05) 0g/s 3796p/s 3796c/s 3796C/s kiki07..brookie1
0g 0:00:00:20 0.44% (ETA: 16:43:24) 0g/s 3775p/s 3775c/s 3775C/s klk123..honda21
prometheus       (root)     
1g 0:00:00:21 DONE (2022-11-24 15:28) 0.04633g/s 3772p/s 3772c/s 3772C/s pudgey..nana04
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
                                                                                                                                                  
┌──(kali㉿kali)-[~/challenge]
└─$
```
The password for root is *prometheus* and so is the flag!

Flag: __prometheus__

### Lessons Learnt
* Unshadow command prepares the information for you (I remember doing the process manually)