---
layout:     post
title:      ATENEA | Really???
date:       2023-03-29 00:00:00
summary:    Writeup to solve "Really???" challenge under cryptography and steganography category. This challenge is now retired and is worth 0 points.
categories: CTF
thumbnail: flag
tags:
 - CTF
 - Atenea
 - Snow
 - Rockyou.txt
 - PGP
---

The challenge is presented as follows:
> Durante el estudio del disco duro del ordenador de un sospechoso se ha encontrado un fichero cifrado mediante PGP. Al no encontrarse ninguna clave privada dentro del equipo se sospecha que dicho fichero esté cifrado mediante cifrado simétrico.Por otro lado, todas las contraseñas obtenidas de varias cuentas del sospechoso (a partir de la investigación de su equipo) tienen las siguientes características:
> - Son de longitud 6 o 7
> - Sólo contienen letras minúsculas
> - Sólo se utilizan estas letras: qwertyiopnmjk
> - No se repite ninguna de las letras de la contraseña
> - Algunas de ellas contiene únicamente un número entre estos: 013
>
> Ninguna de esas contraseñas ha servido para descifrar el fichero, pero quizás haya sido cifrado con una contraseña con estas mismas características. No sabemos si el contenido del fichero es relevante para la investigación, pero sólo hay una forma de averiguarlo...

We are provided an .asc file containing a PGP message as starting point.

