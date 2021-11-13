# Jarkom-Modul-3-B01-2021

Kelompok B01

|      NRP       |                  Nama                   |
| :------------: | :-------------------------------------: |
| 05111940000120 |       Jonathan Timothy Siregar          |
| 05111940000195 |    Muhammad Fikri Sandi Pratama         |
| 05111940000150 |         Mohammad Thoriq Huda            |

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


