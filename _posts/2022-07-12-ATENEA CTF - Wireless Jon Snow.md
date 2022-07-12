---
layout:     post
title:      ATENEA | Wireless Jon Snow
date:       2022-07-12 10:00:00
summary:    Writeup to solve "Wireless Jon Snow" challenge under traffick analysis category. This challenge is now retired and is worth 0 points.
categories: CTF
thumbnail: flag
tags:
 - CTF
 - Atenea
 - Wireless
 - Wireshark
 - FTP
 - Cracking
---
The challenge is presented as follows:
> Nuestro Red Team ha conseguido monitorizar por un tiempo el tráfico wireless de una de las redes de la organización objetivo. Se sospecha que desde dicha red el usuario Jon Snow se conecta con el servidor FTP de la compañía. Tu objetivo es analizar el pcap y tratar de localizar la contraseña FTP de dicho usuario.

Our goal is to identify a FTP password within a network capture. This should be easy as FTP authentication is done in plain text and the password should be right in front of us...right?

![Encrypted traffic](/images/ctf_wirelessjonsnow/protocols.png)

Upon opening the .cap file (note that it is not .pcap) we can quickly realize that the content is somehow unreadable. The outstanding protocol, if any, is 802.11 (Wi-Fi standard). This means that this traffic has been obtained from outside the network where it was taking place. Therefore the traffic is encrypted for us. And before moving on, we'll revew a little bit of necessary theory.

### IEEE 802.11 and the 4-way handshake
As abovementioned, IEEE 802.11 stands for the family of standards that make wireless LANs exist. In other words, what we understand as Wi-Fi. When it comes to home (or not corporate) networks the authentication method is often a pre-shared key. A pre-shared key is a passphrase that both the Access Point (AP, router, etc) and the supplicant (device connecting to the AP) know beforehand.

The authentication doesn't happen with the supplicant sending the password to the AP over the network in plain text (this way, a simple sniffing of the network would leak the passphrase). Instead, a 4-way handshake begins in order to both devices to authenticate each other without actually sending the passphrase directly (EAPOL, or Extensible authentication protocol). A good resource on understanding this process can be found here:
[4-way handshake explanation](https://www.wifi-professionals.com/2019/01/4-way-handshake)

For this challenge we only need to bear in mind that the key that will end up decrypting the traffic will be necessarily derived from the SSID (name of the WLAN) and the passphrase. 

### Back to the challenge

We know that in order to decrypt the traffic we must  figure out the SSID and the pre-shared key, or passphrase.

#### SSID

This one is easy. First of all, we must identify if the handshake has been captured by looking for EAPOL packets, and indeed it has. Few packets back, we can see the probe requests and responses that includes de SSID.

![SSID name](/images/ctf_wirelessjonsnow/ssid.png)

The SSID is: Earthrealm

#### Passphrase

Here it comes the "tricky" part. We don't know the passphrase and that should prevent us from reading packets from a network we haven't authenticated to.

My first approach was to check the default password for the specific router model, which turned out to be a Realtek Soho (this information can be found in the authentication packets as well). However, it didn't turn out.

Then I proceeded to crack it. This article has a great break down of the cracking process, which needs many pieces of data such as the SSID and some of the keys that are exchanged during the EAPOL process. All this information is, by definition, present in our packet capture.

[WPA2 pre-shared key cracking](https://www.ins1gn1a.com/understanding-wpa-psk-cracking)

However, we don't need to go through this process on our own. We'll use _aircrack-ng_ to crack the password by providing it with the packet capture, a wordlist (rockyou.txt will do the trick for us) and the SSID we want to crack. The tool will then grab all the data it needs from the packet capture and start a cracking process (basically automating what the abovementioned article explains).

```
aircrack-ng <path to your encrypted .cap file> -w /usr/share/wordlists/rockyou.txt
```
Around 10 minutes later it has found a valid passphrase: sysadmin1

![Cracked password](/images/ctf_wirelessjonsnow/cracking.png)


Now we can decrypt the traffic! Although Wireshark implements a feature to decrypt 802.11 traffic providing the SSID and passphrase, I could make it work for me :| Nonetheless, I leveraged a different tool to automatically generate a decrypted pcap: 
```bash
airdecap-ng -o decrypted.pcap -e Earthrealm -p sysadmin1 <path to your encrypted .cap file>
```
Et voilá!

![Cracked password](/images/ctf_wirelessjonsnow/decrypted_traffic_command.png)

The .pcap is ready to analyze via Wireshark. However, we don't need to go quite further to spot the user and the password for the FTP authentication process:

![Cracked password](/images/ctf_wirelessjonsnow/ftp_info.png)

The flag is the FTP password: __Subzer0_2017__

### Lessons Learnt
* How WPA2 authentication works
* How to decrypt WPA2 protected traffic, knowing and not knowing the passphrase