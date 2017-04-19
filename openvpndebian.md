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
kemudian buat *symbolic links* dari `openssl-1.0.0.cnf` ke `openssl.cnf`:
```php
sudo ln -s openssl-1.0.0.cnf openssl.cnf
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

* mendefinisikan lokasi sertifikat master ca, dan server *certificate* and server *key* berada
```
# Any X509 key management system can be used.
# OpenVPN can also use a PKCS #12 formatted key file
# (see "pkcs12" directive in man page).
ca /etc/openvpn/keys/ca.crt
cert /etc/openvpn/keys/openvpn-server.crt
key /etc/openvpn/keys/openvpn-server.key 
```
* mengubah dhparam dari default 1024 ke 4096, sesuai dengan nilai key `dhparam` yang kita buat sebelumnya.
```php
# Diffie hellman parameters.
# Generate your own with:
#   openssl dhparam -out dh1024.pem 1024
# Substitute 2048 for 1024 if you are using
# 2048 bit keys.
dh dh4096.pem
```
* ijinkan opsi push "redirect-gateway" dengan menghapus *comment(;)* nya.
```php
;push "redirect-gateway def1 bypass-dhcp"
```
* hapus *comment* dari opsi `push "dhcp-option"` dan gantilah alamt DNS dengan alamat DNS yang kita ingin gunakan.
Kita bisa menggunakan opsi DNS dari [daftar public DNS ini](https://www.lifewire.com/free-and-public-dns-servers-2626062)
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

Selanjutnya kita akan membuat user tanpa login shell dan *privileges root* dan menyetting `user` dan `group`, karna secara *default* `openvpn` berjalan dengan akun *root* atau superuser, maka untuk meningkatkan keamanan kita akan merubah ini, agar setelah berjalan di *background* openvpn server menurukan *privileges*nya menjadi user yang baru kita buat.
```php
sudo adduser --system --shell /usr/sbin/nologin --no-create-home openvpn
```
kemudian cari baris berikut, hapus *comment*nya dan ganti *user*nya.
```php
# You can uncomment this out on
# non-Windows systems.
;user nobody
;group nogroup
```
menjadi seperti ini 
```php
# You can uncomment this out on
# non-Windows systems.
user openvpn
group nogroup
```
Agar client yang saling terhubung dapat saling melihat satu sama lain dan berkomunikasi, enable opsi `client-to-client`
```php
# Uncomment this directive to allow different
# clients to be able to "see" each other.
# By default, clients will only see the server.
# To force clients to only see the server, you
# will also need to appropriately firewall the
# server's TUN/TAP interface.
client-to-client
```

Untuk mengijinkan satu *certificate* digunakan oleh lebih dari satu user disaat yang bersamaan, maka kita enable opsi `duplicate-cn` dengan menghapusn*comment*nya
```php
# IF YOU HAVE NOT GENERATED INDIVIDUAL
# CERTIFICATE/KEY PAIRS FOR EACH CLIENT,
# EACH HAVING ITS OWN UNIQUE "COMMON NAME",
# UNCOMMENT THIS LINE OUT.
duplicate-cn
```

Opsi lain yang bisa ditambahkan atau di ubah untuk memperkuat openvpn server kita seperti
* merubah *chipper* dari 128 menjadi 256
```php
echo 'cipher AES-256-CBC' >> /etc/openvpn/server.conf
```
* merubah auth dari default SHA-1 dengan SHA-2 512
```php
echo 'auth SHA512' >> /etc/openvpn/server.conf
```
* meningkatkan VPN kontrol channel dengan *chipper suite* yang lebih kuat sesuai refrensi [disini](https://community.openvpn.net/openvpn/wiki/Hardening#Useof--tls-cipher)
```php
echo 'tls-cipher TLS-DHE-RSA-WITH-AES-256-GCM-SHA384:TLS-DHE-RSA-WITH-AES-128-GCM-SHA256:TLS-DHE-RSA-WITH-AES-256-CBC-SHA:TLS-DHE-RSA-WITH-CAMELLIA-256-CBC-SHA:TLS-DHE-RSA-WITH-AES-128-CBC-SHA:TLS-DHE-RSA-WITH-CAMELLIA-128-CBC-SHA' >> /etc/openvpn/server.conf        
```
* menggunakan opsi --tls-auth
Untuk menggunakan opsi ini, kita harus meng*generate*nya terlebih dahulu
```php
sudo openvpn --genkey --secret easy-rsa/keys/ta.key
```
Kemudian ubah opsi tls-auth di config menjadi seperti ini
```php
# The server and each client must have
# a copy of this key.
# The second parameter should be '0'
# on the server and '1' on the clients.
tls-auth /etc/openvpn/easy-rsa/keys/ta.key 0 # This file is secret
```

Selanjutnya kita akan mengkonfigurasi firewall dan enable *packet forwarding*
* enable *packet forwarding* untuk mengijinkan opsi ini maka kita akan merubah baris di `/etc/sysctl.conf`
dari 
```php
# Uncomment the next line to enable packet forwarding for IPv4
#net.ipv4.ip_forward=1
```
menjadi seperti ini
```php
# Uncomment the next line to enable packet forwarding for IPv4
net.ipv4.ip_forward=1
```
* Install dan Setting `ufw`. UFW adalah front-end untuk mempermudah manajemen dan administrasi dari linux IPTables.
```php
sudo apt install ufw
```
kemudian ubah default *policy*
dari seperti ini
```
DEFAULT_FORWARD_POLICY="DROP"
```
menjadi 
```php
DEFAULT_FORWARD_POLICY="ACCEPT"
```
Selanjutnya kita akan menambahkan rules baru untuk `openvpn` pada UFW.
```
nano /etc/ufw/before.rules
```
kemudian tambahkan baris berikut sebelum baris terahir `Don't delete`:
```php
# START OPENVPN RULES
# NAT table rules
*nat
:POSTROUTING ACCEPT [0:0]
# Allow traffic from OpenVPN client to eth0
-A POSTROUTING -s 10.8.0.0/8 -o eth0 -j MASQUERADE
COMMIT
# END OPENVPN RULES
```
Swlanjutnya kita akan mengijinkan koneksi service seperti ssh, web server serta openvpn ke ufw dan menjalankan ufw *service*
```php
sudo ufw allow ssh
sudo ufw allow http
sudo ufw allow https
sudo ufw allow 1194/udp
sudo ufw enable
```
jika kita ingin mengecek status ufw bisa dilakukan dengan perintah:
```php
sudo ufw status
```
Selanjutnya *save* dan *restart service* openvpn server
```php
sudo systemctl restart openvpn
```

