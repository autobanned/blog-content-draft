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

* Meningkatkan level enkripsi 
* Meneruskan *web traffic* ke alamat tujuan
* Mencegah bocornya DNS *request* keluar dari koneksi VPN 
* Menyetting *permission* dan memperkuatnya

tapi kita menyesuaikan settingan file konfig di `server.conf` kita akan terlebih dahulu membuat sertifikat dan key, yang akan digunakan sebagai autentikasi untuk `OpenVPN` server kita.
Pertama kita akan meng*copy* `Easy-rsa` *generation script*
```php
sudo cp -r /usr/share/easy-rsa/ /etc/openvpn
```
Selanjutnya buat direktori tempat kita menyimpan key dan sertifikat `OpenVPN` kita.
```php
sudo mkdir /etc/openvpn/easy-rsa/keys
```
Setelah itu kita akan menyetting parameter untuk *certificate* kita sebelum sertifikat itu di buat.
```php
sudo nano /etc/openvpn/easy-rsa/vars
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

Selanjutnya kita akan pindah ke direktori `/etc/openvpn` untuk meng*generate* *Diffie-Helman* parameter menggunakan *tool* bawaan dari OPENSSL bernama `dhparam` 
```php
cd /etc/openvpn
sudo openssl dhparam -out /etc/openvpn/easy-rsa/keys/dh4096.pem 4096
```
opsi -out diatas mendefinisikan lokasi output hasil *generate* dari dhparam.

Dengan demikian *dhparam*  kita telah berhasil dibuat, selanjutnya kita akan meng*generate* *certificate* server  dan server *key*.

Untuk menggenerate server *key* dan server *certificate* kita akan pindah satu direktori ke direktory *easy-rsa* dan meng*generate* pki (Public Key Infrastructure) dari file `vars` yang telah kita setting variablenya sebelumnya
```php
cd easy-rsa
sudo . ./vars
```
<sup>perhatikan tanda spasi (white space) diantara titik, saat meng*generate* pki diatas. </sup>

Saat menjalankan perintah diatas kita akan mendapatkan notif :
```
NOTE: If you run ./clean-all, I will be doing a rm -rf on /etc/openvpn/easy-rsa/keys
```
Selanjutnya jalankan clean-all untuk mencegah *key* lain yang mungkin konflik dengan *key* server kita nanti, dan membuat ca *certificate*
```
sudo ./clean-all
sudo ./build-ca
```
Perintah `./build-ca` diatas akan memanggil open-ssl interaktif dan memberikan beberapa *challange question* seperti berikut:
```php
Generating a 1024 bit RSA private key
............++++++
...........++++++
writing new private key to 'ca.key'
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [KG]:
State or Province Name (full name) [NA]:
Locality Name (eg, city) [BISHKEK]:
Organization Name (eg, company) [OpenVPN-TEST]:
Organizational Unit Name (eg, section) []:
Common Name (eg, your name or your server's hostname) []:OpenVPN-CA
Email Address [me@myhost.mydomain]:
```
Dengan demikian kita sudah memiliki *certificate* ca master kita, selanjutnya kita akan membuat  *key* server dan *certificate* server untuk openvpn server dengan perintah berikut:

```php
sudo ./build-key-server server
```
kita akan kembali mendapatkan *challenge question* interaktif dengan tambahan extra 2 pertanyaan seperti ini:
```
Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:

Sign the certificate? [y/n]
1 out of 1 certificate requests certified, commit? [y/n]
```
<sup>seluruh pertanyaan challenge question sebaiknya dibiarkan bernilai default saja dengan menekan `enter`, karna kita sudah menyetting nilai variabelnya di file `vars`. Untuk extra pada saat membuat server *key* dan server *certificate* biarkan bernilai kosong.</sup>

Setelah itu, selanjutnya kita akan mengcopy semua file kedalam directory *keys* yang sudah kita buat sebelumnya  dan merubah permission dari direktori tersebut supaya lebih aman.
```php
sudo cp /etc/openvpn/easy-rsa/keys/{server.crt,server.key,ca.crt} /etc/openvpn
sudo chmod 400 /etc/openvpn/easy-rsa/keys
```

Selanjutnya kita akan kembali ke directory `openvpn` untuk melanjutkan mengubah settingan dari file config.
```php
cd /etc/openvpn
sudo nano server.conf
```
beberapa hal yang akan kita ubah:
```php
# Diffie hellman parameters.
# Generate your own with:
#   openssl dhparam -out dh1024.pem 1024
# Substitute 2048 for 1024 if you are using
# 2048 bit keys.
dh dh1024.pem



```
* mengubah dhparam dari default 1024 ke 4096, sesuai dengan nilai key `dhparam` yang kita buat sebelumnya.

```php
;push "redirect-gateway def1 bypass-dhcp"
```
* ijinkan opsi push "redirect-gateway" dengan menghapus *comment (;)*nya.

```php
# Certain Windows-specific network settings
# can be pushed to clients, such as DNS
# or WINS server addresses.  CAVEAT:
# http://openvpn.net/faq.html#dhcpcaveats
# The addresses below refer to the public
# DNS servers provided by opendns.com.
;push "dhcp-option DNS 208.67.222.222"
;push "dhcp-option DNS 208.67.220.220"
```
* hapus *comment* dari opsi `push "dhcp-option"` dan gantilah alamt DNS dengan alamat DNS yang kita ingin gunakan.
Kita bisa menggunakan opsi DNS dari (daftar public DNS ini)[http://public-dns.info].






