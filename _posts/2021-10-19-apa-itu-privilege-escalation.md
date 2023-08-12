---
title: "Apa itu Privilege Escalation?"
layout: post
published: true
---

![pe](/assets/pictures/Pe.png)
## PENGERTIAN PRIVILEGE ESCALATION

Privilege escalation merupakan tindakan menyerang kelemahan (vulnerability/system flaws) sesuatu machine untuk menaikkan taraf akses ke tahap yang lebih tinggi dan penyerang boleh mengakses rangkaian, aplikasi dan sistem sesebuah machine yang bersifat kritikal. Tindakan ini selalu digunakan oleh hackers atau pentester untuk hijack sesebuah sistem operasi atau server untuk kepentingan diri sendiri. Jika penyerang tersebut dapat melakukan tindakan ini, mereka dapat mengakses sesebuah aplikasi yang istimewa dan dilindungi ketat oleh pentadbir sistem iaitu root. Pencurian data sensitif juga menjadi tumpuan utama para penyerang apabila melakukan privilege escalation kerana sesetengah dokumen yang sensitif hanya dapat diakses oleh para pentadbir sistem seperti database customers, maklumat kredit kad pelanggan dan sebagainya.

Apa lagi benefits hackers akan dapat apabila dia berjaya melakukan privilege escalation ? Hackers boleh mendapatkan akses sebagai pentadbir sistem iaitu root dan dalam waktu yang sama dia berpeluang untuk mengakses kernel sesebuah machine.

![privilege-escalation-diagram](/assets/pictures/220px-Privilege_Escalation_Diagram.svg.png)

Rajah di atas menunjukkan senario bagaimana privilege escalation dilakukan. Senario ini bermula dengan serangan atau eksploitasi terhadap kelemahan sistem daripada tahap yang paling rendah iaitu User. Selepas penyerang menjumpai kelemahan dalam sesebuah machine dan berjaya log masuk sebagai User, dia akan mencari cara untuk log masuk sebagai Admin. Penyerang akan mengorak langkah mencari kelemahan lain yang terdapat dalam machine di mana ia membolehkan penyerang untuk log masuk sebagai Admin. Setelah penyerang berjaya log masuk sebagai Admin, dia akan mencari cara lain untuk log masuk sebagai Root iaitu pentadbir atau pemilik kepada sesebuah machine. Root merupakan User yang paling tinggi dalam hierarki Linux users. Pada tahap ini, penyerang dapat mengakses kernel sesebuah machine dan berpeluang untuk shut down machine, performing crontab jobs, dan lain-lain lagi kebolehan.

BAHAYA KAN? IA MEMANG SUNGGUH BAHAYA. ROOT JUGA BOLEH MEMBUANG SESEORANG PENGGUNA DALAM SISTEM DAN MENGAMBIL ALIH SEGALA KERJA YANG DILAKUKAN OLEH PENGGUNA BIASA.

## JENIS PRIVILEGE ESCALATION
Privilege escalation mempunyai dua jenis senario. Setiap senario ini mempunyai tujuan yang sama untuk mendapatkan akses sebagai pengguna luar biasa atau pentadbir sistem. Tetapi, kedua-dua senario ini berbeza dari segi cara untuk menaikkan taraf sebagai root.

![type-of-privilege-escalation](/assets/pictures/type-of-pe.jpeg)


| Vertical Privilege Escalation | Horizontal Privilege Escalation |
|--|--|
| Privilege escalation menegak berlaku apabila penyerang ingin menaikkan hak istimewa secara terus daripada user biasa ke pentadbir sistem iaitu root. | Privilege escalation mendatar berlaku apabila penyerang ingin menaikkan hak istimewa sebagai root dengan melalui pelbagai cara. |
| Tindakan ini lebih senang dilakukan kerana ia hanya berlaku dalam satu proses sahaja. Penyerang akan mencari kelemahan yang ada dalam pengguna biasa yang membolehkan dia terus mendapatkan hak istimewa sebagai root. Vertical privilege escalation ini selalu berlaku kerana pengguna dapat menjalakan sesebuah aplikasi sebagai root. Dalam kata lain, pengguna biasa mendapat hak istimewa untuk menjalakan sesebuah aplikasi yang ada dalam sistem operasi. | Tindakan ini agak sukar untuk dilakukan kerana ia cenderung memerlukan pengetahuan yang lebih mendalam dalam sesebuah sistem operasi untuk mencari kelemahan yang ada. Tindakan ini selalunya dilakukan penyerang dengan mencari data sensitif yang ada dalam seseorang pengguna seperti password.BAK. File ini merupakan password yang di simpan atau backup dan ia sangatlah sensitif. Dalam sistem operasi LINUX, penyerang boleh melakukan tindakan ini dengan membaca /etc/passwd dan /etc/shadow file untuk mendapatkan maklumat kata laluan pengguna. Prosesnya agak panjang kerana penyerang perlu mencari pelbagai kelemahan yang ada dalam sistem operasi. |

## Bonus: Common Linux Privilege Escalation
Antara cara penyerang melakukan privilege escalation adalah dengan mendapatkan maklumat pengguna dalam file /etc/passwd.

![passwd](/assets/pictures/passwd.webp)

Dalam gambar di atas, terdapat pelbagai pengguna yang ada dalam sistem operasi dan kesemuanya merupakan sistem user. Penyerang akan membaca file tersebut untuk melihat sama ada seseorang pengguna mempunyai encrypted password atau tidak. Untuk lebih memahami perkara ini, anda perlulah mempunyai asas sistem operasi Linux. Sekiranya sistem operasi linux mempunyai kelemahan seperti pengguna biasa boleh membaca /etc/shadow file. Penyerang dapat melihat password pengguna dan mencuba untuk log masuk sebagai pengguna tersebut.

![shadowfile](/assets/pictures/etc-shadow-file.png)

Dalam gambar di atas, kita dapat membaca joe's password di dalam /etc/shadow kerana kita mengakses sebagai root. Sekiranya file tersebut mempunyai permission 644 iaitu rw--r--r--, pengguna biasa mempunyai akses untuk membaca file ini dan mendapatkan password seseorang pengguna. Secara umumnya, password pengguna akan di encrypted dalam bentuk sha512,bcrypt dalam shadow file dan penyerang perlu crack algorithm tersebut sebelum dapat log masuk ke dalam pengguna yang berlainan.

#### YANG MANA SATU PASSWORD TU?
Password pengguna terdapat di dalam column yang kedua iaitu : :


Walau bagaimanapun, shadow file jarang sekali mempunyai permission seperti itu. Jadi, penyerang akan mencari cara lain untuk cuba leverage attack. Antara cara yang popular adalah abusing setuid, menjalankan aplikasi yang mempunyai hak istimewa dengan sudo dan memasukkan code malicious atau jahat dalam crontab jobs.

Untuk melakukan privilege escalation, anda perlu mempunyai pengetahuan yang tinggi dalam sesebuah sistem operasi untuk memahami bagaimana sesebuah machine itu berfungsi. Terdapat banyak lagi cara yang boleh dilakukan untuk menaikkan taraf menyerang sebagai root. Jadi, anda tidak boleh terikat dengan cara yang sedia ada.


