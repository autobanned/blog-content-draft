Title: VPN / Virtual Private Network
Date: 2014-08-02 13:27
Modified: 2017-04-14 23:40
Tags: openvpn, vpn, pptp, *nix. unix, windows, freebsd, gnu/linux, debian, ubuntu, linux, vps
Category: Misc
Slug: mengenal-vpn-dan-jenisnya
Authors: andy
Summary: Mengenal vpn dan jenisnya

#### Pengertian

VPN atau *private network* adalah teknologi jaringan untuk membuat sambungan aman dan terenkripsi agar dapat bertukar data dan terhubung dengan aman di jaringan terbuka atau internet. Dengan VPN maka setiap system yang tersambung dengannya akan memiliki jaringan *private* dan dapat terhubung selayaknya berada didalam jaringan local dan dapat berkomunikasi secara aman meskipun terhubung melalui internet. 
Contoh implementasi dari vpn seperti yang VPN biasa digunakan untuk pertukaran informasi dan data antara perusahaan pusat dan cabang, untuk memastikan data yang dikirim dan diterima tetap aman meskipun saling berukar informasi dan data di jaringan terbuka.

Selain itu di era modern saat ini teknik vpn juga banyak digunakan untuk menjaga privacy kita saat berinteraksi di internet dimana saat kita terhubung ke vpn server maka identitas asli kita seperti alamat ip dapat kita lindungi, karna yang terdeteksi oleh situs yang kita kunjungi adalah alamat dari vpn server tersebut dan ISP pun hanya mendeteksi alamat IP dari VPN sebagai alamat yang kita kunjungi. 

VPN juga dapat dijadikan alat untuk membantu ketika kita butuh melakukan aktivitas yang meminta kita memiliki public IP agar bisa di hubungi dari jaringan tapi kita tidak memilikinya, dengan VPN maka kita menjadi memiliki Public IP.


#### Jenis VPN
Setelah mengetahui definisi dari VPN sekarang mari kita melihat jenis-jenisnya, VPN sendiri memiliki jenis atau type yang berbeda satu sama lainnya. Secara umum jenis vpn dibedakan menjadi 2, berdasarkan tipe penggunaannya. 

##### 1. Remote Access VPN
Merupakan tipe *VPN* yang paling umum digunakan. VPN dengan jenis ini mengnizinkan user untuk terhubung dengan jaringan *private* melalui internet untuk dapat terhubung dan saling bertukar informasi didalamnya. Remote Access VPN biasa digunakan di perusahaan dan pengguna rumahan/individual. VPN jenis ini relative lebih mudah dan murah dalam pembuatan dan perawataanya.Biasa digunakan oleh perusahaan yang memiliki pegawai mobile untuk dapat terhubung kejaringannya atau perusahaan yang memiliki lokasi cabang yang masih berada di satu wilayah geografis.


##### 2. Site-to-site
*VPN* jenis ini juga biasa disebut sebagai Router-to-Router VPN karna VPN jenis ini biasanya menggunakan satu Router sebagai server dan yang lain sebagai client, dan sambungan komunikasi baru terhubung ketika kedua router telah melakukan validasi autentikasi. Biasa di gunakan perusahaan atau korporat yang memiliki kantor cabang yang berlokasi di luar wilayah geografisnya. Kumpulan dari site-to-site VPN yang saling terhubung dengan server yang sama biasa disebut sebagai **Intranet based VPN.** Sedangkan jaringan yang terhubung ke satu *Site-to-site* VPN untuk dapat terhubung ke jaringan lainnya disebut Extranet **Based VPN**. 



