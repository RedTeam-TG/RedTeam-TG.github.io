
## Exfiltration Chall

The first 300pts challenge...Thanks to `manas3` for bringing this prize to our team.

```
Exfiltration

MEDIUM FORENSIC

A conversation between two terrorist groups has been intercepted. It is possible that very sensitive data was transmitted during the communication.

Flag : CTF_*
```
we were given a pcap file. Let's open it with Wireshark to see the content.

A ton of DNS requests.

A while ago I was watching a replay of a DEF CON presentation (the most famous hacker convention in the world) where the presenter was talking about this technique that allows to exfiltrate data through DNS requests. You have to know that in a network, the least monitored flow is the DNS flow, what an ingenious idea to steal a company's data under the nose of the engineers and their firewall army.

Let's start by looking at the statistics of all this stream. The pcap file is divided into 2 types of dns requests (A and CNAME). Take a look at this site [iana.org](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml)
 or on the internet to see more about the types of dns requests and their value.

```
┌──(kali㉿kali)-[~/CTFs/HackerlabCTF/Stages/Exfiltration]
└─$ tshark -r capture.pcap -qz dns,tree                                                                                                 

======================================================================================================================================================
DNS:
Topic / Item                           Count         Average       Min Val       Max Val       Rate (ms)     Percent       Burst Rate    Burst Start  
------------------------------------------------------------------------------------------------------------------------
--snip--

 Query Type                            1550                                                    0.0141        100.00%       0.0400        12.539       
  CNAME (Canonical NAME for an alias)  1172                                                    0.0106        75.61%        0.0200        12.284       
  A (Host Address)                     378                                                     0.0034        24.39%        0.0200        0.000        
 Class                                 1550                                                    0.0141        100.00%       0.0400        12.539    

 --snip--

```
Let's get the data we are interested in. Tshark the command line version of wireshark does a good job. The first time I didn't notice, but as you can see there are duplicates. We will filter the type A and CNAME queries in different files

```
┌──(kali㉿kali)-[~/CTFs/HackerlabCTF/Basic/Exfiltration]
└─$ tshark -r capture.pcap -Y "dns.qry.type==1" -T fields -e dns.qry.name
89504e470d0a1a0a0000000d49484452000002f5000001cb0802000000c7b8f.hackerlab.africa
89504e470d0a1a0a0000000d49484452000002f5000001cb0802000000c7b8f.hackerlab.africa
09a000000017352474200aece1ce90000000467414d410000b18f0bfc610500.hackerlab.africa
09a000000017352474200aece1ce90000000467414d410000b18f0bfc610500.hackerlab.africa
0000097048597300000ec300000ec301c76fa86400000013744558744175746.hackerlab.africa
0000097048597300000ec300000ec301c76fa86400000013744558744175746.hackerlab.africa
86f7200636861726c696570792d504307794fa4000016ad49444154785eeddd.hackerlab.africa
86f7200636861726c696570792d504307794fa4000016ad49444154785eeddd.hackerlab.africa
db61db3a1605d0a9cb05a51e5793665c4c86d42337b2240220018ada5eeb6b2.hackerlab.africa
db61db3a1605d0a9cb05a51e5793665c4c86d42337b2240220018ada5eeb6b2.hackerlab.africa
6d702403c0eb61c47fedf1f00802cf20d009046be0100d2c83700401af90600.hackerlab.africa
6d702403c0eb61c47fedf1f00802cf20d009046be0100d2c83700401af90600.hackerlab.africa
4823df000069e41b00208d7c0300a4916f008034f20d009046be0100d2c8370.hackerlab.africa
4823df000069e41b00208d7c0300a4916f008034f20d009046be0100d2c8370.hackerlab.africa
0401af906004823df000069e41b00208d7c0300a4916f008034f20d009046be.hackerlab.africa
--snip--
```
#### A records

the A requests have been used to send an image. On the image file it says `ROCKYOU ft. l33t`

```
──(kali㉿kali)-[~/CTFs/HackerlabCTF/Stages/Exfiltration]
└─$ tshark -r capture.pcap -Y "dns.qry.type==1" -T fields -e dns.qry.name | cut -d "." -f1 | uniq|xxd -r -p > file1                                      
                                                                                                                                                              
┌──(kali㉿kali)-[~/CTFs/HackerlabCTF/Stages/Exfiltration]
└─$ file file1                  
file1: PNG image data, 757 x 459, 8-bit/color RGB, non-interlaced

```
#### CNAME records

The CNAME requests have been used to send a Zip file. 

```
──(kali㉿kali)-[~/CTFs/HackerlabCTF/Stages/Exfiltration]
└─$ tshark -r capture.pcap -Y "dns.qry.type==5" -T fields -e dns.qry.name | cut -d "." -f1 | uniq|xxd -r -p > file2
                                                                                                                                                              
┌──(kali㉿kali)-[~/CTFs/HackerlabCTF/Stages/Exfiltration]
└─$ file file2
file2: Zip archive data, at least v2.0 to extract

```
On this one, the author of the challenge made us a big joke. We have to go through several layers of archive before we get to the file we are interested in. Can you imagine all those letters of the alphabet are actually archival extensions. Unkind isn't it?

