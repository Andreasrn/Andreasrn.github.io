---
layout:     post
title:      How to | Setup an online Kali-like virtual machine (I)
date:       2022-07-30 10:00:00
summary:    A brief tutorial to set up an online VM with the necessary tools to work on CTFs and related, while accessible from any device (part 1).
categories: Tutorial
thumbnail: wrench
tags:
 - CTF
 - Kali
 - VPS
 - Tools
 - Attack Box
 - Virtual Machine
 - Tutorial
---

If you are used to work from different devices you may have come across a common problem: To keep your working settings along. I personally work from 3 different devices: A Windows laptop, a Windows desktop and a MacOs laptop. I also prefer to keep my working tools on VMs or dual boot. For this reason, depending on where I am, I have access to a subset of all my VMs.

For instance, I have a reversing VM on my Windows desktop. But if I attended to a conference with my MacOS device, I would be unable to follow any reversing workshop. Nontheless, the worst part is to not to have a single Kali linux (or similar) instance ready to use from any of those devices I have access to. Kali-linux offers a good set of tools that would help me to carry out some bug bounty, a wide variety of CTF challenges and little pentesting. It would be like my toolbox, and it must be accessible in most situations.

And here is were it comes the tutorial: I have built and online VM with Kali tools installed and it is graphically accessible with a decent performance. And I'll explain how to do it. 

This article covers the deployment and basic configuration that are needed to have a basic functional remote VM. The next part will go through the process of performing further configuration such as useful browser extensions, launchers creation and terminal prompt adjustment.

### Disclaimers
I have no system administration background and therefore I'm not quite familiar with many usual tasks such as domain registration, server management and stuff. Thus, this tutorial considers the reader as unexperienced as I am :)

Also, there are plenty of ways to achieve this. I'm just trying to keep it simple and cheap.

# Step 1: Cloud infrastructure acquisition

Our very first step should be to acquire a portion of the cloud to host your VM. There is a wide variety of options, including setting up a server at home (cheaper - harder) and purchasing the services of cloud providers such as AWS, Azure or Digital Ocean (more expensive - easier). Some of these even would allow to import our custom ISO, which translates to installing a real Kali instance.

However, I decided to pay for a Virtual Private Server (VPS). A VPS is essentially a hosting that virtualizes a dedicated server, although it is actually running on a shared one. If you don't need high performance, this option is an affordable alternative.

There are tons of VPS providers, just compare prices and make a choice based on your budget. In my particular case I purchased a 1-year VPS on OVHCloud with the following specifications:
* 2 Cores
* 4 GB RAM
* 80 GB storage

I also paid an extra to have snapshots available: For me, this is a must as I like to work from freshly installed instances.

The monthly price, for you to have a reference, ends up being around 14€ (taxes included). My recommendation is to look for something with my specs or superior. 

Deploy it with Debian 11 (or your favourite SO) and you'll receive its public IP along with the credentials to access it via CLI. For this tutorial I'll assume you choose Debian too. Connect via PuTTY or similar and start configuring it (next part).

Summary of this step: Acquire a VPS in which you can install Debian 11 or a similar distro.

# Step 2: Basic configuration

At this point you have a fresh Debian instance, for which you have been provided with an username/password pair. We will start configuring it step by step.

### User management

First, we will create an user to work on that is different from the default. We will also disable shell on root and the default user, as a security measure.
```plain
# Create custom user
sudo adduser <HERE_YOUR_USERNAME>

Add user to sudoers
sudo usermod -aG sudo <HERE_YOUR_USERNAME>

# Switch to recently created user
su <HERE_YOUR_USERNAME>

# Edit file to disable console for root and default user (debian)
sudo vim /etc/passwd
```

To disable the shell, open the file _/etc/passwd_ with your preferred text editor (vim/nano) and change _/bin/bash_ to _/bin/false_ in _root_ and your default user (mine was _debian_). Save the changes and exit.

(snapshot at this point if possible)

### GUI installation

Now we will install a desktop environment to work with. The choice of an environment depends on your computing resources and preferences. My initial choice was GNOME but it didn't run smoothly enough, so I tried XFCE instead. This one worked better, but had a login issue that couldn't resolve and ended up installing MATE. It does not only look pretty well, but also seems to be lightweight enough to run pretty decently with a good screen resolution.

Install MATE with the following command, or look up the commands needed to install the desktop environment of your choice.
```plain
sudo apt update && sudo apt install mate-desktop-environment
```

### Remote access

Once we have a GUI, it is time to use it. Among the possible access methods, I'd use VNC or NoMachine. VNC could be eventually accessed from the web browser (which is a nice feature) but I wanted to give a try to NoMachine. These are the steps to install it.
```
wget https://download.nomachine.com/download/7.10/Linux/nomachine_7.10.1_1_amd64.deb
sudo dpkg -i nomachine_7.10.1_1_amd64.deb
```
It will automatically set up a running server listening on port 4000. To access the machine, install NoMachine client in your preferred devices and connect to it by providing the public IP of your VPS and your user credentials.

It does not matter what remote access tool (hehe) you install, but by the end of this step you must be able to interact with your SO graphically.

Summary of this step: Have a working user to access your instance graphically from a remote device.

(snapshot at this point if possible)

# Step 3: Build your toolset

This is the more interesting (yet tedious) part. Now we have a working instance that we can access and interact to from any device (the point of this tutorial) it is time to install the tools we need and eventually organize them properly.

### Download Kali tools
Although you have all the freedom to install whatever you prefer, my needs are close to what Kali offers, thus, I borrowed this script from Github to install all the tools:
[makekali.sh](https://gist.githubusercontent.com/warecrash/f35d4f9a822c452b0c54bbdb47c0c9a5/raw/8f7055e1cde8ae03f19f57d0154f259e9f8f3060/makekali.sh)

Download, make executable and run it with the following commands:
```plain
wget https://gist.githubusercontent.com/warecrash/f35d4f9a822c452b0c54bbdb47c0c9a5/raw/8f7055e1cde8ae03f19f57d0154f259e9f8f3060/makekali.sh
sudo chmod +x makekali.sh
sudo ./makekali.sh
```

It will take some time, but in the end you will have a "Kali" login screen, two themes (dark/light), a wallpaper available, and all the tools installed.

Summary of this step: Download the tools you need and configure them properly so that they are accessible according to your needs.

(snapshot at this point if possible)

# Conclusion

Throughout this tutorial we have achieved a functional remote VM with several offensive security and analysis tools, which can be accessed from any device at any time. The following part will cover the configuration of some aspects of the instance to make it smoother to use.