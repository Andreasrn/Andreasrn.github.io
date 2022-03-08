---
layout:     post
title:      ATENEA | Inline
date:       2022-03-07 10:00:00
summary:    Writeup to solve "Inline" challenge under Traffic Analysis category. This challenge is now retired and is worth 0 points.
categories: CTF
thumbnail: flag
tags:
 - CTF
 - Atenea
 - Wireshark
---
The challenge is presented as follows, along with a pcap file.
> El siguiente fichero pcap recoge las últimas conexiones establecidas por un equipo de nuestra organización justo antes de ser comprometido. Los atacantes han conseguido instalar cierto tipo de backdoor en la máquina. ¿Podrías identificar el password empleado por los atacantes?

When it comes to traffic analysis challenges I tend to start off by reviewing http activity. There are some GET requests to outstanding filenames.

(here it goes an image)

The following files have been served:
* cgi.html
* cgi.payload
* /zri/6.prednaska/tools/OllyDbg/110_withPlugins/

The site related to OllyDBG didn't show up anything interesting so I focused on the first downloaded file: cgi.html, which I checked and found what looked like obfuscated js code:

(here it goes an image)

I copied all the code to a file named site.html and opened it with a web browser. The following file was downloaded: cgi.vbs

It consisted on a obfuscated Visual Basic script. To figure out its output without actually executing potential malicious code, I changed the word "Execute" into "Wscript.Echo", so instead of executing the deobfuscated code, it would only show it in clear text.

After executing it with cscript I obtained more code and saved it as trojan.vbs
It contained more obfuscated code, but in the end we could see that:
* It uses SharpShooter to create the payload. The obfuscated code from this file is likely the packed source files (C#)
* It creates what seems to be an instance of SharpShooter (variable o) and executes functions CheckPlease and Go

We revised the source code in GitHub to understand what these functions do:

### CheckPlease
It performs some checks to find out if it is being executed within a virtual environment
### Go
This is the function which actually executes the malware. The following explanation has been learned from the Github sources aswell.
The function receives the following parameters:
* RefStr: "mscorlib.dll"
* NameSpace: "ShellcodeInjection.Program"
* EntryPoint: "Main"
* Technique: 1
* StageHost: "http://192.168.43.242:8000/cgi.payload"

It basically downloads the payload from hXXp://192.168.43.242:8000/cgi.payload (this can be found in the pcap file), decodes it from b64 and unzips it (all with custom functions). To run the code in the same manner that the real malware but only to obtain the code from the payload, I needed to copy some functions and modify some parts of them. This was the result, that should be executed in an C# online compiler and executer.

(here it goes two images)

The retrieved code is stored within a file and reviewed again. We can spot the following code being inserted to a variable:

(here it goes an image)

We clean up the commas and "x" so we obtain the hex content and decode it.

(here it goes an image)

We can notice at the end that an user is added and its password (and flag) is LulzSecC#11!