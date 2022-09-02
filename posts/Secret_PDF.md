---
layout: default
title : Redteam-TG -Secret_PDF Writeup
---

## Secret PDF Chall

```
Secret PDF

EASY BRUTEFORCE
Can you open it?

Flag : CTF_*
```

For this challenge we are given a pdf document that we have to read. Easy, just open it and read the flag?? Gift points.

Let's open our file to get the flag and validate our gift points. 

```
──(kali㉿kali)-[~/CTFs/HackerlabCTF/Basic/pdf]
└─$ evince secret.pdf 
```

Ooh We need a password to read the content of our document. I knew that they weren't the gift-giving type. 
I know a nice tool from the john suite that can help us.

### pdf2john.py or pdf2john.pl

Basically the tool extracts the hash inside the encrypted pdf that we can crack with the almighty `john`.

```
┌──(kali㉿kali)-[~/CTFs/HackerlabCTF/Basic/pdf]
└─$ ./pdf2john secret.pdf > hash 
                                                                                                                                                              
┌──(kali㉿kali)-[~/CTFs/HackerlabCTF/Basic/pdf]
└─$ cat hash 
secret.pdf:$pdf$4*4*128*-1060*1*16*15c0aee17f397540bdec4edb020a2247*32*447e5ab472a0d9557b9f8664bb73c00900000000000000000000000000000000*32*cc34bdea75a5d9ac0346d4a2adcb39ac72d8aeb6dc275e4b187fb19d3cdd2cf1:::::secret.pdf                                                                                                                             
```
it should be noted that with python3 you will have rather this. 

```
──(kali㉿kali)-[~/CTFs/HackerlabCTF/Basic/pdf]
└─$ python pdf2john secret.pdf                                         
b'secret.pdf':b'$pdf$4*4*128*-1060*1*16*15c0aee17f397540bdec4edb020a2247*32*447e5ab472a0d9557b9f8664bb73c00900000000000000000000000000000000*32*cc34bdea75a5d9ac0346d4a2adcb39ac72d8aeb6dc275e4b187fb19d3cdd2cf1':::::b'secret.pdf'

```
 which does not work as expected.

 Let's crack it quietly with john. And bingo!!! we have the password. The flag is waiting for us.

```
┌──(kali㉿kali)-[~/CTFs/HackerlabCTF/Basic/pdf]
└─$ john --wordlist=/usr/share/wordlists/rockyou.txt hash
Using default input encoding: UTF-8
Loaded 1 password hash (PDF [MD5 SHA2 RC4/AES 32/64])
Cost 1 (revision) is 4 for all loaded hashes
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
MyP@ssw0rd!      (secret.pdf)
1g 0:00:03:42 DONE (2022-09-02 08:39) 0.004485g/s 48484p/s 48484c/s 48484C/s MyPassword6..MyMayra1!
Use the "--show --format=PDF" options to display all of the cracked passwords reliably
Session completed

```

Oh no! at first sight the document is empty. what if we select the whole document? Oh well, there is a sequence of numbers at the bottom. It looks like something I know well.

A quick look at [decode](https://www.dcode.fr/identification-chiffrement) and it tells us that there is a chance that it has something to do with ascii. 

And voila!!!

`CTF_QRC0D3T0OCT4L?!!`

Done!!
