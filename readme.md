
# Jarkom-Modul-2-IT24-2023

## Anggota Kelompok

- Wisnuyasha Faizal / 5027211036
- I Gusti Agung Bagus Maitreya Satyamurti / 5027211046

## Soal 1
> Yudhistira akan digunakan sebagai DNS Master, Werkudara sebagai DNS Slave, Arjuna merupakan Load Balancer yang terdiri dari beberapa Web Server yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Buatlah topologi dengan pembagian sebagai berikut. Folder topologi dapat diakses pada drive berikut 

### Soal Topologi
![SSTOPOLOGI](https://github.com/wisnuyasha/Jarkom-Modul-2-IT24-2023/assets/100693456/ee6495ec-8052-4b52-bb32-65851d8fad12)

### Node Configure

#### Pandudewanata (Router)
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 192.245.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 192.245.2.1
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 192.245.3.1
	netmask 255.255.255.0
```
#### Yudhistira (DNS Master)
```
auto eth0
iface eth0 inet static
	address 192.245.2.2
	netmask 255.255.255.0
	gateway 192.245.2.1
```
#### Werkudara (DNS Slave)
```
auto eth0
iface eth0 inet static
	address 192.245.2.3
	netmask 255.255.255.0
	gateway 192.245.2.1
```
#### Prabukusuma (Web Server)
```
auto eth0
iface eth0 inet static
	address 192.245.3.2
	netmask 255.255.255.0
	gateway 192.245.3.1
```
#### Abimanyu (Web Server)
```
auto eth0
iface eth0 inet static
	address 192.245.3.3
	netmask 255.255.255.0
	gateway 192.245.3.1
```
#### Wisanggeni (Web Server)
```
auto eth0
iface eth0 inet static
	address 192.245.3.4
	netmask 255.255.255.0
	gateway 192.245.3.1
```
#### Arjuna (Load Balancer)
```
auto eth0
iface eth0 inet static
	address 192.245.3.5
	netmask 255.255.255.0
	gateway 192.245.3.1
```
#### Nakula (Client)
```
auto eth0
iface eth0 inet static
	address 192.245.1.2
	netmask 255.255.255.0
	gateway 192.245.1.1
```
#### Sadewa (Client)
```
auto eth0
iface eth0 inet static
	address 192.245.1.3
	netmask 255.255.255.0
	gateway 192.245.1.1
```

### Router Console Config

Router (Pandudewanata) perlu diberikan config iptables, agar semua node yang mengakses ip router bisa mengakses internet
```
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.245.0.0/16
```
`192.245` adalah ip kelompok IT24

## Soal 2
> Buatlah website utama pada node arjuna dengan akses ke arjuna.yyy.com dengan alias www.arjuna.yyy.com dengan yyy merupakan kode kelompok.

1. Install dependencies & setup bind :
```bash
apt-get update
apt-get install bind9 -y
service bind9 start
```
2. Edit config pada bind `/etc/bind/named.conf.local` :
```bash
zone "arjuna.it24.com" {
    type master;
    file "/etc/bind/arjuna/arjuna.it24.com";
};
```
3. Edit DNS record `/etc/bind/arjuna/arjuna.it24.com` :
```bash
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     arjuna.it24.com. root.arjuna.it24.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      arjuna.it24.com.
@       IN      A       192.245.2.2
www     IN      CNAME   arjuna.it24.com.
```
4. Restart bind9
```
service bind9 restart
```
## Soal 3
> Dengan cara yang sama seperti soal nomor 2, buatlah website utama dengan akses ke abimanyu.yyy.com dan alias www.abimanyu.yyy.com.

1. Install dependencies & setup bind :
```bash
apt-get update
apt-get install bind9 -y
service bind9 start
```
2. Edit config pada bind `/etc/bind/named.conf.local` :
```bash
zone "abimanyu.it24.com" {
    type master;
    file "/etc/bind/abimanyu/abimanyu.it24.com";
};
```
3. Edit DNS record `/etc/bind/arjuna/arjuna.it24.com` :
```bash
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.it24.com. root.abimanyu.it24.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      abimanyu.it24.com.
@       IN      A       192.245.2.2
www     IN      CNAME   abimanyu.it24.com.
```
4. Restart bind9
```
service bind9 restart
```
## Soal 4
>Kemudian, karena terdapat beberapa web yang harus di-deploy, buatlah subdomain parikesit.abimanyu.yyy.com yang diatur DNS-nya di Yudhistira dan mengarah ke Abimanyu.

1. tambahkan line pada /etc/bind/named.local.conf:
```
zone "parikesit.abimanyu.it24.com" {
    type master;
    file "/etc/bind/it24/baratayuda/parikesit.abimanyu.it24.com";
};
```
2. record DNS pada /etc/bind/it24/baratayuda/parikesit.abimanyu.it24.com.
```
![image](https://github.com/wisnuyasha/Jarkom-Modul-2-IT24-2023/assets/100349628/6a954b81-e69b-4c9f-9f66-c37a698cc33c)

```

3.Restart apache
```
service bind9 restart
```

## Soal 5

>Buat juga reverse domain untuk domain utama. (Abimanyu saja yang direverse)
1.Pada file /etc/bind/named.local.conf tambahkan line berikut:
```
![image](https://github.com/wisnuyasha/Jarkom-Modul-2-IT24-2023/assets/100349628/82064b72-dab3-495e-8dea-828d64b6cb5e)
```

2.Buat DNS recordnya pada file /etc/bind/abimanyu.024/3.245.192.in-addr.arpa.
```
![image](https://github.com/wisnuyasha/Jarkom-Modul-2-IT24-2023/assets/100349628/570a80b8-fa33-439e-971d-7959a7134597)
```
3.Restart bind9 service
```
service bind9 restart
```

## Soal 6
>Agar dapat tetap dihubungi ketika DNS Server Yudhistira bermasalah, buat juga Werkudara sebagai DNS Slave untuk domain utama.

1.Edit konfigurasi zone arjuna.024.com & abimanyu.024.com pada file /etc/bind/named.local.conf sehingga menjadi seperti berikut.
```
![image](https://github.com/wisnuyasha/Jarkom-Modul-2-IT24-2023/assets/100349628/ddcad193-ae14-4f3e-ac8b-09cb56044cd0)
```

2. Di Werkudara
```
![image](https://github.com/wisnuyasha/Jarkom-Modul-2-IT24-2023/assets/100349628/2b372013-4334-4923-96d9-f84108e69765)

```

3.Restart bind9 service
```
service bind9 restart
```


## Soal 7
>Seperti yang kita tahu karena banyak sekali informasi yang harus diterima, buatlah subdomain khusus untuk perang yaitu baratayuda.abimanyu.yyy.com dengan alias www.baratayuda.abimanyu.yyy.com yang didelegasikan dari Yudhistira ke Werkudara dengan IP menuju ke Abimanyu dalam folder Baratayuda.

1.Edit file /etc/bind/abimanyu.024/abimanyu.024.com seperti berikut:

```
![image](https://github.com/wisnuyasha/Jarkom-Modul-2-IT24-2023/assets/100349628/a63b8e18-835e-4995-8209-67db89fca757)

```
2.Edit juga file /etc/bind/named.conf.options.
```
options {
        directory "/var/cache/bind";

        allow-query{any;};

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
};
```
3.Restart bind9 service
```
service bind9 restart
```

## Soal 8
>dengan IP menuju ke Abimanyu dalam folder Baratayuda.
Untuk informasi yang lebih spesifik mengenai Ranjapan Baratayuda, buatlah subdomain melalui Werkudara dengan akses rjp.baratayuda.abimanyu.yyy.com dengan alias www.rjp.baratayuda.abimanyu.yyy.com yang mengarah ke Abimanyu.

1.Pada file /etc/bind/named.local.conf tambahkan line seperti berikut:
```
![image](https://github.com/wisnuyasha/Jarkom-Modul-2-IT24-2023/assets/100349628/1eada20c-1369-4bfc-ba66-a8ec27a9fdeb)
```

2.Buat DNS record pada file /etc/bind/it29/baratayuda/baratayuda.abimanyu.024.com.
```
![image](https://github.com/wisnuyasha/Jarkom-Modul-2-IT24-2023/assets/100349628/5006f396-7f69-4cae-a234-1f55939e2611)
```

3.Restart bind9 service
```
service bind9 restart
```

## Soal 9
> Arjuna merupakan suatu Load Balancer Nginx dengan tiga worker (yang juga menggunakan nginx sebagai webserver) yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Lakukan deployment pada masing-masing worker.

1. Install dependencies :
```
apt-get update -y
apt-get install nginx unzip php7.2-fpm -y
```
2. Edit file konfigurasi untuk setiap worker di `/etc/nginx/sites-available/arjuna.it24.com` dan `/etc/nginx/sites-enabled/arjuna.it24.com` :
- Prabakusuma
```
server {
    listen 8001;
    listen [::]:8001;

    root /var/www/arjuna.it24.com;
    index index.php index.html index.htm index.nginx-debian.html;

    server_name arjuna.it24.com;

    location / {
        try_files \$uri \$uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;

        fastcgi_pass unix:/run/php/php7.2-fpm.sock;
    }

    location ~ /\.ht {
        deny all;
    }
}
```
- Abimanyu
```
server {
    listen 8002;
    listen [::]:8002;

    root /var/www/arjuna.it24.com;
    index index.php index.html index.htm index.nginx-debian.html;

    server_name arjuna.it24.com;

    location / {
        try_files \$uri \$uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;

        fastcgi_pass unix:/run/php/php7.2-fpm.sock;
    }

    location ~ /\.ht {
        deny all;
    }
}
```
- Wisanggeni
```
server {
    listen 8003;
    listen [::]:8003;

    root /var/www/arjuna.it24.com;
    index index.php index.html index.htm index.nginx-debian.html;

    server_name arjuna.it24.com;

    location / {
        try_files \$uri \$uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;

        fastcgi_pass unix:/run/php/php7.2-fpm.sock;
    }

    location ~ /\.ht {
        deny all;
    }
}
```
3. Download resource yang telah diberikan dan diletakkan ke file `/var/www/arjuna.it24.com/`
```
wget --no-check-certificate 'https://docs.google.com/uc?export=download&id=17tAM_XDKYWDvF-JJix1x7txvTBEax7vX' -O /tmp/file.zip

unzip /tmp/file.zip -d /tmp

mv /tmp/arjuna.yyy.com/index.php /var/www/arjuna.it24.com/index.php
```

4. Restart/start service php / nginx
```
service php7.2-fpm start

service nginx restart
```
SS OUTPUT :
![ssno910](https://github.com/wisnuyasha/Jarkom-Modul-2-IT24-2023/assets/100693456/1f75b339-46ed-4b46-acc9-f3d85a3a3a26)
![ssnoo910](https://github.com/wisnuyasha/Jarkom-Modul-2-IT24-2023/assets/100693456/47e8f779-c6d9-4150-a323-4823f95c6a51)
![ssnooo910](https://github.com/wisnuyasha/Jarkom-Modul-2-IT24-2023/assets/100693456/8e6d81d4-8f6a-4bfb-be8d-af8306120f5d)

## Soal 10
> Kemudian gunakan algoritma Round Robin untuk Load Balancer pada Arjuna. Gunakan server_name pada soal nomor 1. Untuk melakukan pengecekan akses alamat web tersebut kemudian pastikan worker yang digunakan untuk menangani permintaan akan berganti ganti secara acak. Untuk webserver di masing-masing worker wajib berjalan di port 8001-8003. Contoh
- Prabakusuma:8001
- Abimanyu:8002
- Wisanggeni:8003

1. Install Dependencies
```
apt-get update -y
apt-get install nginx -y
```
2. Edit file konfigurasi nginx pada `/etc/nginx/nginx.conf`
```
user www-data;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;

events {
    worker_connections 768;
    # multi_accept on;
}

http {
    upstream nodes_lb {
        server 192.245.3.2:8001;
        server 192.245.3.3:8002;
        server 192.245.3.4:8003;
    }

    server {
        listen 80;
        listen [::]:80;

        server_name arjuna.it24.com;

        location / {
            proxy_pass http://nodes_lb;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }
    }
}
```
3. Restart service nginx
```
service nginx start

```
SS OUTPUT DI NO 9
## Revisi

## Soal 11
> Selain menggunakan Nginx, lakukan konfigurasi Apache Web Server pada worker Abimanyu dengan web server www.abimanyu.yyy.com. Pertama dibutuhkan web server dengan DocumentRoot pada /var/www/abimanyu.yyy

1. Install dependencies setup webserver
```
apt-get install apache2 libapache2-mod-php7.2 -y
```

2. Lakukan enable konfigurasi abimanyu.it24.com.conf 
```
a2ensite abimanyu.it24.com
```
3. Edit file konfigurasi pada file `/etc/apache2/sites-available/abimanyu.it24.com.conf`
```
<VirtualHost *:80>
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/abimanyu.it24
    ServerName abimanyu.it24.com
    ServerAlias www.abimanyu.it24.com
    
    <Directory /var/www/abimanyu.it24>
        Options +FollowSymLinks -Multiviews
        AllowOverride All
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

</VirtualHost>
```

4. Download resource yang telah diberikan dan diletakkan ke file `/var/www/abimanyu.it24`
```
wget 'https://docs.google.com/uc?export=download&id=1a4V23hwK9S7hQEDEcv9FL14UkkrHc-Zc' -O /tmp/abimanyu.zip

unzip -o /tmp/abimanyu.zip -d /tmp

# enable konfigurasi
a2ensite abimanyu.it24.com

mv /tmp/abimanyu.yyy.com/* /var/www/abimanyu.it24/ -f
```

5. Lakukan disable konfig apache2 dan start service apache

```
a2dissite 000-default.conf
service apache2 start
```
#### Testing :
```
lynx abimanyu.it24.com
```
#### SS OUTPUT :
![ssno11](https://github.com/wisnuyasha/Jarkom-Modul-2-IT24-2023/assets/100693456/ec6c0330-d4ee-49ef-9bbe-5f44115fb54c)

## Soal 12
> Setelah itu ubahlah agar url www.abimanyu.yyy.com/index.php/home menjadi www.abimanyu.yyy.com/home.
1. Lakukan aktivasi rewrite
```
a2enmod rewrite
```

2. Buatlah file `var/www/abimanyu.it24/.htaccess`
```
RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^home$ /index.php/home [NC,L]
```

3. Restart apache2
```
service apache2 restart
```
#### Testing :
```
lynx www.abimanyu.it24.com/home
```
#### SS OUTPUT :
![ssno12](https://github.com/wisnuyasha/Jarkom-Modul-2-IT24-2023/assets/100693456/2c6fac9d-c7a6-4a01-850c-d51cded82899)

## Soal 13
> Selain itu, pada subdomain www.parikesit.abimanyu.yyy.com, DocumentRoot disimpan pada /var/www/parikesit.abimanyu.yyy

1. Lakukan enable konfigurasi `abimanyu.it24.com.conf`
```
a2ensite parikesit.abimanyu.it24.com
```

2. Edit konfigurasi pada `/etc/apache2/sites-available/parikesit.abimanyu.it24.com.conf` 
```
<VirtualHost *:80>
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/parikesit.abimanyu.it24
    ServerName parikesit.abimanyu.it24.com
    ServerAlias www.parikesit.abimanyu.it24.com

    <Directory /var/www/parikesit.abimanyu.it24>
        Options +FollowSymLinks -Multiviews
        AllowOverride All
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

</VirtualHost>
```

3. Download resource yang telah diberikan dan diletakkan ke file `/var/www/parikesit.abimanyu.it24/`
```
mkdir -p /var/www/parikesit.abimanyu.it24

wget 'https://docs.google.com/uc?export=download&id=1LdbYntiYVF_NVNgJis1GLCLPEGyIOreS' -O /tmp/parikesit.zip

unzip -o /tmp/parikesit.zip -d /tmp

mv /tmp/parikesit.abimanyu.yyy.com/* /var/www/parikesit.abimanyu.it24/ -f
```

#### Testing :
```
lynx www.parikesit.abimanyu.it24.com
```
#### SS Output :
![ssno13](https://github.com/wisnuyasha/Jarkom-Modul-2-IT24-2023/assets/100693456/caa953c2-00fb-4d12-91ef-05b22466a1fe)

## Soal 14
> Pada subdomain tersebut folder /public hanya dapat melakukan directory listing sedangkan pada folder /secret tidak dapat diakses (403 Forbidden).
1. Buat directory secret 
```
mkdir /var/www/parikesit.abimanyu.it24/secret
```

2. Input file ke secret.txt
```
echo "secret" > /var/www/parikesit.abimanyu.it24/secret/secret.txt
```

3. Untuk disable directory listing secret, lakukan edit pada `/var/www/parikesit.abimanyu.it24/secret/.htaccess`
```
Options -Indexes
```

#### Testing
```
lynx www.parikesit.abimanyu.it24.com/secret
```
#### SS Output
![ssno14](https://github.com/wisnuyasha/Jarkom-Modul-2-IT24-2023/assets/100693456/21dd193f-6178-4264-9f7c-fb583d218b03)
![ssnooo14](https://github.com/wisnuyasha/Jarkom-Modul-2-IT24-2023/assets/100693456/7ba1ebb7-fa7b-4440-b24b-73acc04de80f)

## Soal 15
> Buatlah kustomisasi halaman error pada folder /error untuk mengganti error kode pada Apache. Error kode yang perlu diganti adalah 404 Not Found dan 403 Forbidden.

1. Edit file `/var/www/parikesit.abimanyu.it24/.htaccess` sehingga error menjadi 404 not found dan 403 forbidden
```
ErrorDocument 404 /error/404.html
ErrorDocument 403 /error/403.html
```
#### Testing
```
lynx www.parikesit.abimanyu.it24.com/secret

```
#### SS Output
![ssno15](https://github.com/wisnuyasha/Jarkom-Modul-2-IT24-2023/assets/100693456/a030252f-2df5-48b2-aa17-48375aab192b)

## Soal 16
>Buatlah suatu konfigurasi virtual host agar file asset www.parikesit.abimanyu.yyy.com/public/js menjadi 
www.parikesit.abimanyu.yyy.com/js

1.Command :
![WhatsApp Image 2023-10-17 at 17 17 54_e004a369](https://github.com/wisnuyasha/Jarkom-Modul-2-IT24-2023/assets/100349628/27ce776b-35a2-41c7-8865-3cab50dd74d5)

2.Output:
![WhatsApp Image 2023-10-17 at 17 17 06_f03cd7b0](https://github.com/wisnuyasha/Jarkom-Modul-2-IT24-2023/assets/100349628/6205aa3b-d1af-4a20-bb4e-7f296cba5178)

## Soal 17 & 18
>Agar aman, buatlah konfigurasi agar www.rjp.baratayuda.abimanyu.yyy.com hanya dapat diakses melalui port 14000 dan 14400.
>Untuk mengaksesnya buatlah autentikasi username berupa “Wayang” dan password “baratayudayyy” dengan yyy merupakan kode kelompok. Letakkan DocumentRoot pada /var/www/rjp.baratayuda.abimanyu.yyy.

1.Command :
![WhatsApp Image 2023-10-17 at 17 19 47_6ddf2e23](https://github.com/wisnuyasha/Jarkom-Modul-2-IT24-2023/assets/100349628/3e7f15c3-3014-4e70-8bd6-1cab2fce0c09)

2.Output :
![WhatsApp Image 2023-10-17 at 17 22 16_b7b66f7b](https://github.com/wisnuyasha/Jarkom-Modul-2-IT24-2023/assets/100349628/41152b6b-6901-4936-8345-0e44e7285f8f)
![WhatsApp Image 2023-10-17 at 17 21 40_23e1da45](https://github.com/wisnuyasha/Jarkom-Modul-2-IT24-2023/assets/100349628/add4f7df-5173-441c-8af8-5ba977fe7aca)


## Soal 19
>Buatlah agar setiap kali mengakses IP dari Abimanyu akan secara otomatis dialihkan ke www.abimanyu.yyy.com (alias)

1.Command :
![WhatsApp Image 2023-10-17 at 17 22 53_1b69a233](https://github.com/wisnuyasha/Jarkom-Modul-2-IT24-2023/assets/100349628/9003a652-ba21-49e8-8bbe-5172784396c3)

2.Ouput :
![WhatsApp Image 2023-10-17 at 17 23 07_ebed1cd2](https://github.com/wisnuyasha/Jarkom-Modul-2-IT24-2023/assets/100349628/638c90a6-1a32-4c6f-9b4e-137865af74ff)

## Soal 20
>Karena website www.parikesit.abimanyu.yyy.com semakin banyak pengunjung dan banyak gambar gambar random, maka ubahlah request gambar yang memiliki substring “abimanyu” akan diarahkan menuju abimanyu.png.

1.Command :
![WhatsApp Image 2023-10-17 at 18 18 07_f3ca0e0d](https://github.com/wisnuyasha/Jarkom-Modul-2-IT24-2023/assets/100349628/8316b2ea-4b80-408f-b390-6aa50b730823)

2.Output :
![WhatsApp Image 2023-10-17 at 18 18 17_f960cd79](https://github.com/wisnuyasha/Jarkom-Modul-2-IT24-2023/assets/100349628/ec84f75e-8628-42f4-8b63-933c9c43324f)
