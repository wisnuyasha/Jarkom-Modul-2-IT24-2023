
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

## Soal 12

## Soal 13

## Soal 14

## Soal 15

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
