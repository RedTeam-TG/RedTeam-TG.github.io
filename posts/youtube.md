---
layout: default
title : Redteam-TG -Youtube Writeup
---

## Youtube challenge

```
Youtube

EASY BITS
Check the announcement video of the hackerlab https://www.youtube.com/watch?v=77yjGguVM0U

Flag : CTF_*

```
once on the youtube channel and at the bottom of the video description we can see a binary sequence.
First reflex, a decoding tool, personally I use [asciitohex](https://www.asciitohex.com/) when I already know the encoding.

```
00100001 00100001 00100001 00110010 00110011 00110110 00110001 00110001 00110010 00110110 00110010 00110111 01101001 01110100 00110000 01000111 00110001 01011111 01000110 01010100 01000011
```
the result we get is not directly the flag. it is upside down

```
!!!236112627it0G1_FTC
```
a very nice little tool from kali and it's all done!!!

```
┌──(kali㉿kali)-[~/CTFs/HackerlabCTF/Basic/youtube]
└─$ echo '!!!236112627it0G1_FTC' | rev
CTF_1G0ti726211632!!!         
```
