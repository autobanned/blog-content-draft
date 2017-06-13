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

Setelah terinstall kita dapat mengkonfigurasi bind sesuai kebutuhan kita, Bind9 merupakan software DNS server yang flexible dan powerfull dapat digunakan dari DNS Forwarder hingga membuat authoritative nameserver. Tapi dibalik kehandalannya tersebut bind9 tergolong software yang sulit untuk dikonfigurasi apalagi bagi yang baru mempelajari tentang DNS. 

Untuk mengkonfigurasi bind kita tinggal merubah file konfigurasinya sesuai kebutuhan kita, pada debian dan hampir kebanyakan distro linux file tersebut berada di '/etc/bind`.

###### Menjalankan bind9
