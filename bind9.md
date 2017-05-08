Title: Konfigurasi BIND9 di GNU/Linux Debian dan Ubuntu based 
Date: 2017-04-18 23:27 
Modified: 2017-04-19 23:40 
Tags: dns, nameserver, gnu/linux, debian, linux, vps, network, ubuntu
Category: GNU/Linux 
Slug: setting-bind9-debian-amp-ubuntu
Authors: andy 
Summary: setting dan konfigurasi bind9 di debian jessie dan ubuntu 16.04

##### Sejarah
Bind9 adalah DNS software DNS yang powerfull dan merupakan yang paling banyak digunakan di internet dan dapat dijalankan pada unix-like system maupun system lainnya, dalam sejarahnya nama bind sendiri merupakan aknronim dari Berkeley Internet Name Domain, dan Bind9 adalah versi ke sembilan dari bind yang rilis sejak tahun 2000, merupakan penerus dari bind8 dan bind4, dan sebagai penerusnya bind10 sedang dalam masa pengembangan sejak tahun 2009 dan dinamai ulang sebagai [bundy](http://bundy-dns.de/) setelah dilepas menjadi proyek komunitas oleh *ISC*  untuk sejarah lengkapnya bisa dibaca di [sini](https://www.isc.org/downloads/bind/history-of-bind/). 

Di tutorial ini saya akan membahas cara konfigurasi bind di debian jessie, strecth dan ubuntu. 
Bind sendiri tersedia di hampir semua repositori *official* distro/distribusi GNU/Linux, untuk menginstallnya di di Debian jessie ataupun search maupun ubuntu kita cukup menggunakan apt *package manager* saja. 
<small><strong>Untuk keseluruhan aktifitas di tutorial ini saya menggunakan akun dengan hak akses ***root*** atau ***superuser/sudo*** </strong></small>

##### Install

```php 
apt install bind9 bind9-utils bind9-docs
```