It has been quite a chaotic challenge for me. I ended up looking up the final part in a published writeup because I was so curious about what the next step would be...And when I found out I felt so bad because that was one of the possible ways I was gonna try the next day >:( This was a lessont learnt itself.

Well, the challenge description is already quite clear about what is expected from us. We are given an encrypted message (symmetrically, but we'll dig deeper in this later on) and the characteristics of the password. There are a few ways of doing this.
1. First, I tried to generate a password dictionary based on the information we know, but not only was it massively big, but didn't know how to filter out words with no repeating chars. I had a script running for several hours with no luck, and decided to try something different.
2. Then, I made the assumption the password was in rockyou.txt and focused on filtering all the passwords that matched the given pattern.

To do this, I built the following script:
```
import re

rockyou_raw = ""

with open("rockyou.txt","r",encoding='latin-1') as f:
    rockyou_raw = f.readlines()

rockyou_raw = [x.strip() for x in rockyou_raw]

rockyou_filtered = []

for word in rockyou_raw:
    if len(word) == 6 or len(word) == 7:
        x = re.search("^(?:([qwertyiopnmjk013])(?!.*\1)){6,7}$",word)
        if x:
            rockyou_filtered.append("\n"+x[0])

with open("my_wonderful_wordlist.txt","w") as f:
    f.writelines(rockyou_filtered)
```

Terrible code but it was too late and I just wanted to finish and go to bed.

This script reads the _rockyou.txt_ words and, using a regular expresion, we save those words that match the pattern given in the challenge description. Then it saves them to a new, smaller file.
```
(base) thymine@MacBook-Pro-de-thymine % wc -l rockyou.txt 
 14344394 rockyou.txt
(base) thymine@MacBook-Pro-de-thymine % wc -l my_wonderful_wordlist.txt 
   51183 my_wonderful_wordlist.txt
(base) thymine@MacBook-Pro-de-thymine % 

```

To crack the message password, I used _john_ and its utility _gpg2john_.
```
(base) thymine@MacBook-Pro-de-thymine ~ % /usr/local/Cellar/john-jumbo/1.9.0_1/share/john/gpg2john Downloads/message.gpg > to_crack 
File Downloads/message.gpg
(base) thymine@MacBook-Pro-de-thymine ~ % john --wordlist="my_wonderful_wordlist.txt" to_crack
Warning: detected hash type "gpg", but the string is also recognized as "gpg-opencl"
Use the "--format=gpg-opencl" option to force loading these as that type instead
Using default input encoding: UTF-8
Loaded 1 password hash (gpg, OpenPGP / GnuPG Secret Key [32/64])
Cost 1 (s2k-count) is 35651584 for all loaded hashes
Cost 2 (hash algorithm [1:MD5 2:SHA1 3:RIPEMD160 8:SHA256 9:SHA384 10:SHA512 11:SHA224]) is 2 for all loaded hashes
Cost 3 (cipher algorithm [1:IDEA 2:3DES 3:CAST5 4:Blowfish 7:AES128 8:AES192 9:AES256 10:Twofish 11:Camellia128 12:Camellia192 13:Camellia256]) is 7 for all loaded hashes
Press 'q' or Ctrl-C to abort, almost any other key for status
monkey3          (?)
1g 0:00:00:04 DONE (2023-03-29 18:26) 0.2347g/s 27.69p/s 27.69c/s 27.69C/s monkey3
Use the "--show" option to display all of the cracked passwords reliably
Session completed
(base) thymine@MacBook-Pro-de-thymine ~ % 
```
As we can see in _john's_ output, the password is *monkey3*.

Now we know the password, we can decypher the message with the following command:
```
(base) thymine@MacBook-Pro-de-thymine ~ % gpg --decrypt message.gpg > plain
```
After being prompted for the password, we will have the plain message.
```
Imposible me ha sido rehusarme á las repetidas instancias que el	  
Caballero Trelawney, el Doctor Livesey y otros muchos señores me han 	 
hecho para que escribiese la historia circunstanciada y completa de la   
Isla del Tesoro. Voy, pues, á poner manos á la obra contándolo todo,  
desde el _alfa_ hasta el _omega_, sin dejarme cosa alguna en el tintero,     
exceptuando la determinación geográfica de la isla, y esto tan solamente 
porque tengo por seguro que en ella existe todavía un tesoro no      	      
descubierto. Tomo la pluma en el año de gracia de 17--y retrocedo hasta    
la época en que mi padre tenía aún la posada del "_Almirante Benbow_," y  
hasta el día en que por primera vez llegó á alojarse en ella aquel viejo
marino de tez bronceada y curtida por los elementos, con su grande y       
visible cicatriz.  	    	 	    	    	   	 	     	    	      	      
[...]
Le pregunté, desde luego, en qué podía servirle y él me contestó que    
deseaba tomar un poco de rom, pero apenas iba yo á salir de la sala en	  
busca de lo que pedía cuando se sentó á una de las mesas excitándome á  
que me acercase á él. Yo me detuve en el sitio en que su indicación me 
había cogido, teniendo en mi mano una servilleta. 	    	  
	  	 
--Ven aquí, muchacho, me repitió, acércate más.
```

I will save you some time: The text does not mention any flag, and belongs to a famous novel (Treasure Island). Therefore the challenge must go on.
> **_NOTE:_**  This was the moment when I was a bit exhausted and wanted to go to bed, so I looked up the solution. Nonetheless, I will refer to this part as I would have done if didn't know the solution. I followed these exact steps despite knowing the solution just to see how far would I have been from solving it on my own.

After analyzing the text searching for odd patterns, _I_ thought of (haha) hidden info, something related to whitespaces. Googling about it _I_ found an steganography tool named *snow* which uses whitespaces (that are not visible to the human eye) to hide information. In fact, if we select the text we can spot some anomalies:
![Rare whitespaces](/images/ctf_really/whitespaces.png)

Using the tool on the plain file we observe the following output:
```
(base) thymine@MacBook-Pro-de-thymine ~ % snow plain
Receta:

- Un poco de ron negro
- Una cucharada de azúcar
- Zumo de lima
- Una rama de canela
- Agua hirviendo

Se mezclan todos los ingredientes, añadiendo agua hirviendo hasta llenar el vaso.

Solución al reto: la bebida de la receta 2 veces seguidas (y en minúsculas!)

(base) thymine@MacBook-Pro-de-thymine ~ % 
```
Looking up the ingredients we find that are related to a drink named "Grog". Following the instructions...

*Flag: groggrog*

### Lessons Learnt
* Be patient, don't let curiosity ruin your chances of figuring out on your own
* In stego texts, select the text (not kidding) to reveal hidden artifacts or chars, as well as suspicious whitespaces patterns
* PGP messages may have been encrypted symmetrically
* John can crack them