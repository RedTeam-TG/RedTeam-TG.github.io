---
layout: default
title : Redteam-TG -JSFuck Writeup
---

## JSFuck challenge

```
JSFuck

EASY WEB JS
Check the hackerlab website https://hackerlab.africa/

Flag : CTF_*

```
Another site to visit

At first sight there is nothing like a flag on the site, maybe in the source code?
Let's take a closer look.

In the source code also nothing, the attached files then? JSFuck sounds more javascript. Let's look at the javascript files of the site. 
A file with a rather unreadable content draws the attention, probably an encoding.

```
[][(![]+[])[+[]]+(![]+[])[!+[]+!+[]]+(![]+[])[+!+[]]+(!![]+[])[+[]]][([][(![]+[])[+[]]+(![]+[])[!+[]+!+[]]+(![]+[])[+!+[]]+(!![]+[])[+[]]]+[])[!+[]+!+[]+!+[]]+(!![]+[][(![]+[])[+[]]+(![]+[])[!+[]+!+[]]+(![]+[])[+!+[]]+(!![]+[])[+[]]])[+!+[]+[+[]]]+([][[]]+[])[+!+[]]+(![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[+!+[]]+([][[]]+[])[+[]]+([][(![]+[])[+[]]+(![]+[])[!+[]+!+[]]+(![]+[])[+!+[]]+(!![]+[])[+[]]]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[][(![]+[])[+[]]+(![]+[])[!+[]+!+[]]+(![]+[])[+!+[]]+(!![]+[])[+[]]])[+!+[]+[+[]]]+(!![]+[])[+!+[]]]([][(![]+[])[+[]]+(![]+[])[!+[]+!+[]]+(![]+[])[+!+[]]+(!![]+[])[+[]]][([][(![]+[])[+[]]+(![]+[])[!+[]+!+[]]+(![]+[])[+!+[]]+(!![]+[])[+[]]]+[])[!+[]+!+[]+!+[]]+(!![]+[][(![]+[])[+[]]+(![]+[])[!+[]+!+[]]+(![]+[])[+!+[]]+(!![]+[])[+[]]])[+!+[]+[+[]]]+([][[]]+[])[+!+[]]+(![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[+!+[]]+([][[]]+[])[+[]]+([][(![]+[])[+[]]+(![]+[])[!+[]+!+[]]+(![]+[])[+!+[]]+(!![]+[])[+[]]]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[][(![]+[])[+[]]+(![]+[])[!+[]+!+[]]+(![]+[])[+!+[]]+(!![]+[])[+[]]])[+!+[]+[+[]]]+(!![]+[])[+!+[]]]((!![]+[])[+!+[]]+(!![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+[]]+([][[]]+[])[+[]]+(!![]+[])[+!+[]]+([][[]]+[])[+!+[]]+([]+[])[(![]+[])[+[]]+(!![]+[][(![]+[])[+[]]+(![]+[])[!+[]+!+[]]+(![]+[])[+!+[]]+(!![]+[])[+[]]])[+!+[]+[+[]]]+([][[]]+[])[+!+[]]

--snip--
```

since I don't even know what encoding it is I got 2 options 
*option 1 [Cyberchef](https://gchq.github.io/CyberChef/) and option 2 [decode](https://www.dcode.fr/identification-chiffrement)*. This time I choose option 2, and since the content is quite large I copy a part that I try to identify..Bingo!!! decode says it's JSFuck.

Here we are. This is a javascript code. How to run it? 
My approach, although a bit long, i create a `.js file`, log the result of the code in a console with `console.log()` and also create an html code in which I call my javascript file and the browser does the rest.

```
cipher = [67, 85, 68, 92, 55, 49, 51, 94, 90, 56, 109, 99, 59, 50, 63, 61, 35, 37] var f = "" function xor_xor(x,y){ return x ^ y; } for (var i=0; i < cipher.length ; i++){ f+= xor_xor(cipher[i] ^ i); }
```
When we run the code we get a sequence of numbers,

`67847095515253898249103104556349505152`

A quick look at [decode](https://www.dcode.fr/identification-chiffrement) and it tells us that there is a chance that it has something to do with ascii. it proposes to decode it for us.

And bingo!!!!

`CTF_345YR1gh7?1234`

Done!!!
