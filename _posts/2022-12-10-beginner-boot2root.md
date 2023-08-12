---
layout: post
title: "TryHackMe: Beginner boot2root"
categories: TryHackMe
published: true
---

![Banner](/assets/pictures/b2root_1.png)

Hey!! Long time no see! Here I come again to contribute something beneficial for you all! Last week, I attended the very first Malaysia CyberSecurity Camp 2022. So there is an assignment (boot2root) for participants to solve. So here we go!

## Reconnaissance
Let's start the challenge by performing an active port scanning using nmap. 
> nmap -sC -sV $IP -oN nmap.txt --vv

* -sC - This is the switch for scanning common vuln using common script
* -sV - This is the switch for scanning the running services 
* -oN - This is the switch to output the result in TXT format.

```bash
# Nmap 7.93 scan initiated Thu Dec  8 11:21:54 2022 as: nmap -sC -sV -oN nmap.txt --vv 10.10.86.113
Nmap scan report for 10.10.86.113
Host is up, received echo-reply ttl 63 (0.22s latency).
Scanned at 2022-12-08 11:21:55 EST for 32s
Not shown: 848 closed tcp ports (reset), 149 filtered tcp ports (no-response)
PORT   STATE SERVICE REASON         VERSION
21/tcp open  ftp     syn-ack ttl 63 vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.8.9.53
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxr-xr-x    2 ftp      ftp          4096 Dec 01 10:45 pcap
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 9f39a978c7e72abeef2b770b796b8673 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDT2y1PCQ/z0fmv5ZOq9wuboxyDGOSfc/vrcRwq3MHdpIXN6vrBh0i9p2XQ9hzNSy8pqxfUti0IDV/HcdLqU0zJ19cbFIhYkx1yN1OMG83RVqEYVaD/FeosvJamzMnCHZCd4iiJ/ZGALFNbUCiC3q9ukGiRlSZSDvgP+PXODr4bPHSP0eQdqiaJ9qGCr634F1a/3rg48VmxLGiws6wn5bHwA0WdEraXcGdmmiotCd6IwnXBGu7SEYisXuBTR7yQgmwjPu1LrJqwJ05elUHl5EjSf9W7pA4lftWrLvvGRdb4P+zPsji/5gcs0vnJSM6meDcSYMj56RhjYepA8zXEBhLB
|   256 6ac2750d5992ec0afcb0d850dc0cef4c (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBFggt7CsgxeLn3AI5JbreweWRmCy3gNtwxl1HKIqOGV9a234jm0h2PT3t4tjhCbCS6nFhmYv7qt7M/BaVDtNYK0=
|   256 015a808320f6f425de2319fafe37cc35 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIOmi19qi9fZjWbEkZhLUq8NHtQ8xYGWniMs+ZT+jrfmq
80/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Login
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```

As you can see, there are three running services: FTP, SSH, and HTTP. If we look at the FTP scan result, we can login to the server as an anonymous user without entering a password.

```
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxr-xr-x    2 ftp      ftp          4096 Dec 01 10:45 pcap
```

So let's login to the server and download a network packet captured file from it. It turns out that this pcap file contains login credentials. Open a dump.pcap file using a wireshark and follow the TCP stream to line 19.

```
Username:johncena
Password:xianzaiwoyoubcl
```

![wireshark](/assets/pictures/b2root_2.png)

---
## Enumeration

A webpage showing a John Cena meme (LOL) after successfully logging in

![johncena](/assets/pictures/Johncena.png)

Hahahaha, enough memes! Let's find the source of this picture. I opened a new tab after right-clicking on the John Cena image. This feature will take us to the location of this picture, which is in the index of "/media."

![indexofmedia](/assets/pictures/index%20of%20media.png)

As you can see, there are two other pictures. I downloaded blackcat.jpg and performed steganography on this picture. It turns out that this picture has an embedded file. However, it was protected with a passphrase.

![blackcat.jpg](/assets/pictures/blackcat.png)
![blackcat](/assets/pictures/steghide%20info%20blackcat.png)

## Foothold
Meanwhile, I performed directory fuzzing using a gobuster. I found an interesting directory called `dictionary`. This directory contains wordlists (complete_meowlist.txt). It means there might be a valid passphrase inside of it. So, let's crack the passphrase! 

![valid-passphrase](/assets/pictures/stegcracker%20meow.png)

The `BadcatMCC99` can be used to extract an embedded file from blackcat.jpg. This embedded file (secret_meow.txt) contains an SSH credential!  

Succesfully logged in! A user flag is located in home directory.

![user_flag](/assets/pictures/foothold_b2root.png)

---
## Privilege Escalation

As usual, I find any SUID binary and list down any possible binary that can be executed by sudoers but there's nothing found :(
However, there is a weird bash script.sh located in root directory. 

script.sh

```bash
badcat@ubuntu:~$ cat /script.sh 
#!/bin/bash
bash -c 'rm -rf /tmp/* '
```
This script might be an automation tool that deletes everything inside the temporary directory. So, I read /etc/crontab to check whether a root is using cronjobs or not. However, it did not appear to be running any automation tasks. I get confused. Nevertheless, it turns out that any automation job can be hidden using `#^M.` This occurred to me shortly after opening /etc/crontab in Nano's text editor. 

![hidden-cronjob](/assets/pictures/hidden%20cronjob.png)

Plus, this script.sh is writable! (--rw-) It means we can inject any malicious script, and it will be executed by root! So, let's insert a reverse shell inside the file and setup a netcat listener.

![root](/assets/pictures/b2root_rootflag.png)

We got root! 