#### *Generate client credential*

Selanjutnya kita akan meng*generate* dan setting *certificate* untuk client, untuk melakukan ini kita akan kembali pindah ke direktori easy-rsa dan mulai meng*generate*nya:
```php
cd easy-rsa
sudo ./build-key client1
```
<sub> untuk penamaan client1 ini bebas, kita bisa memberikan nama sesuai kemauan kita</sub>

Pada saat proses *build-key* untuk client ini kita akan kembali mendapatkan *challenge question* variabel yang bisa kita biarkan blank saja agar terisi nilai default sesuai yang kita isi di file `vars` .
```php
Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:
```
dan pertanyaan konfirmasi
```php
Sign the certificate? [y/n]
1 out of 1 certificate requests certified, commit? [y/n]
```
kita bisa mengulangi proses *generate* *certificate* dan *keys* untuk beberapa klien sesuai yang diinginkan, dan jika dikemudian hari kita ingin menambahkan client kita dan meng*generate* kembali *credential* baru untuk client yang baru kita cukup melakukan perintah ini:
```php
cd /etc/openvpn/easy-rsa && source ./vars && ./build-key client1
```

Setelah selesai meng*generate credential* *certificate* dan *key* untuk client selanjutnya kita akan membuat client config, untuk mengkonfigurasinya.
Disini kita akan membuat client *credential* menjadi single file berformat ovpn dimana *certifiate* dan *key* akan di tuliskan secara inline didalam file ovpn ini agar lebih mudah digunakan diberbagai platform.

copy client sample
```php
sudo cp /usr/share/doc/openvpn/examples/sample-config-files/client.conf /etc/openvpn/easy-rsa/keys/client.ovpn
```
selannjutnya kita akan merubah settingannya:

* rubah baris my-server-1 dengan alamat IP dari openvpn server kita
```php
# The hostname/IP and port of the server.
# You can have multiple remote entries
# to load balance between the servers.
remote  172.16.10.5 1194
;remote my-server-2 1194
```
* Hapus *comment* dari baris `user` dan `group` sama seperti yang kita lakukan di settingan `server.conf`
```php
# Downgrade privileges after initialization (non-Windows only)
user nobody
group no group
```
* *Commented out* atau hapus baris SSL/TLS param dan tls-auth karna kita akan memasukan semua file *ca,cert dan key* secara inline di ahir file
```php
# SSL/TLS parms.
# See the server config file for more
# description.  It's best to use
# a separate .crt/.key file pair
# for each client.  A single ca
# file can be used for all clients.
#ca ca.crt
#cert client.crt
#key client.key




# If a tls-auth key is used on the server
# then every client must also have the key.
#tls-auth 1
```
* Samakan opsi-opsi lain yang ditambahkan di konfigurasiserver seperti `chipper`, `SHA-auth` dan `tls-chipper`
```php
cipher AES-256-CBC
auth SHA512
tls-cipher TLS-DHE-RSA-WITH-AES-256-GCM-SHA384:TLS-DHE-RSA-WITH-AES-128-GCM-SHA256:TLS-DHE-RSA-WITH-AES-256-CBC-SHA:TLS-DHE-RSA-WITH-CAMELLIA-256-CBC-SHA:TLS-DHE-RSA-WITH-AES-128-CBC-SHA:TLS-DHE-RSA-WITH-CAMELLIA-128-CBC-SHA
```
Diahiran file kita akan memasukan *certificate* ca dan server *certificate* (cert) dan server *key* beserta tls-auth ke file `client.ovpn`
```
sudo echo "<ca>" | cat ca.crt >> client.ovpn && sudo echo "</ca>" >> client.ovpn
sudo echo "<cert>" | cat server.crt >> client.ovpn && sudo echo "</cert>" >> client.ovpn
sudo echo "<key>" | cat server.key >> client.ovpn && sudo echo "</key>" >> client.ovpn
sudo echo "key-direction 1" >> client.ovpn
sudo echo "<tls-auth>" | cat ta.key >> client.ovpn && sudo echo "</tls-auth>" >> client.ovpn
```
Selanjutnya kita bisa meng*copy* file `client.ovpn` ke client yang akan terhubung dengan openvpn server menggunakan `scp` atau lainnya,  dan kemudian mengeteset nya. 

Sekian dulu tutorial Install dan setting *secure* openvpn servernya, thx

