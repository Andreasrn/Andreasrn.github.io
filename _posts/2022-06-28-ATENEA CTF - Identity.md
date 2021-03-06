---
layout:     post
title:      ATENEA | Identity
date:       2022-06-28 05:00:00
summary:    Writeup to solve "Identity" challenge under OSINT category. This challenge is now retired and is worth 0 points.
categories: CTF
thumbnail: flag
tags:
 - CTF
 - Atenea
 - OSINT
---
The challenge description is as follows:
> Obtén la identidad secreta de la persona detrás del siguiente screenshot (deberás ponerlo todo en minúsculas).

We have an initial PNG file which we must analyze to figure out a secret identity.

![Initial challenge image](/images/identity_initial.png)

Taking a look into the image details we can somehow identify several groups of information:
* Application list (apparently useless)
* Code snippets (doesn't look interesting after examining its overal content)
* Hex decoded strings (meaningless so far, but may come in handy later on in the challenge)
* QR code in the background

The most promising information group is the QR code. However, its visibility isn't good enough to be read. We will clean it up with an image editor.
1. Crop the image to fit the QR code.
2. Increase brightness, contrast and B&W intensity to achieve proper white and black tones.
3. Use the color picker to paint over the icons that slightly overlap the QR code. It is easy to infer where the black pixels go (it doesn't need to be quite precise)

The cleaned up code looks as follows and can be read by a QR reader app (I used Lens):
![Cleaned QR code](/images/cleaned_up_qr.png)

It leads to the following URL: goo[.]gl/bNi7tK
Such URL redirects to hXXps://mega.nz/file/qrh1hASK#xK6mg4Byu0VfouY1crLx68 pJyWC6G4_LwHfSFPEiwP4 and contains the following image, which corresponds to a boarding pass:
![Boarding pass](/images/ticket.jpg)

Its metadata contains some interesting fields:
```
...
Comment: NaNaNaNana
...
Gps Latitude: 26 deg 21' 28.43" N
Gps Longitude: 127 deg 47' 1.71" E
Gps Position: 26 deg 21' 28.43" N, 127 deg 47' 1.71" E
```
We can use the GPS position to perform a search in Google Maps (26º21'28.43" N, 127º47'1.71" E). This is what the map reveals:
![Location](/images/batman_location.png)

This image and the comment suggest that the secret identity is... NaNaNaNana: *batman*