# Jarkom-Modul-3-B01-2021

Kelompok B01

|      NRP       |                  Nama                   |
| :------------: | :-------------------------------------: |
| 05111940000120 |       Jonathan Timothy Siregar          |
| 05111940000195 |    Muhammad Fikri Sandi Pratama         |
| 05111940000207 |      Mohammad Thoriq Huda               |

- [Soal Shift Modul 3](https://docs.google.com/document/d/1hwuI5YpxiP-aboS7wGWPbaQeSOQl0HHVHLT3ws2BPUk/edit)

### Membuat Topologi
Luffy yang sudah menjadi Raja Bajak Laut ingin mengembangkan daerah kekuasaannya dengan membuat peta seperti berikut :

![1 1](https://user-images.githubusercontent.com/55092974/141379277-51db3549-90d0-4ebf-a4ef-8277eaaa6607.JPG)

### Edit Knofigurasi Network
#### Foosha
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 192.177.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 192.177.2.1
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 192.177.3.1
	netmask 255.255.255.0
```
#### EniesLobby
```
auto eth0
iface eth0 inet static
	address 192.177.2.2
	netmask 255.255.255.0
	gateway 192.177.2.1
```
#### Water7
```
auto eth0
iface eth0 inet static
	address 192.177.2.3
	netmask 255.255.255.0
	gateway 192.177.2.1
```
### Jipangu
```
auto eth0
iface eth0 inet static
	address 192.177.2.4
	netmask 255.255.255.0
	gateway 192.177.2.1
```
#### Loguetown, Alabasta, Totoland, dan Skypie (Client) 
```
auto eth0
iface eth0 inet dhcp
```

## No. 1
Luffy bersama Zoro berencana membuat peta tersebut dengan kriteria EniesLobby sebagai DNS Server, Jipangu sebagai DHCP Server, Water7 sebagai Proxy Server

#### Foosha

- Pertama yaitu menjalankan command `iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.177.0.0/16` yang bertujuan untuk dapat terhubung ke jaringan luar pada router `Foosha`.

- Kemudian servernya yaitu pada EniesLobby, Water7, dan Jipangu jalankan command `echo "nameserver 192.168.122.1" > /etc/resolv.conf` untuk setting IP agar dapat terhubung ke jaringan luar.

#### EniesLobby

- Lalu pada EniesLobby jalankan command `apt-get update` dan `apt-get install bind9 -y` untuk menginstall bind9, Karena akan menjadi DNS server.

#### Water7

- Pada bagian Water7 jalankan command `apt-get update` dan `apt-get install squid -y` untuk menginstall squid, Karena akan menjadi proxy.

#### Jipangu

- Pada bagian Jipangu jalankan command `apt-get update` dan `apt-get install isc-dhcp-server -y` untuk menginstall isc-dhcp-server, Karena akan menjadi DHCP Server.

- Kemudian setting `INTERFACES` yang digunakan oleh Jipangu pada file `/etc/default/isc-dhcp-server` dengan menambahkan `eth0`.

## No. 2
Foosha sebagai DHCP Relay

#### Foosha
- Pertama-tama yaitu pada bagian Foosha jalankan command `apt-get update` dan `apt-get install isc-dhcp-relay -y` untuk menginstall isc-dhcp-relay.

- Kemudian edit file `/etc/default/isc-dhcp-relay` dengan menambahkan `SERVER = "IP Jipangu"` dan `INTERFACES = "eth1 eth2 eth3"`, Seperti gambar berikut :

![2 1](https://user-images.githubusercontent.com/55092974/141381175-6ed05e99-f2b5-4542-85c8-58a52a3c20c8.JPG)

- Setelah itu jalankan command `service isc-dhcp-relay restart` untuk restart dhcp-relaynya.

## No. 3
Client yang melalui Switch1 mendapatkan range IP dari [prefix IP].1.20 - [prefix IP].1.99 dan [prefix IP].1.150 - [prefix IP].1.169

#### Jipangu
- Pertama-tama yaitu setting edit file `/etc/dhcp/dhcpd.conf` kemudian edit bagian rangenya sesuai dengan soal.

```
    subnet 192.177.1.0 netmask 255.255.255.0 {
    range 192.177.1.20 192.177.1.99;
    range 192.177.1.150 192.177.1.169;
    option routers 192.177.1.1;
    option broadcast-address 192.177.1.255;
    option domain-name-servers 192.177.2.2 , 192.168.122.1;
    default-lease-time 360;
    max-lease-time 7200;
}    
```
![3 a](https://user-images.githubusercontent.com/55092974/141385631-028da8d8-c059-4768-aefe-669bec84f78e.JPG)

- Kemudian jalankan command `service isc-dhcp-server restart`
- Lalu cek IP di client `Loguetown` dengan cara `ip a` seperti pada gambar berikut :

![3 b](https://user-images.githubusercontent.com/55092974/141385723-23f7f9f8-2a61-4802-a904-783da9170457.JPG)

## No. 4
Client yang melalui Switch3 mendapatkan range IP dari [prefix IP].3.30 - [prefix IP].3.50

#### Jipangu
- Pertama-tama yaitu setting edit file `/etc/dhcp/dhcpd.conf` kemudian edit bagian rangenya sesuai dengan soal.

```
    subnet 192.177.3.0 netmask 255.255.255.0 {
    range 192.177.3.30 192.177.3.50;
    option routers 192.177.3.1;
    option broadcast-address 192.177.3.255;
    option domain-name-servers 192.177.2.2 , 192.168.122.1;
    default-lease-time 720;
    max-lease-time 7200;
}
```
![4 a](https://user-images.githubusercontent.com/55092974/141386115-7b8abad8-0db3-4d14-b0b9-576a8c0305b1.JPG)

- Kemudian jalankan command `service isc-dhcp-server restart`
- Lalu cek IP di client `TattoLand` dengan cara `ip a` seperti pada gambar berikut :

![4 b](https://user-images.githubusercontent.com/55092974/141386147-51fb8d95-a76c-4c45-a2c3-1cb9dd9a217f.JPG)

## No. 5
Client mendapatkan DNS dari EniesLobby dan client dapat terhubung dengan internet melalui DNS tersebut.


edit file `/etc/dhcp/dhcpd.conf` pastikan ada baris `option domain-name-servers 192.177.2.2 , 192.168.122.1;` pada subnet swicth 1 dan 3



![image](https://user-images.githubusercontent.com/81372291/141606195-8260c251-bf0d-47f3-8d78-cdc15d46764b.png)


bisa dicek dengan `ping google.com` pada client


![image](https://user-images.githubusercontent.com/81372291/141606246-0ac8f39e-899c-41b8-b441-b3a0ab71a834.png)

## No. 6
Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch1 selama 6 menit sedangkan pada client yang melalui Switch3 selama 12 menit. Dengan waktu maksimal yang dialokasikan untuk peminjaman alamat IP selama 120 menit.

edit file `/etc/dhcp/dhcpd.conf` edit default lease time menjadi 360 untuk subnet switch 1 dan 720 untuk subnet switch 3. ganti max lease time menjadi 7200


![image](https://user-images.githubusercontent.com/81372291/141606473-5e268331-273a-452c-880c-9223ac7945e8.png)


Dapat dicek dengan restart node client untuk melihat lease time


![image](https://user-images.githubusercontent.com/81372291/141606522-1c82297f-42f8-48f0-8282-ee75c9520e6f.png)

## No. 7
Skypie sebagai server untuk jual beli kapal yang dimilikinya dengan alamat IP yang tetap dengan IP [prefix IP].3.69

edit file `/etc/dhcp/dhcpd.conf` tambahkan

```
host Skypie {
    hardware ethernet 7a:a4:e3:4e:9f:d3;
    fixed-address 192.177.3.69;
}
```

![image](https://user-images.githubusercontent.com/81372291/141606303-10a645ed-829c-4c1d-9d53-c910a4cc9c59.png)


lalu buka node skypie dan edit file `/etc/network/interfaces` dan tambahkan baris `hwaddress ether 7a:a4:e3:4e:9f:d3`


hwaddress dapat dilihat dari link/ether pada eth0 saat menggunakan command `ip a`


![image](https://user-images.githubusercontent.com/81372291/141606385-638f1aa6-a65d-4e29-a369-2ddac4e3c90f.png)

## No. 8
Pada Loguetown, proxy harus bisa diakses dengan nama jualbelikapal.yyy.com dengan port yang digunakan adalah 5000

#### Water7

- Pastikan telah menginstall squid dan apache2 menggunakan :
` apt-get install squid -y
apt-get install apache2 -y`

- Lakukan backup file config squid
`mv /etc/squid/squid.conf /etc/squid/squid.conf.bak`

- Tambahkan teks di bawah ke file `squid.conf` yang kosong
`http_port 5000
visible_hostname jualbelikapal.b01.com

http_access allow all`

- Restart squid
`service squid restart`


#### Loguetown

- Install Lynx
`apt-get install lynx -y`

- Aktifkan proxy dengan port 5000
`export http_proxy="http://192.177.2.3:5000"`

- Gunakan lynx untuk membuka `its.ac.id`
![image](https://user-images.githubusercontent.com/90582800/141645710-5b6338ba-7d45-457c-b987-6b3d232b143a.png)

## No. 9

Agar transaksi jual beli lebih aman dan pengguna website ada dua orang, proxy dipasang autentikasi user proxy dengan enkripsi MD5 dengan dua username, yaitu `luffybelikapalb01` dengan password `luffy_b01` dan `zorobelikapalb01` dengan password `zoro_b01`

#### Water7

- Lakukan perintah di bawah untuk membuat username dan password sesuai permintaan soal. Baris kedua tidak memerlukan -c karena file passwd sudah di create. -m digunakan untuk melakukan dekripsi menggunakan MD5
`htpasswd -c -b -m /etc/squid/passwd zorobelikapalb01 zoro_b01
htpasswd -b -m /etc/squid/passwd luffybelikapalb01 luffy_b01`

- Tambahkan perintah di bawah ke file `/etc/squid/squid.conf`
`auth_param basic program /usr/lib/squid/basic_ncsa_auth /etc/squid/passwd
auth_param basic children 5
auth_param basic realm Proxy
auth_param basic credentialsttl 2 hours
auth_param basic casesensitive on
acl USERS proxy_auth REQUIRED
http_access allow USERS`

- Restart Squid
`service squid restart`

#### Loguetown

- Gunakan lynx untuk membuka `its.ac.id`

![image](https://user-images.githubusercontent.com/90582800/141646252-98aa4293-8a89-4e2b-bd80-c4450de44ea1.png)
![image](https://user-images.githubusercontent.com/90582800/141646266-ed9b6d08-b7fa-4493-80ee-f74ac5d58288.png)

- Apabila Autentikasi berhasil
![image](https://user-images.githubusercontent.com/90582800/141645710-5b6338ba-7d45-457c-b987-6b3d232b143a.png)


## No. 10

Transaksi jual beli tidak dilakukan setiap hari, oleh karena itu akses internet dibatasi hanya dapat diakses setiap hari Senin-Kamis pukul 07.00-11.00 dan setiap hari Selasa-Jum’at pukul 17.00-03.00 keesokan harinya (sampai Sabtu pukul 03.00)

#### Water7

- Tambahkan perintah berikut pada `/etc/squid/acl.conf`
`acl AVAILABLE_WORKING_1 time MTWH 07:00-11:00
acl AVAILABLE_WORKING_2 time TWHF 17:00-23:59
acl AVAILABLE_WORKING_3 time WHFA 00:00-03:00`

- Variabel MTWHFAS merepresentasikan inisial tiap hari dalam bahasa inggris

- Tambahkan perintah berikut pada `/etc/squid/squid.conf`
`
include /etc/squid/acl.conf

http_access allow USERS AVAILABLE_WORKING_1
http_access allow USERS AVAILABLE_WORKING_2
http_access allow USERS AVAILABLE_WORKING_3
http_access deny USERS all`

- Restart Squid
`service squid restart`

#### Loguetown

- Gunakan lynx untuk membuka `its.ac.id`

- Apabila menggunakan setelan waktu yang sesuai, akan ditampilkan halaman seperti gambar di atas.

- Apabila waktu di set menggunakan perintah `date -s "13 NOV 2021 22:00:00"`, maka akan ditampilkan error sebagai berikut
![image](https://user-images.githubusercontent.com/90582800/141646296-7a3570f4-a40e-43c2-849e-2a9e6ad6924d.png)



## No.11
Agar transaksi bisa lebih fokus berjalan, maka dilakukan redirect website agar mudah mengingat website transaksi jual beli kapal. Setiap mengakses google.com, akan diredirect menuju `super.franky.yyy.com` dengan website yang sama pada soal shift modul 2. Web server `super.franky.yyy.com` berada pada node `Skypie`.
#### Skypie
- Pertama - tama, update library dari ubuntu dengan perintah `apt-get update`.
- Install `apache2 dan librarynya` dengan menjalankan `apt-get install apache2 -y` dan `apt-get install libapache2-mod-php7.0 -y`.
- Lalu Jalankan apache yang telah dipasang dengan memasukkan perintah `service apache2 start`.
- Install `php` dengan menjalankan perintah `apt-get install php -y`.
- Install tools `wget` dengan menjalankan perintah `apt-get install wget -y`.
- Install tools `unzip` dengan menjalankan perintah `apt-get install unzip -y`.
- Setelah sudah menginstal semua yang dibutuhkan, download file dari web yang telah disediakan dengan menjalankan perintah seperti berikut:
	```bash
	wget https://raw.githubusercontent.com/FeinardSlim/Praktikum-Modul-2-Jarkom/main/super.franky.zip
	```
- Lalu Copy konfigurasi 000-default web server yang akan digunakan sebagai template konfigurasi web server super.franky.c07.com dengan perintah :
	```bash
	cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/super.franky.b01.com.conf
	```
- Kemudian edit file Edit file `/etc/apache2/sites-available/super.franky.b01.com.conf` seperti gambar berikut :

![11 1](https://user-images.githubusercontent.com/55092974/141611958-8072b582-3ad2-4181-9c37-7fbb5a1f6f04.JPG)

- setelah itu buat folder `super.franky.b01.com` di direktori /var/www/ dengan perintah :
	```bash
	mkdir /var/www/super.franky.b01.com
	```
- Unzip file library web yang sebelumnya telah di download ke folder yang telah dibuat dengan perintah :
	```bash
	unzip -j super.franky.zip -d /var/www/super.franky.b01.com
	```

-   Jalankan konfigurasi website yang telah dibuat dengan menjalankan perintah :
    ```bash
    cd /etc/apache2/sites-available/
    a2ensite super.franky.b01.com.conf
    ````

-   Edit file `/etc/apache2/ports.conf` seperti pada gambar berikut :

![11 2](https://user-images.githubusercontent.com/55092974/141611997-7400ccd5-c0a8-44c1-a809-311aa06c14f3.JPG)


-   Restart service apache dengan perintah :
	```bash
	service apache2 restart
	```
  #### EniesLobby
  - Pertama-tama edit file /etc/bind/named.conf.local dan tambahkan perintah seperti berikut :
  	```bash
	zone "super.franky.c07.com" {
 	type master;
 	file "/etc/bind/jarkom/super.franky.c07.com";
	};
	```

![11 3](https://user-images.githubusercontent.com/55092974/141612102-9fb6409b-af03-4391-b358-b7b048113485.JPG)

- Lalu edit file `/etc/bind/kaizoku/super.franky.b01.com` seperti gambar berikut :

![11 4](https://user-images.githubusercontent.com/55092974/141612933-4d2e2da3-6a68-4f8a-89d1-71530d7a3b01.JPG)

- Kemudian restart bind9 dengan perintah `service bind9 restart`

#### Water7
- Pertama edit file Edit file `/etc/squid/squid.conf` dan tambahkan berikut :
```bash
dns_nameservers 192.187.2.2 192.168.122.1

acl WHEN_ACCESS dstdomain google.com
deny_info http://super.franky.b01.com:5000 WHEN_ACCESS
http_reply_access deny WHEN_ACCESS
```
![11 5](https://user-images.githubusercontent.com/55092974/141613048-14d6175e-b914-4052-b720-2443ca892234.JPG)

- Lalu restart squid dengan perintah `service squid restart`

#### Loguetown
- Export terlebih dahulu dengan perintah `export http_proxy="http://jualbelikapal.b01.com:5000"`
- lalu buka `lynx google.com`, jika username dan password benar / valid akan muncul gambar seperti berikut :

![11 6](https://user-images.githubusercontent.com/55092974/141613133-bb0044e0-a941-4b21-b00c-ad8dfd58d4db.JPG)


