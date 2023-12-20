# **Lapres Praktikum Jarkom Modul 5 Kelompok D15**

### **Anggota Kelompok**

| **Nama**                  | **NRP**    |
| ------------------------- | ---------- |
| Rayhan Arvianta Bayuputra | 5025211217 |
| Yehezkiel Wiradhika       | 5025201086 |

## Daftar Isi

- [Prerequisites](#Prerequisites)
  - [Setup Topologi](#Setup-Topologi)
  - [Subnetting dengan VLSM](#Subnetting-dengan-VLSM)
  - [Routing](#Routing)
  - [Setup DHCP](#Setup-DHCP)
- [Soal 1](#Soal-1)
- [Soal 2](#Soal-2)
- [Soal 3](#Soal-3)
- [Soal 4](#Soal-4)
- [Soal 5](#Soal-5)
- [Soal 6](#Soal-6)
- [Soal 7](#Soal-7)
- [Soal 8](#Soal-8)
- [Soal 9](#Soal-9)
- [Soal 10](#Soal-10)

## Prerequisites

Berikut adalah hal yang harus disiapkan sebelum terjun ke soal-soal

### Setup Topologi

(foto topologi di gns3, tolong masukin mas gns3 ku dead)

Dengan keterangan

- Richter adalah DNS Server
- Revolte adalah DHCP Server
- Sein dan Stark adalah Web Server
- Jumlah Host pada SchwerMountain adalah 64
- Jumlah Host pada LaubHills adalah 255
- Jumlah Host pada TurkRegion adalah 1022
- Jumlah Host pada GrobeForest adalah 512

### Subnetting dengan VLSM

Sheet subnetting dapat diakses lewat link <a href="https://docs.google.com/spreadsheets/d/16ojlqLXfdEohJvJMFsAyd2HqNdLfOEMsJbRg7eYrSY4/edit?usp=sharing">berikut</a>.

Berdasarkan informasi dari soal, dapat dihitung jumlah IP yang dibutuhkan oleh setiap subnet, beserta length yang optimal untuk jumlah IP yang terkait.

| Nama Subnet | Rute                                               | Jumlah IP | Netmask |
| ----------- | -------------------------------------------------- | --------- | ------- |
| A1          | Fern - Switch2 - Revolte                           | 2         | /30     |
| A2          | Fern - Richter                                     | 2         | /30     |
| A3          | Himmel - Switch3 - SchwerMountain - Switch3 - Fern | 66        | /25     |
| A4          | Himmel - LaubHills                                 | 256       | /23     |
| A5          | Frieren - Himmel                                   | 2         | /30     |
| A6          | Frieren - Stark                                    | 2         | /30     |
| A7          | Aura - Frieren                                     | 2         | /30     |
| A8          | Aura - Heiter                                      | 2         | /30     |
| A9          | Heiter - TurkRegion                                | 1023      | /21     |
| A10         | Heiter - Switch1 - Sein - Switch1 - GrobeForest    | 514       | /22     |
| Total       |                                                    | 1871      | /21     |

Dan dari informasi pada tabel di atas, dapat dibentuk VLSM tree seperti berikut.

![vlsm-tree](./assets/vlsm-tree-prak5.png)

Hasil subnetting dari VLSM tree di atas dapat dipetakan pada tabel berikut.

| Subnet | Network ID   | Netmask         | Broadcast    |
| ------ | ------------ | --------------- | ------------ |
| A1     | 10.29.14.128 | 255.255.255.252 | 10.29.14.131 |
| A2     | 10.29.14.132 | 255.255.255.252 | 10.29.14.135 |
| A3     | 10.29.14.0   | 255.255.255.128 | 10.29.14.127 |
| A4     | 10.29.12.0   | 255.255.254.0   | 10.29.13.255 |
| A5     | 10.29.14.136 | 255.255.255.252 | 10.29.14.139 |
| A6     | 10.29.14.140 | 255.255.255.252 | 10.29.14.143 |
| A7     | 10.29.14.144 | 255.255.255.252 | 10.29.14.147 |
| A8     | 10.29.14.148 | 255.255.255.252 | 10.29.14.151 |
| A9     | 10.29.0.0    | 255.255.248.0   | 10.29.7.255  |
| A10    | 10.29.8.0    | 255.255.252.0   | 10.29.11.255 |

Setelah mendapatkan informasi mengenai NID dan BID serta netmask dari setiap subnet, berikut adalah pembagian IP dari setiap node yang ada

| No  | Nama Node      | Interface | IP Address               | Jumlah Host | Network ID   | Netmask         | Broadcast ID | Nama Subnet |
| --- | -------------- | --------- | ------------------------ | ----------- | ------------ | --------------- | ------------ | ----------- |
| 1   | Fern           | eth2      | 10.29.14.129             | 2           | 10.29.14.128 | 255.255.255.252 | 10.29.14.131 | A1          |
| 2   | Revolte        | eth0      | 10.29.14.130             | 2           | 10.29.14.128 | 255.255.255.252 | 10.29.14.131 | A1          |
| 3   | Fern           | eth1      | 10.29.14.133             | 2           | 10.29.14.132 | 255.255.255.252 | 10.29.14.135 | A2          |
| 4   | Richter        | eth0      | 10.29.14.134             | 2           | 10.29.14.132 | 255.255.255.252 | 10.29.14.135 | A2          |
| 5   | Himmel         | eth2      | 10.29.14.1               | 126         | 10.29.14.0   | 255.255.255.128 | 10.29.14.127 | A3          |
| 6   | SchwerMountain | eth0      | 10.29.14.3 - 10.29.14.66 | 126         | 10.29.14.0   | 255.255.255.128 | 10.29.14.127 | A3          |
| 7   | Fern           | eth0      | 10.29.14.2               | 126         | 10.29.14.0   | 255.255.255.128 | 10.29.14.127 | A3          |
| 8   | Himmel         | eth1      | 10.29.12.1               | 510         | 10.29.12.0   | 255.255.254.0   | 10.29.13.255 | A4          |
| 9   | LaubHills      | eth0      | 10.29.12.2 - 10.29.13.0  | 510         | 10.29.12.0   | 255.255.254.0   | 10.29.13.255 | A4          |
| 10  | Frieren        | eth2      | 10.29.14.137             | 2           | 10.29.14.136 | 255.255.255.252 | 10.29.14.139 | A5          |
| 11  | Himmel         | eth0      | 10.29.14.138             | 2           | 10.29.14.136 | 255.255.255.252 | 10.29.14.139 | A5          |
| 12  | Frieren        | eth1      | 10.29.14.141             | 2           | 10.29.14.140 | 255.255.255.252 | 10.29.14.143 | A6          |
| 13  | Stark          | eth0      | 10.29.14.142             | 2           | 10.29.14.140 | 255.255.255.252 | 10.29.14.143 | A6          |
| 14  | Aura           | eth2      | 10.29.14.145             | 2           | 10.29.14.144 | 255.255.255.252 | 10.29.14.147 | A7          |
| 15  | Frieren        | eth0      | 10.29.14.146             | 2           | 10.29.14.144 | 255.255.255.252 | 10.29.14.147 | A7          |
| 16  | Aura           | eth1      | 10.29.14.149             | 2           | 10.29.14.148 | 255.255.255.252 | 10.29.14.151 | A8          |
| 17  | Heiter         | eth0      | 10.29.14.150             | 2           | 10.29.14.148 | 255.255.255.252 | 10.29.14.151 | A8          |
| 18  | Heiter         | eth1      | 10.29.0.1                | 2046        | 10.29.0.0    | 255.255.248.0   | 10.29.7.255  | A9          |
| 19  | TurkRegion     | eth0      | 10.29.0.2 - 10.29.4.0    | 2046        | 10.29.0.0    | 255.255.248.0   | 10.29.7.255  | A9          |
| 20  | Heiter         | eth2      | 10.29.8.1                | 1022        | 10.29.8.0    | 255.255.252.0   | 10.29.11.255 | A10         |
| 21  | Sein           | eth0      | 10.29.8.2                | 1022        | 10.29.8.0    | 255.255.252.0   | 10.29.11.255 | A10         |
| 22  | GrobeForest    | eth0      | 10.29.8.3 - 10.29.10.3   | 1022        | 10.29.8.0    | 255.255.252.0   | 10.29.11.255 | A10         |

### Routing

Berikut adalah syntax routing pada setiap router yang ada pada topologi.

> Fern

```
route add -net 0.0.0.0 netmask 0.0.0.0 gw 10.29.14.1
```

> Himmel

```
route add -net 0.0.0.0 netmask 0.0.0.0 gw 10.29.14.137
route add -net 10.29.14.128 netmask 255.255.255.252 gw 10.29.14.2
route add -net 10.29.14.132 netmask 255.255.255.252 gw 10.29.14.2
```

> Frieren

```
route add -net 0.0.0.0 netmask 0.0.0.0 gw 10.29.14.145
route add -net 10.29.14.128 netmask 255.255.255.252 gw 10.29.14.138
route add -net 10.29.14.132 netmask 255.255.255.252 gw 10.29.14.138
route add -net 10.29.14.0 netmask 255.255.255.128 gw 10.29.14.138
route add -net 10.29.12.0 netmask 255.255.254.0 gw 10.29.14.138
```

> Aura

```
route add -net 10.29.14.128 netmask 255.255.255.252 gw 10.29.14.146
route add -net 10.29.14.132 netmask 255.255.255.252 gw 10.29.14.146
route add -net 10.29.14.0 netmask 255.255.255.128 gw 10.29.14.146
route add -net 10.29.12.0 netmask 255.255.254.0 gw 10.29.14.146
route add -net 10.29.14.136 netmask 255.255.255.252 gw 10.29.14.146
route add -net 10.29.14.140 netmask 255.255.255.252 gw 10.29.14.146
route add -net 10.29.0.0 netmask 255.255.248.0 gw 10.29.14.150
route add -net 10.29.8.0 netmask 255.255.252.0 gw 10.29.14.150
```

> Heiter

```
route add -net 0.0.0.0 netmask 0.0.0.0 gw 10.29.14.149
```

### Setup DHCP

```
subnet 10.29.14.128 netmask 255.255.255.252 {
}

subnet 10.29.14.132 netmask 255.255.255.252 {
}

subnet 10.29.14.0 netmask 255.255.255.128 {
	range 10.29.14.2 10.29.14.126;
    option routers 10.29.14.2;
    option broadcast-address 10.29.14.127;
    option domain-name-servers 192.168.122.1;
    default-lease-time 600;
    max-lease-time 7200;
}

subnet 10.29.12.0 netmask 255.255.254.0 {
	range 10.29.12.2 10.29.13.254;
    option routers 10.29.12.1;
    option broadcast-address 10.29.13.255;
    option domain-name-servers 192.168.122.1;
    default-lease-time 600;
    max-lease-time 7200;
}

subnet 10.29.14.136 netmask 255.255.255.252 {}

subnet 10.29.14.140 netmask 255.255.255.252 {}

subnet 10.29.14.144 netmask 255.255.255.252 {}

subnet 10.29.14.148 netmask 255.255.255.252 {}

subnet 10.29.0.0 netmask 255.255.248.0 {
	range 10.29.0.2 10.29.7.254;
    option routers 10.29.0.1;
    option broadcast-address 10.29.7.255;
    option domain-name-servers 192.168.122.1;
    default-lease-time 600;
    max-lease-time 7200;
}

subnet 10.29.8.0 netmask 255.255.252.0 {
	range 10.29.8.2 10.29.11.254;
    option routers 10.29.8.1;
    option broadcast-address 10.29.11.255;
    option domain-name-servers 192.168.122.1;
    default-lease-time 600;
    max-lease-time 7200;
}
```

## Soal 1

Agar topologi yang kalian buat dapat mengakses keluar, kalian diminta untuk mengkonfigurasi Aura menggunakan iptables, tetapi tidak ingin menggunakan MASQUERADE.

```
iptables -t nat -A POSTROUTING -o eth0 -j SNAT --to-source 192.168.122.2
```

## Soal 2

Kalian diminta untuk melakukan drop semua TCP dan UDP kecuali port 8080 pada TCP.

```
iptables -A INPUT -p tcp --dport 8080 -j ACCEPT
iptables -A INPUT -p tcp --dport 0:8079 -j DROP
iptables -A INPUT -p tcp --dport 8081:65535 -j DROP
iptables -A INPUT -p udp -j DROP
```

## Soal 3

Kepala Suku North Area meminta kalian untuk membatasi DHCP dan DNS Server hanya dapat dilakukan ping oleh maksimal 3 device secara bersamaan, selebihnya akan di drop.

```
# Izinkan koneksi yang sudah ada dan echo request ICMP (ping) yang masuk
iptables -A INPUT -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
iptables -A INPUT -p icmp --icmp-type echo-request -m hashlimit --hashlimit 3/s --hashlimit-burst 3 --hashlimit-mode srcip --hashlimit-name icmp_limited -j ACCEPT

# Hilangkan request echo ICMP tambahan
iptables -A INPUT -p icmp --icmp-type echo-request -j DROP
```

## Soal 4

Lakukan pembatasan sehingga koneksi SSH pada Web Server hanya dapat dilakukan oleh masyarakat yang berada pada GrobeForest.

```
# Izinkan koneksi SSH dari rentang IP yang ditentukan
iptables -A INPUT -p tcp --dport 22 -i eth0 -m iprange --src-range 10.29.8.2-10.29.11.254 -j ACCEPT

# Putuskan koneksi SSH dari alamat IP lain
iptables -A INPUT -p tcp --dport 22 -i eth0 -j DROP
```

Testing:
<br />
nc 10.29.14.142 22

## Soal 5

Selain itu, akses menuju WebServer hanya diperbolehkan saat jam kerja yaitu Senin-Jumat pada pukul 08.00-16.00.

```
# Izinkan koneksi yang sudah ada dan traffic masuk yang terkait dengan koneksi yang sudah ada
iptables -A INPUT -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT

# Izinkan traffic masuk pada hari kerja (Senin-Jumat) mulai pukul 08:00 hingga 16:00
iptables -A INPUT -m time --timestart 08:00 --timestop 16:00 --weekdays Mon -j ACCEPT
iptables -A INPUT -m time --timestart 08:00 --timestop 16:00 --weekdays Tue -j ACCEPT
iptables -A INPUT -m time --timestart 08:00 --timestop 16:00 --weekdays Wed -j ACCEPT
iptables -A INPUT -m time --timestart 08:00 --timestop 16:00 --weekdays Thu -j ACCEPT
iptables -A INPUT -m time --timestart 08:00 --timestop 16:00 --weekdays Fri -j ACCEPT

# Drop semua traffic masuk lainnya
iptables -A INPUT -j DROP
```

## Soal 6

Lalu, karena ternyata terdapat beberapa waktu di mana network administrator dari WebServer tidak bisa stand by, sehingga perlu ditambahkan rule bahwa akses pada hari Senin - Kamis pada jam 12.00 - 13.00 dilarang (istirahat maksi cuy) dan akses di hari Jumat pada jam 11.00 - 13.00 juga dilarang (maklum, Jumatan rek).

```
# Izinkan semua traffic
iptables -A INPUT -j ACCEPT

# Izinkan alamat IP tertentu pada jangka waktu tertentu
# Senin hingga Kamis from 12:00 to 13:00
iptables -A INPUT -p tcp -s 10.29.14.142 -m time --timestart 12:00 --timestop 13:00 --weekdays Mon -j ACCEPT
iptables -A INPUT -p tcp -s 10.29.14.142 -m time --timestart 12:00 --timestop 13:00 --weekdays Tue -j ACCEPT
iptables -A INPUT -p tcp -s 10.29.14.142 -m time --timestart 12:00 --timestop 13:00 --weekdays Wed -j ACCEPT
iptables -A INPUT -p tcp -s 10.29.14.142 -m time --timestart 12:00 --timestop 13:00 --weekdays Thu -j ACCEPT

iptables -A INPUT -p tcp -s 10.29.8.2 -m time --timestart 12:00 --timestop 13:00 --weekdays Mon -j ACCEPT
iptables -A INPUT -p tcp -s 10.29.8.2 -m time --timestart 12:00 --timestop 13:00 --weekdays Tue -j ACCEPT
iptables -A INPUT -p tcp -s 10.29.8.2 -m time --timestart 12:00 --timestop 13:00 --weekdays Wed -j ACCEPT
iptables -A INPUT -p tcp -s 10.29.8.2 -m time --timestart 12:00 --timestop 13:00 --weekdays Thu -j ACCEPT

# Jumat dari 11:00 hingga 13:00
iptables -A INPUT -p tcp -s 10.29.14.142 -m time --timestart 11:00 --timestop 13:00 --weekdays Fri -j ACCEPT
iptables -A INPUT -p tcp -s 10.29.8.2 -m time --timestart 11:00 --timestop 13:00 --weekdays Fri -j ACCEPT

# Drop traffic lainnya
iptables -A INPUT -j DROP
```

## Soal 7

Karena terdapat 2 WebServer, kalian diminta agar setiap client yang mengakses Sein dengan Port 80 akan didistribusikan secara bergantian pada Sein dan Stark secara berurutan dan request dari client yang mengakses Stark dengan port 443 akan didistribusikan secara bergantian pada Sein dan Stark secara berurutan.

## Soal 8

Karena berbeda koalisi politik, maka subnet dengan masyarakat yang berada pada Revolte dilarang keras mengakses WebServer hingga masa pencoblosan pemilu kepala suku 2024 berakhir. Masa pemilu (hingga pemungutan dan penghitungan suara selesai) kepala suku bersamaan dengan masa pemilu Presiden dan Wakil Presiden Indonesia 2024.

Masa pemilu berakhir (termasuk penghitungan suara): 20 Maret 2024

Perintah iptables yang membatasi akses ke 10.29.14.142 dan 10.29.8.2 dari 10.29.14.129/30 hingga 20 Maret 2024:

```
# Secara default mengizinkan semua akses
iptables -A INPUT -j ACCEPT

# Izinkan traffic dari IP tertentu kecuali rentang yang ditentukan dan setelah 20 Maret 2024
iptables -A INPUT -s 10.29.14.142 -j ACCEPT
iptables -A INPUT -s 10.29.8.2 -j ACCEPT
iptables -A INPUT -s 10.29.14.129/30 -m time --datestop 2024-03-20 -j DROP
iptables -A INPUT -s 10.29.14.129/30 -j ACCEPT

# Drop semua traffic lain
iptables -A INPUT -j DROP
```

## Soal 9

Sadar akan adanya potensial saling serang antar kubu politik, maka WebServer harus dapat secara otomatis memblokir alamat IP yang melakukan scanning port dalam jumlah banyak (maksimal 20 scan port) di dalam selang waktu 10 menit. (clue: test dengan nmap)

```
# Buat chain untuk melacak potensi pemindaian port
iptables -N PORTSCAN
iptables -A PORTSCAN -m recent --name PORTSCAN --set -j ACCEPT

# Check incoming TCP packets, track port scanning (assuming port 80 for illustration)
iptables -A INPUT -p tcp --dport 80 -m recent --name PORTSCAN --rcheck --seconds 600 --hitcount 20 -j LOG --log-prefix "Portscan detected: "
iptables -A INPUT -p tcp --dport 80 -m recent --name PORTSCAN --rcheck --seconds 600 --hitcount 20 -j DROP

# Allow legitimate traffic
iptables -A INPUT -p tcp --dport 80 -j ACCEPT
```

## Soal 10

Karena kepala suku ingin tau paket apa saja yang di-drop, maka di setiap node server dan router ditambahkan logging paket yang di-drop dengan standard syslog level.

```
# Log paket yang didrop ke syslog
iptables -A INPUT -j LOG --log-prefix "Paket yang didrop: " --log-level 6
iptables -A OUTPUT -j LOG --log-prefix "Paket yang didrop: " --log-level 6
iptables -A FORWARD -j LOG --log-prefix "Paket yang didrop: " --log-level 6
```
