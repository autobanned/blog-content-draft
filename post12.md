Title: Membuat halaman phpinfo
Date: 2017-04-11 10:30
Modified: 2017-04-09 23:40
Tags: programming, php, www
Category: Programming
Slug: membuat-halaman-php-info
Authors: andy.zs
Summary: Membuat halaman phpinfo

Halaman `phpinfo` adalah halaman yang menampilkan seluruh informasi dari php modul dan `extension`, beserta dengan informasi dari webserver yang digunakan.
Untuk membuat halaman `phpinfo` ini maka buatlah file php berikut:


```c
<?php

phpinfo();

?>
``` 
file ini akan menampilkan seluruh info dari php kita beserta modul/extensi dan informasi lainnya. 
Jika kita hanya ingin mengetahui modul yang terinstall saja maka ubahlah menjadi seperti ini:
```c
<?

phpinfo(INFO_MODULES);
```

lalu simpan dengan nama apa saja, dan letakan di direktori yang dapat diakses oleh web server dan jangan lupa setting *permission* file.

Sekarang akseslah dari browser dengan memasukan `http://localhost-atau-alamat-ip/direktori/file-phpinfo` .
Disini menyimpan file tersebut menggunakan nama `info.php`, di direktori root html bawaan nginx di debian. maka saya membuka di `address bar`
```http://127.0.0.1/info.php```

maka kita akan ditampilkan dengan
informasi dari php dan extensi dan module yang terinstall seperti ini:





