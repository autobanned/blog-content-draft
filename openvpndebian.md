Title: Install dan setting Openvpn di Debian Jessie 
Date: 2017-04-12 23:27
Modified: 2017-04-16 23:40
Tags: openvpn, vpn, gnu/linux, debian, linux, vps, network
Category: GNU/Linux
Slug: install-openvpn-di-debian-linux-server
Authors: andy.zs
Summary: Cara menginstall openvpn di debian linux

*VPN* atau `virtual private network` protokol jaringan untuk membuat tunnel jaringan diantara komputer yang bukan berada di satu jaringan lokal untuk dapat berkomunikasi secara aman. 

Pada artikel kali ini saya akan menulis *how-to* / tutorial menginstall dan menyetting OpenVPN server di Debian Jessie. 

##### Install 
Seperti biasa sebelum menginstall packet baru di server sebaiknya kita mengupdate system kita terlebih dahulu.

```php
sudo apt get update && sudo apt get upgrade
```
lalu install paket-paket yang dibutuhkan oleh openvpn
```php
sudo apt install openvpn easy-rsa
```
Dengan demikian `OpenVPN` sudah terinstall, selanjutnya kita akan mengkonfigurasinya.

##### Setting OpenVPN  serta Buat Sertifikat dan key
Direktori tempat `OpenVPN` menyimpan file konfigurasinya secara *default* ada `/etc/openvpn/` kita akan meletakan semua file yang dibutuhkan untuk mengkonfigurasi server di direktori tersebut.
`OpenVPN` server menyertakan contoh file konfigurasinya untuk mempermudah pengguna melakukan konfigurasi, meskipun kita bisa membuat sendiri file konfigurasi ini.


Untuk mengcopy file *sample* ini dan meletakannya di direktori `/etc/openvpn` kita bisa melakukan dengan perintah berikut:
```php
sudo gunzip -c /usr/share/doc/openvpn/examples/sample-config-files/server.conf.gz > /etc/openvpn/server.conf
```

Selanjutnya kita akan menyetting dan menyesuaikan file konfigurasi `server.conf` ini.

Beberapa perubahan utama yang akan kita buat pada file konfig adalah:

* Meningkatkan level enkripsi openvpn server 
* Meneruskan *web traffic* ke alamat tujuan
* Mencegah bocornya DNS *request* keluar dari koneksi VPN 
* Menyetting *permission*

tapi kita menyesuaikan settingan file konfig di `server.conf` kita akan terlebih dahulu membuat sertifikat dan key, yang akan digunakan sebagai autentikasi untuk `OpenVPN` server kita.
Pertama kita akan meng*copy* `Easy-rsa` *generation script*
```php
cp -r /usr/share/easy-rsa/ /etc/openvpn
```
Selanjutnya buat direktori tempat kita menyimpan key dan sertifikat `OpenVPN` kita.
```php
mkdir /etc/openvpn/easy-rsa/keys
```
Setelah itu kita akan menyetting parameter untuk sertifikat kita sebelum sertifikat itu di buat.
```php
nano /etc/openvpn/easy-rsa/vars
```
Pada file ini variable berikut adalah yg perlu diganti dan dibuat sesuai informasi kita inginkan:
```php
export KEY_COUNTRY="US" 
export KEY_PROVINCE="TX" 
export KEY_CITY="Dallas" 
export KEY_ORG="My Company Name" 
export KEY_EMAIL="sammy@example.com" 
export KEY_OU="MYOrganizationalUnit" 
```
sedang variable lain, tidak perlu diganti kecuali memang paham apa yang akan dilakukan.

Selanjutnya kita akan pindah ke direktori `/etc/openvpn` untuk meng*generate* *Diffie-Helman* parameter menggunakan *tool* bawaan dari OPENSSL bernama `dhparam` untuk *certificate* ca 
```css
cd /etc/openvpn
openssl dhparam -out /etc/openvpn/easy-rsa/keys/dh4096.pem 4096
```
opsi -out diatas mendefinisikan lokasi output hasil *generate* dari dhparam.

Dengan demikian *certificate* ca kita telah siap, selanjutnya kita akan meng*generate* *certificate* server  dan server *key*.

Untuk menggenerate server *key* dan server *certificate* kita akan pindah satu direktori ke direktory *easy-rsa* dan meng*generate* pki (Public Key Infrastructure) dari file `vars` yang telah kita setting variablenya sebelumnya
```php
cd easy-rsa
. ./vars
```
<font size="1">perhatikan tanda spasi (white space) diantara titik, saat meng*generate* pki diatas. </font>

Saat menjalankan perintah diatas kita akan mendapatkan notif :
```NOTE: If you run ./clean-all, I will be doing a rm -rf on /etc/openvpn/easy-rsa/keys
```
Selanjutnya jalankan clean-all untuk mencegah *key* lain yang mungkin konflik dengan *key* server kita nanti.
```
./clean-all
```

Setelah itu kita akan membuat server *certificate* dan server *key* untuk `OpenVPN` server kita.

*Generate certificate* server 
```php
./build-key-server server
```






