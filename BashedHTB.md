# Bashed HTB, IP 10.10.10.68

# Initial Enumeration

**1.nmap -sC -sV 10.10.10.68**

![ba1](https://user-images.githubusercontent.com/15195048/93922279-8eef6800-fcc6-11ea-8fd3-d5bf09ae58f6.png)

**2. ./nmapAutomator.sh 10.10.10.68 All**

![ba2](https://user-images.githubusercontent.com/15195048/93922290-8f87fe80-fcc6-11ea-91cd-d8da4f66f349.png)

**3. Dirb/ Gobuster**
http://10.10.10.68:80/images (Status: 301) [Size: 311]
http://10.10.10.68:80/about.html (Status: 200) [Size: 8190]
http://10.10.10.68:80/index.html (Status: 200) [Size: 7742]
http://10.10.10.68:80/contact.html (Status: 200) [Size: 7802]
http://10.10.10.68:80/uploads (Status: 301) [Size: 312]
http://10.10.10.68:80/php (Status: 301) [Size: 308]
http://10.10.10.68:80/css (Status: 301) [Size: 308]
http://10.10.10.68:80/dev (Status: 301) [Size: 308]
http://10.10.10.68:80/js (Status: 301) [Size: 307]
http://10.10.10.68:80/config.php (Status: 200) [Size: 0]
http://10.10.10.68:80/fonts (Status: 301) [Size: 310]
http://10.10.10.68:80/single.html (Status: 200) [Size: 7476]

--------------------------------------------------------------------------
# Port Enumeration

## 1.Enumerating on port 80

![ba3](https://user-images.githubusercontent.com/15195048/93922293-90209500-fcc6-11ea-8352-943ec9c0ea7e.png)

**Interesting Files**

phpbash.php

![ba4](https://user-images.githubusercontent.com/15195048/93922297-90209500-fcc6-11ea-925d-c57157414321.png)

![ba5](https://user-images.githubusercontent.com/15195048/93922298-90b92b80-fcc6-11ea-98e6-b3e516f176f0.png)

![ba6](https://user-images.githubusercontent.com/15195048/93922301-90b92b80-fcc6-11ea-9114-742c6f126da0.png)


user.txt
--------------------------------------------------------------------------------------------
# Privilege Escalation

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
www-data@bashed:/var/www/html# sudo -l -U www-data
Matching Defaults entries for www-data on bashed:
 env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin
  (scriptmanager : scriptmanager) NOPASSWD: ALL
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


You can run any command as scriptmanager and do everything it can do.

![ba7](https://user-images.githubusercontent.com/15195048/93922302-9151c200-fcc6-11ea-9e85-97024d936199.png)

I could initiate a reverse shell.

**SHELL UPGRADE**
sudo -u scriptmanager /bin/bash

![ba8](https://user-images.githubusercontent.com/15195048/93922304-91ea5880-fcc6-11ea-83a1-107bdb061c9a.png)


**Interactive shell:**

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
python -c 'import pty; pty.spawn("/bin/sh")'
export TERM=xterm-256color
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


Being scriptmanager we will check for suspicious file, for files that we own such as “scripts” file.

![ba9](https://user-images.githubusercontent.com/15195048/93922306-91ea5880-fcc6-11ea-9a86-6dd3e8c11945.png)


**cd scripts**


**ls -al**

![ba10](https://user-images.githubusercontent.com/15195048/93922307-9282ef00-fcc6-11ea-9ed7-3d6669b59396.png)


**Notes:**
We have two files test.py and test.txt
test.py is writable, while test.txt is readable and writable by root only

lets check both files
cat test.py

![ba11](https://user-images.githubusercontent.com/15195048/93922309-931b8580-fcc6-11ea-8ed9-23193383f967.png)

testing 123!

**Notes:**
**1.** Check permissions. I tried to execute the python file but this is not possible because we dont have the permission. 
However, we can write thμπορούμε να γράψουμε σε αυτό και απ'ότι φαίνεται ότι γράψουμε αποτυπώνεται στο test.txt.
We conclude that test.txt is updated throug python script.

**2.** Check Date of each file. 
Here, test.txt file has today's date which means that a cronjob associated with the mentioned file is running. This cronjob updated the script.

**3.** Check the beginning /bin/bash or /usr/bin/python file.

**4.** Normally, we can overwrite the file and append text with echo. Here, we are not able because of the lenght of one liner. Thus we could create a similar file in our VM and downloaded it.

![ba12](https://user-images.githubusercontent.com/15195048/93922313-931b8580-fcc6-11ea-9ddb-21a997146a34.png)


Our file is downloaded with the name of test.py.1, now I removed test.py file and changed the name of test.py.1 file to test.py so machine will run only that file which we want to execute for reverse shell. Code for reverse shell is same as I used at the start of this write-up.
Now turn on your netcat listener and wait a minute to get executed that test.py file to get reverse shell.
 
![ba13](https://user-images.githubusercontent.com/15195048/93922316-94e54900-fcc6-11ea-990d-81deb053dca9.png)