```
┌──(kali㉿kali)-[~/CTFs/HackerlabCTF/Stages/Exfiltration]
└─$ unzip file2   
Archive:  file2
  inflating: flag.a.b.c.d.e.f.g.h.i.j.k.l.m.n.o.p.q.r.s.t.u.v.w.a.b.c.d.e.f.g.h.i.j.k.l.m.n.o.p.q.r.s.t.u.v.w.w.v.u.t.s.r.q.p.o.n.m.l.k.j.i.h.g.f.e.d.c.b.a.a.b.c.d.e.f.g.h.i.j.k.l.m.n.o.p.q.r.s.t.u.v.w 
```
As usual, I wrote my own little script to get the job done. I wasn't going to do all that work again. I admit I could have done better. But anyway, it does the job.

```

┌──(kali㉿kali)-[~/…/HackerlabCTF/Stages/Exfiltration]
└─$ cat script.sh                     

unzip_all(){
        FILE=$(ls | head -n1)
        if file $FILE | grep "Zip archive data"; then
                unzip $FILE
            unzip_all

        elif file $FILE | grep "XZ compressed data"; then
                ZIPFILE=$(mv "$FILE" "${FILE%.*}.xz")
                xz -d *.xz
                unzip_all

        elif file $FILE | grep "bzip2 compressed data"; then
                bunzip2 $FILE
                unzip_all

        elif file $FILE | grep "gzip compressed data"; then
                ZIPFILE=$(mv "$FILE" "${FILE%.*}.gz")
                gunzip  *.gz
                unzip_all
        fi
}
unzip_all

```
at the end of my script I am asked to enter a password. The image was about rockyou ft l33t. I'll have to do some bruteforce.

#### zip2john.py

I first rename the archive I got into something easier to handle. Then with `zip2john` I can extract the hash and crack it with the all powerful `john`

```
┌──(kali㉿kali)-[~/…/HackerlabCTF/Stages/Exfiltration]
└─$ /usr/sbin/zip2john flag.zip > hash
```
The clue `l33t` which could have been written `leet` tells us that we will have to make transformations on the Rockyou dictionary to find the password. I tried to find the right rule file but without success. So let's make it simple.

```
┌──(kali㉿kali)-[~/…/HackerlabCTF/Stages/Exfiltration]
└─$ john hash                                            
Using default input encoding: UTF-8
Loaded 1 password hash (PKZIP [32/64])
Will run 2 OpenMP threads
Proceeding with single, rules:Single
Press 'q' or Ctrl-C to abort, almost any other key for status
Warning: Only 7 candidates buffered for the current salt, minimum 8 needed for performance.
Warning: Only 4 candidates buffered for the current salt, minimum 8 needed for performance.
Almost done: Processing the remaining buffered candidate passwords, if any.
Proceeding with wordlist:/usr/share/john/password.lst, rules:Wordlist
Proceeding with incremental:ASCII
3c0w45           (flag.zip/flag.txt)
1g 0:00:08:36 DONE 3/3 (2022-09-04 09:41) 0.001936g/s 5373Kp/s 5373Kc/s 5373KC/s 3c0gbi..3c0igd
Use the "--show" option to display all of the cracked passwords reliably
Session completed

```
When we unzip the last archive we get a file `flag.txt` which contains a QR code that we will have to read and get the flag.

```
┌──(kali㉿kali)-[~/…/HackerlabCTF/Stages/Exfiltration]
└─$ cat flag.txt                      
                                                                                  
                                                                                  
                                                                                  
                                                                                  
        ##############  ##      ##  ######  ##  ####  ##    ##############        
        ##          ##  ##  ######            ####          ##          ##        
        ##  ######  ##  ##  ##  ##  ####          ########  ##  ######  ##        
        ##  ######  ##    ########  ##  ####    ######  ##  ##  ######  ##        
        ##  ######  ##    ##  ######        ##  ####        ##  ######  ##        
        ##          ##  ##      ##    ######  ####    ####  ##          ##        
        ##############  ##  ##  ##  ##  ##  ##  ##  ##  ##  ##############        
                        ####  ######  ##      ##  ######                          
            ######  ##  ##  ####  ####  ####  ##  ######  ######    ######        
        ##      ##            ##  ########      ######    ##        ##            
                ########    ####  ##  ##  ##########    ##########    ##          
            ##        ####  ####  ##  ####    ##  ############      ##            
          ####  ##  ####      ####  ########      ##    ####      ##              
        ##  ########  ####  ##  ######      ##      ##      ####  ##              
        ##      ##  ##  ##  ####      ######      ####  ##          ####          
        ##  ##        ##########    ##########  ####          ##    ##            
                ##  ##  ##  ##    ####      ####    ##              ####          
        ####  ####    ##########  ##    ##  ######    ##########    ##            
        ##  ##  ########  ##      ##        ####          ####  ####  ####        
          ##  ##      ##########        ##  ##  ########    ##  ######            
              ##    ##  ##  ########    ##  ##    ####    ####    ########        
        ##        ##  ####  ##  ##  ####  ##    ####    ##        ##  ##          
        ##  ##  ######      ##  ##    ##  ####      ##    ####    ######          
        ##    ######      ####    ##    ##    ##  ####  ######    ######          
        ##  ##  ##  ##      ########  ##      ##        ##########    ##          
                        ############  ######  ####  ######      ##########        
        ##############    ########    ####    ####  ##  ##  ##  ######            
        ##          ##                      ##        ####      ##########        
        ##  ######  ##  ####  ####  ########  ##  ####  ##############            
        ##  ######  ##  ##  ####  ##    ##    ##  ##  ######  ##  ##  ##          
        ##  ######  ##  ######      ########    ##      ####                      
        ##          ##    ######        ##    ##  ##  ##    ####  ##              
        ##############    ##    ####  ########      ##  ##          ####          
                                                                                  

```
And voila!

`CTF_W3lc0Me_h4CKER5_338333371819`

Done!!!