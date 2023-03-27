---
layout:     post
title:      ATENEA | Servidor web
date:       2023-03-27 00:00:00
summary:    Writeup to solve "Servidor web" challenge under traffic analysis category. This challenge is now retired and is worth 0 points.
categories: CTF
thumbnail: flag
tags:
 - CTF
 - Atenea
 - DHCP
 - UDP
 - Shellshock
 - CVE-2014-6271
---

The challenge is presented as follows:
> Ha comprometido el servidor web de la DMZ de tu organización. Es imprescindible identificar la vulnerabilidad explotada para poder parchear el servidor y evitar futuros ataques. Indica el CVE asociado a la vulnerabilidad explotada (CVE-XXXX-XXXX)

We must identify which CVE has been exploited to compromise the DMZ, for which we are provided with a .pcap.

My methodology has completely failed for this challenge. Although I got the flag without human help, I arrived to the flag before I understood the challenge. Anyhow, it is a great oportunity to learn from my mistakes!

The first check for traffic analysis challenges is the protocol hierarchy.
![Protocol hierarchy](/images/ctf_webserver/protocols.png)

My earliest bad decision was to disregard non-TCP traffic, mostly due to the fact that I can't recall any past challenge where non-TCP traffic was the clue. Therefore I focused on TCP only. These were the steps I followed:
1. VNC traffic overview and RCE-related CVE's lookup (No relevant findings)
2. HTTP traffic (Oh my god is that a /etc/password access attempt? Wait, I found you, you dirty Apache Continuum exploit! Oh wait...you have no assigned CVE! And you seemed to fail!)

(I spent so much time looking for the Apache Continuum vulnerability...which anyways hadn't succeeded to exploit due to the POST method not being allowed. It was a sort of clever rabbit hole and I wanted to believe I guess).

All I could see on the traffic was a successful `GET /password` that retrieved _/etc/passwd_ file, but nothing else (besides obvious failed attempts and that kind of noise). And that was definitely not a CVE I could look for.

## The shortcut

After some hours of no progress I searched the PCAP hash in Virustotal, which led me to a perfectly tagged result:
![Virustotal results](/images/ctf_webserver/virustotal.png)

There it is!

Flag: CVE-2014-6271 

But hey...Now we know the answer we can easily find the answer in the PCAP and figure out why we missed it, don't we? DON'T WE?

The universe: Hold my beer...

Well, it turns out the CVE-2014-6271 is named after "Shellshock", and it's official description is the following:
> GNU Bash through 4.3 processes trailing strings after function definitions in the values of environment variables, which allows remote attackers to execute arbitrary code via a crafted environment, as demonstrated by vectors involving the ForceCommand feature in OpenSSH sshd, the mod_cgi and mod_cgid modules in the Apache HTTP Server, scripts executed by unspecified DHCP clients, and other situations in which setting the environment occurs across a privilege boundary from Bash execution, aka "ShellShock." NOTE: the original fix for this issue was incorrect; CVE-2014-7169 has been assigned to cover the vulnerability that is still present after the incorrect fix.

I tried so hard to find cgi-related HTTP requests with no luck...and all tutorials I found out there to exploit the vulnerability either leveraged metasploit either abused cgi-bin exposed paths, two ways that I couldn't find in the PCAP.

I started reading all triggered rules in Virustotal for this PCAP aiming to know a bit more about what behaviors were found in it, and luckily spotted the following: *ET EXPLOIT Possible CVE-2014-6271 exploit attempt via malicious DHCP ACK [2019237]*

So...DHCP seems to be part of the solution. Googling about it led me to learn that a malicious DHCP server could be an exploitation vector
> Some DHCP clients can also pass commands to Bash; a vulnerable system could be attacked when connecting to an open Wi-Fi network. A DHCP client typically requests and gets an IP address from a DHCP server, but it can also be provided a series of additional options. A malicious DHCP server could provide, in one of these options, a string crafted to execute code on a vulnerable workstation or laptop.

Back in the PCAP, after filtering the DHCP traffic, quickly found malicious bash commands:
![Protocol hierarchy](/images/ctf_webserver/exploitation.png)

The command `/bin/cp /etc/passwd /var/www/html` copies the file _/etc/passwd_ to the web's root path. This is what caused that a _GET /passwd_ returned _/etc/passwd_ file.

### Lessons Learnt
* If known vectors fail (TCP traffic) it might be worth it to check those that you usually overlook.
* Proper enumeration is the key. The CVE description already warned about DHCP, however, I only checked the lowest hanging fruits (HTTP). I didn't even read the full description.
* Things happen for a reason. If _/etc/passwd_ is accessible from the web's root path there must be an explanation (and thinking that the web is hosted in /etc IS NOT a reasonable explamation in most of the cases).
* Virustotal can come in handy, but make sure you learn what you missed after using it.