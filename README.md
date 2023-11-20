# Jarkom-Modul-3-D24-2023
Laporan Praktikum Kelas Jaringan Komputer 2024
Kelompok D24.

## Author
<ul>
  <li>Daffa Saskara - 5025211249</li>
  <li>Arundaya Pratama Nurhasan - 5025221205</li>
</ul>

# Laporan Revisi
## No. 1
<img src="assets/konfig.jpg" alt="">
Setelah membuat topologi seperti pada gambar diatas, selanjutnya kita perlu menyeting konfigurasi untuk setup awal pada setiap node sebagai berikut.

- Aura (DHCP Relay)
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 192.203.1.0
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 192.203.2.0
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 192.203.3.0
	netmask 255.255.255.0

auto eth4
iface eth4 inet static
	address 192.203.4.0
	netmask 255.255.255.0

```
- Himmel (DHCP Server)
```
auto eth0
iface eth0 inet static
	address 192.203.1.1
	netmask 255.255.255.0
	gateway 192.173.1.0

```
- Heiter (DNS Server)
```
auto eth0
iface eth0 inet static
	address 192.203.1.2
	netmask 255.255.255.0
	gateway 192.203.1.0
	up echo nameserver 192.168.122.1 > /etc/resolv.conf
```
- Denken (Database Server)
```
auto eth0
iface eth0 inet static
	address 192.203.2.1
	netmask 255.255.255.0
	gateway 192.203.2.0
	up echo nameserver 192.203.1.2 > /etc/resolv.conf
```
- Eisen (Load Balancer)
```
auto eth0
iface eth0 inet static
	address 192.203.2.2
	netmask 255.255.255.0
	gateway 192.203.2.0
	up echo nameserver 192.203.1.2 > /etc/resolv.conf
```
- Frieren (Laravel Worker)
```
auto eth0
iface eth0 inet static
	address 192.203.4.3
	netmask 255.255.255.0
	gateway 192.203.4.0
	up echo nameserver 192.203.1.2 > /etc/resolv.conf
```
- Flamme (Laravel Worker)
```
auto eth0
iface eth0 inet static
	address 192.203.4.2
	netmask 255.255.255.0
	gateway 192.203.4.0
	up echo nameserver 192.203.1.2 > /etc/resolv.conf
```
- Fern (Laravel Worker)
```
auto eth0
iface eth0 inet static
	address 192.203.4.1
	netmask 255.255.255.0
	gateway 192.203.4.0
	up echo nameserver 192.203.1.2 > /etc/resolv.conf
```
- Lawine (PHP Worker)
```
auto eth0
iface eth0 inet static
	address 192.203.3.3
	netmask 255.255.255.0
	gateway 192.203.3.0
	up echo nameserver 192.203.1.2 > /etc/resolv.conf
```
- Linie (PHP Worker)
```
auto eth0
iface eth0 inet static
	address 192.203.3.2
	netmask 255.255.255.0
	gateway 192.203.3.0
	up echo nameserver 192.203.1.2 > /etc/resolv.conf
```
- Lugner (PHP Worker)
```
auto eth0
iface eth0 inet static
	address 192.203.3.1
	netmask 255.255.255.0
	gateway 192.203.3.0
	up echo nameserver 192.203.1.2 > /etc/resolv.conf
```
- Richter (Client)
```
auto eth0
iface eth0 inet static
	address 192.203.3.4
	netmask 255.255.255.0
	gateway 192.203.3.0
	up echo nameserver 192.203.1.2 > /etc/resolv.conf
```
- Revolte (Client)
```
auto eth0
iface eth0 inet static
	address 192.203.3.5
	netmask 255.255.255.0
	gateway 192.203.3.0
	up echo nameserver 192.203.1.2 > /etc/resolv.conf
```
- Stark (Client)
```
auto eth0
iface eth0 inet static
	address 192.203.4.4
	netmask 255.255.255.0
	gateway 192.293.4.0
	up echo nameserver 192.203.1.2 > /etc/resolv.conf
```
- Sein (Client)
```
auto eth0
iface eth0 inet static
	address 192.203.4.5
	netmask 255.255.255.0
	gateway 192.203.4.0
	up echo nameserver 192.203.1.2 > /etc/resolv.conf
```
Kemudian kita mengubah file `.bashrc` dengan memasukkan perintah `nano .bashrc` pada node `DNS Relay` dan menambahkan baris berikut.
  ```sh
  iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.203.0.0/16

  apt-get update
  apt install isc-dhcp-relay -y
  ```
selanjutnya melakukan setup untuk node `DNS Server` sebagai berikut
  ```sh
  apt-get update
  apt-get install bind9 -y

  echo 'zone "riegel.canyon.d24.com" {
    type master;
    file "/etc/bind/sites/riegel.canyon.d24.com";
  };

  zone "granz.channel.d24.com" {
    type master;
    file "/etc/bind/sites/granz.channel.d24.com";
  };

  zone "1.203.192.in-addr.arpa" {
    type master;
    file "/etc/bind/sites/1.203.192.in-addr.arpa";
  };' > /etc/bind/named.conf.local

  mkdir -p /etc/bind/sites
  cp /etc/bind/db.local /etc/bind/sites/riegel.canyon.d24.com
  cp /etc/bind/db.local /etc/bind/sites/granz.channel.d24.com
  cp /etc/bind/db.local /etc/bind/sites/1.203.192.in-addr.arpa

  echo ';
  ; BIND data file for local loopback interface
  ;
  $TTL    604800
  @       IN      SOA     riegel.canyon.d24.com. root.riegel.canyon.d24.com. (
                          2023111601      ; Serial
                           604800         ; Refresh
                            86400         ; Retry
                          2419200         ; Expire
                           604800 )       ; Negative Cache TTL
  ;
  @       IN      NS      riegel.canyon.d24.com.
  @       IN      A       192.203.4.1     ; IP Fern
  www     IN      CNAME   riegel.canyon.d24.com.' > /etc/bind/sites/riegel.canyon.d24.com

  echo '
  ; BIND data file for local loopback interface
  ;
  $TTL    604800
  @       IN      SOA     granz.channel.d24.com. root.granz.channel.d24.com. (
                          2023111601      ; Serial
                           604800         ; Refresh
                            86400         ; Retry
                          2419200         ; Expire
                           604800 )       ; Negative Cache TTL
  ;
  @       IN      NS      granz.channel.d24.com.
  @       IN      A       192.203.3.1     ; IP Lugner
  www     IN      CNAME   granz.channel.d24.com.' > /etc/bind/sites/granz.channel.d24.com

  echo 'options {
        directory "/var/cache/bind";

        forwarders {
                192.168.122.1;
        };

        // dnssec-validation auto;
        allow-query{any;};
        auth-nxdomain no;
        listen-on-v6 { any; };
  }; ' >/etc/bind/named.conf.options

  service bind9 start
  ```
kita bisa melihat hasilnya dengan melakukan perintah `ping riegel.canyon.d24.com` dan `ping granz.channel.d24.com` pada salah satu node `client`, kali ini kita meenggunakan node `Richter`. Pastikan node Lugner dan Fern telah berjalan.

