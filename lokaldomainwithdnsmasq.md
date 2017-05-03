Title: Setting lokal domain menggunakan dnsmasq
Date: 2017-04-03 21:00
Modified: 2017-04-09 23:40
Tags: dns, dnsmasq, gnu/linux, debian, linux
Category: GNU/Linux
Slug: setting-lokal-domain-menggunakan-dnsmasq
Authors: andy.zs
Summary: Cara membuat domain *private* untuk jaringan lokal/LAN dengan menggunakan dnsmasq, untuk mempermudah penamaan tiap *workstation*.



Saat kita mempunyai beberapa komputer di jaringan lokal, agar kita dapat saling menghubungi maka 
kita harus mengingat alamat IP dari tiap komputer yang ingin kita panggil. Alamat IP cukup panjang tidaklah mudah untuk mengingatnya 
apalagi jika jumlah alamat yang harus kita hafal ada cukup banyak. Sebenarnya kita bisa dengan membuat alias IP untuk setiap *workstation/computer* di file `/etc/hosts` tapi jika suatu hari ada workstation / user baru yang bertambah atau berkurang maka kita akan repot karna harus merubahnya secara manual lagi, untuk mempermudah itu maka kita bisa membuat `local domain` pada dns server. Karna yang kita butuhkan hanya `DNS forwader` dan `DHCP server`, maka tidak lah membutuhkan banyak fitur wah dari dns server seperti `Authoritive name`, `DNS Recursive` dll. **Dnsmasq** adalah *tools* yang ringan dan cepat serta mudah dikonfigurasi. Dnsmasq tersedia dihampir seluruh nix`*` system. 



