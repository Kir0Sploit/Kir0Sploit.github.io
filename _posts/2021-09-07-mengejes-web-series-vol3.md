---
layout: post
title: "Mengejes Web Series: High Vulnerability Level"
categories: Mengejes-Web-Series
permalink: /high-vulnerability-level-mengejes-web-series
published: true
---

![xss](/assets/pictures/XSS.png)
### Cross Site Scripting (XSS)
 Cross-Site Scripting atau lebih dikenali sebagai XSS merupakan client-side code injection attack. Dalam kata lain, pengguna yang mengeksploitasi code sebuah halaman sesawang. XSS secara umumnya membolehkan hacker untuk mencuri cookies session pengguna atau admin dengan menyamar sebagai pengguna biasa. Sebagai contoh, mereka akan memasukkan malicious JavaScript code melalui titik input ke dalam server untuk mendapatkan admin’s cookie session. Setelah itu mereka berupaya untuk login sebagai admin.

---

![XXE](/assets/pictures/xxe-injection.png)
### XML External Entity Injection (XXE)
 XML External Entity Injection atau lebih dikenali sebagai XXE Injection adalah vulnerability yang membolehkan hacker untuk memanipulasi pemprosesan data aplikasi XML. Ia membenarkan hacker untuk melihat kandungan system file yang ada dalam server seperti ‘passwd’. Secara amnya, hacker menggunakan teknik XXE Injection untuk mendapatkan sensitif data dan melakukan server-side request forgery (SSRF)

---

![LFI](/assets/pictures/lfi.png)
### Local File Inclusion (LFI)
 Local File Inclusion atau lebih dikenali sebagai LFI merupakan vulnerability yang membolehkan hacker untuk mendapatkan sistem file dari server. Faktor utama yang menyebabkan sistem file boleh dikeluarkan adalah kerana PHP script yang vulnerable. PHP code tersebut tidak dibersihkan sepenuhnya ketika web application ingin mengemukakan file seperti ‘index.html’.

 Contoh PHP code yang vulnerable:

 ```PHP
 <?php
$file = $_GET[‘file’];
if(isset($file))
{
include(“pages/$file”);
}
else
{
include(“index.php”);
}
?>
```

Script tersebut membenarkan hacker untuk include file yang ada dalam server selain file index.php

#### Vulnerable url parameter:
> http://example.com/vulnphpcode.php?page=index.html

Web application memaparkan file index.html

#### Exploiting vulnerable parameter:
> http://example.com/vulnphpcode.php?page=../../../../etc/passwd

Web application mengemukakan kandungan sistem file passwd. Kebiasaannya, vulnerability ini boleh menyebabkan berlakunya directory traversal, malah hacker mampu leverage attack ke RFI untuk mencapai remote command execution (RCE) ke atas server.