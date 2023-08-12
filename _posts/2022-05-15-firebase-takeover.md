---
title: "Firebase database takeover"
layout: post
published: true
---

![fdbtakeover](/assets/pictures/Firebase_pwned.png)

Jom kita tengok Firebase!
 
![fbdmeme](/assets/pictures/fdbmeme1.png)
 
“Firebase ? Pangkalan api ke?“ Tanya Pak Samad sambil menggaru-garu kepala di hadapan Abang Kiro.
“Mende apa ni mat ? Tiktok viral baru ke ?“ Tambah Pak Samad. Lalu Abang Kiro menjawab, “ Bukann laa Pakcik, ni pangkalan data dalam aplikasi. “

“Oh apo bondo ni yop, ce cite.“ Pak Samad dengan semangat bertanya Abang Kiro sambil menunjukkan ekspresi muka gembira.

“Haa Pak Samad berminat nk tahu? Ajomm kita tengokk.” Balas Abang Kiro dengan hati yang berkobar-kobar sambil membuka komputer ribanya di atas meja restoran.​

---
### Pengertian Firebase
Firebase merupakan platform pembangunan aplikasi Backend-as-a-Service (BaaS) yang menyediakan perkhidmatan backend seperti realtime database, cloud storage, authentication, crash reporting, machine learning, remote configuration, dan untuk hosting fail statik sesebuah website atau aplikasi. Platfom ini dibangunkan syarikat Firebase Inc iaitu James Tamplin and Andrew Lee pada tahun 2011. Walau bagaimanapun, Google telah mengambil alih platform ini pada tahun 2012 sehingga kini. Firebase menawarkan perkhidmatan;

1. Analisis
    * pelaporan percuma tanpa had pada sebanyak 500 events berasingan. Ia berfungsi untuk melaporkan analisis data penggunaan aplikasi berasaskan IOS atau Android.
2. Pengesahan/Authentication - 
    * memudahkan developer membina sistem pengesahan yang selamat dan meningkatkan ciri log masuk pengguna. Ciri ini menawarkan complete identity solution, supporting email, kata laluan, phone auth, dan juga Google, Facebook, GitHub, Twitter login.​
3. Cloud messaging
    * alat pemesejan ‘cross platform’ yang membolehkan syarikat menerima dan menghantar mesej pada iOS, Android dan web tanpa sebarang kos.​
4. Realtime database
    * pangkalan data NoSQL dihoskan secara cloud yang membolehkan data disimpan dan synchronise antara pengguna dalam masa nyata.​

Selain perkhidmatan di atas, terdapat banyak lagi perkhidmatan yang ditawarkan Firebase untuk semua developer. Namun begitu, perkhidmatan-perkhidmatan yang ditawarkan boleh dieksploitasi oleh penggodam yang tidak beretika. Dalam konteks keselamatan, semua produk digital termasuk Firebase database mempunyai kelemahan yang boleh dieksploitasi sehingga mewujudkan risiko godaman data.

Pada bulan Mac 2020, pasukan penyelidik keselamatan Comparitech yang diketuai oleh Bob Diachenko menganalisis sampel 515,735 aplikasi Android daripada Google Play Store mendapati 155,066 buah aplikasi telah menggunakan Firebase. Menurut laporan Forbes (2020), Bob Diachenko mengesahkan bahawa kira-kira 11,730 aplikasi yang menggunakan Firebase database mendedahkan pangkalan data Firebase itu secara terbuka di dalam configuration file. Tambah Bob, 9,014 aplikasi termasuk yang mempunyai ‘write permission’ di dalam pangkalan data membolehkan penggodam mengubah suai data, menambah, memadam atau memuat turunnya. Comparitech (2020) mendedahkan bahawa 4,282 aplikasi membocorkan maklumat sensitif seperti alamat e-mel, bilangan mesej, nama pengguna, kata laluan, dan nombor telefon.

“Ohh ini ke Firebase mat… Jadi cemano ek Hacker hack database Firebase ni yop?” Tanya Pak Samad yang semakin teruja mendengar penjelasan Abang Kiro.

“Ehh! Pak Samad nk tengok demo ke? Ajommm kita hack” Balas Kiro kepada Pak Samad, lantas beliau membuka aplikasi VMware.​

---
![poc](/assets/pictures/poc.png)

Berdasarkan tajuk, anda pasti tertanya-tanya bagaimana sesebuah pangkalan data aplikasi Android dapat dieksploitasi khususnya diambil oleh penggodam. Dalam konteks ini, saya telah menjumpai sebuah aplikasi Android yang mendedahkan maklumat sensitif pangkalan data Firebase tempoh hari. Nama aplikasi tersebut tidak akan didedahkan berikutan polisi yang perlu dituruti.

### Steps to Reproduce

1. Decompile application menggunakan apktool

![decompileprogram](/assets/pictures/decompile.png)

2. Masuk dalam extracted source code folder, dan cari firebase db url

![firebase](/assets/pictures/firebase_db.png)

3. Buka link tersebut dan intercept request untuk perhatikan response dalam Burp Suite. Terdapat mesej, profile picture dan username dalam bentuk JSON terdedah kepada umum.

![HTTP-response](/assets/pictures/response.png)

4. Cuba check HTTP method sama ada server allow PUT method atau tidak. Didapati server membenarkan PUT method yang membolehkan penggodam untuk memasukkan data ke dalam database.

![PUT-HTTP-method](/assets/pictures/PUT.png)

5. Siapkan python script untuk write data ke dalam db menggunakan requests module.

![python-simple-script](/assets/pictures/script.png)

6. Run script tersebut dan lihat hasilnya dalam browser.

![HTTP-request](/assets/pictures/request.png)

Berdasarkan gambar di atas, exploit berjaya dilakukan dan menyebabkan data dimasukkan ke dalam pangkalan data Firebase aplikasi ini. Antara faktor terbesar berlakunya perkara ini adalah developer membuka akses pangkalan data kepada umum semasa membangunkan aplikasi. Impak yang mungkin berlaku adalah kecurian data peribadi pengguna. Penggodam boleh menggunakan data tersebut untuk tujuan jahat dan boleh memburukkan reputasi syarikat pembangun aplikasi ini. Hatta, developer perlu menukar akses database hanya untuk pekerja syarikat untuk mengelakkan perkara yang sama berulang.

![pak-samad](/assets/pictures/pak%20samad%20insaf.png)