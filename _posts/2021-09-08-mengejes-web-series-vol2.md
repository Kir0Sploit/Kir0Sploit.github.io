---
layout: post
title: "Mengejes Web Series: Medium & Low Vulnerability Level"
categories: Mengejes-Web-Series
permalink: /Mengejes-Web-Series-med-low-vuln-level
published: true
---

![dir-listing](/assets/pictures/dir-listing.png)
### Directory listing
 Directory-listing merupakan fungsi web server yang memaparkan kandungan direktori dalam web page kerana dalam web page tersebut tidak mempunyai index file. Fungsi ini amatlah bahaya kerana ia akan menyebabkan information disclosure seperti kebocoran data sensitif.

---

![open-redirect](/assets/pictures/exploiting_open_redirectionpng.png)
### Open redirect
 Open Redirection merupakan vulnerability yang membolehkan hacker untuk mengawal mangsa yang sedang melayari laman sesawang redirect ke lawan sesawang lain. Vulnerability ini sudah biasa berlaku pada masa kini, lebih-lebih lagi kes penipuan dalam talian semakin meningkat. Dalam konteks ini, open redirection selalu dilakukan hacker untuk mengawal mangsa ke website yang mencurigakan untuk mendapatkan data sensitif seperti maklumat bank, alamat rumah, kata laluan social media dan sebagainya.

Contoh:

Ali menerima email bahawa dia perlu menukar kata laluan akaun online banking untuk tujuan pengesahan. Lalu Ali click link yang disertakan dalam email tersebut seperti;

> http://example.com/vuln.php?redirecturl=http://susweb.net

Ali tidak mengesyaki sebarang penipuan kerana dia akan melayari ke website sebenar dahulu (bankwibu.com) sebelum redirect ke website suspicious (susweb.net). Setelah itu dia log masuk dengan nama dan kata laluan akaun banknya. Secara tidak langsung, hacker telah berjaya mencuri maklumat akaun bank milik Ali.

---

![click-jacking](/assets/pictures/Clickjacking.png)
### Clickjacking
 Clickjacking merupakan vulnerability yang menipu pengguna untuk click satu element website yang halimunan atau ditutup dengan element yang berlainan. Jika pengguna menekan element tersebut, dia berisiko untuk diserang malware atau mendedahkan data sensitif seperti credentials. Secara umumnya, serangan clickjacking memaparkan element webpage seperti ‘Download button’ pada page website. Walaupun pengguna menganggap ‘Download button’ itu hanyalah perkara biasa namun hakikatnya dia akan download perisian hasad sekiranya dia click button tersebut. Button atau web page element tersebut juga boleh redirect pengguna ke website yang suspicious.
