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

| Nama Subnet                       | Rute                                          | Jumlah IP | Netmask |
|-----------------------------------|-----------------------------------------------|------------|---------|
| A1                                | Fern - Switch2 - Revolte                      | 2          | /30     |
| A2                                | Fern - Richter                                | 2          | /30     |
| A3                                | Himmel - Switch3 - SchwerMountain - Switch3 - Fern | 66         | /25     |
| A4                                | Himmel - LaubHills                            | 256        | /23     |
| A5                                | Frieren - Himmel                              | 2          | /30     |
| A6                                | Frieren - Stark                               | 2          | /30     |
| A7                                | Aura - Frieren                                | 2          | /30     |
| A8                                | Aura - Heiter                                 | 2          | /30     |
| A9                                | Heiter - TurkRegion                           | 1023       | /21     |
| A10                               | Heiter - Switch1 - Sein - Switch1 - GrobeForest | 514        | /22     |
| Total                             |                                               | 1871       | /21     |

Dan dari informasi pada tabel di atas, dapat dibentuk VLSM tree seperti berikut.

![vlsm-tree](./assets/vlsm-tree-prak5.png)

Hasil subnetting dari VLSM tree di atas dapat dipetakan pada tabel berikut.

| Subnet | Network ID      | Netmask            | Broadcast        |
|--------|------------------|--------------------|------------------|
| A1     | 10.29.14.128    | 255.255.255.252    | 10.29.14.131     |
| A2     | 10.29.14.132    | 255.255.255.252    | 10.29.14.135     |
| A3     | 10.29.14.0      | 255.255.255.128    | 10.29.14.127     |
| A4     | 10.29.12.0      | 255.255.254.0      | 10.29.13.255     |
| A5     | 10.29.14.136    | 255.255.255.252    | 10.29.14.139     |
| A6     | 10.29.14.140    | 255.255.255.252    | 10.29.14.143     |
| A7     | 10.29.14.144    | 255.255.255.252    | 10.29.14.147     |
| A8     | 10.29.14.148    | 255.255.255.252    | 10.29.14.151     |
| A9     | 10.29.0.0       | 255.255.248.0      | 10.29.7.255      |
| A10    | 10.29.8.0       | 255.255.252.0      | 10.29.11.255     |

Setelah mendapatkan informasi mengenai NID dan BID serta netmask dari setiap subnet, berikut adalah pembagian IP dari setiap node yang ada

| No  | Nama Node        | Interface | IP Address        | Jumlah Host | Network ID       | Netmask            | Broadcast ID       | Nama Subnet |
|-----|------------------|-----------|-------------------|-------------|------------------|--------------------|--------------------|-------------|
| 1   | Fern             | eth2      | 10.29.14.129      | 2           | 10.29.14.128     | 255.255.255.252    | 10.29.14.131       | A1          |
| 2   | Revolte          | eth0      | 10.29.14.130      | 2           | 10.29.14.128     | 255.255.255.252    | 10.29.14.131       | A1          |
| 3   | Fern             | eth1      | 10.29.14.133      | 2           | 10.29.14.132     | 255.255.255.252    | 10.29.14.135       | A2          |
| 4   | Richter          | eth0      | 10.29.14.134      | 2           | 10.29.14.132     | 255.255.255.252    | 10.29.14.135       | A2          |
| 5   | Himmel           | eth2      | 10.29.14.1        | 126         | 10.29.14.0       | 255.255.255.128    | 10.29.14.127       | A3          |
| 6   | SchwerMountain   | eth0      | 10.29.14.3 - 10.29.14.66 | 126   | 10.29.14.0       | 255.255.255.128    | 10.29.14.127       | A3          |
| 7   | Fern             | eth0      | 10.29.14.2        | 126         | 10.29.14.0       | 255.255.255.128    | 10.29.14.127       | A3          |
| 8   | Himmel           | eth1      | 10.29.12.1        | 510         | 10.29.12.0       | 255.255.254.0      | 10.29.13.255       | A4          |
| 9   | LaubHills        | eth0      | 10.29.12.2 - 10.29.13.0 | 510 | 10.29.12.0       | 255.255.254.0      | 10.29.13.255       | A4          |
| 10  | Frieren          | eth2      | 10.29.14.137      | 2           | 10.29.14.136     | 255.255.255.252    | 10.29.14.139       | A5          |
| 11  | Himmel           | eth0      | 10.29.14.138      | 2           | 10.29.14.136     | 255.255.255.252    | 10.29.14.139       | A5          |
| 12  | Frieren          | eth1      | 10.29.14.141      | 2           | 10.29.14.140     | 255.255.255.252    | 10.29.14.143       | A6          |
| 13  | Stark            | eth0      | 10.29.14.142      | 2           | 10.29.14.140     | 255.255.255.252    | 10.29.14.143       | A6          |
| 14  | Aura             | eth2      | 10.29.14.145      | 2           | 10.29.14.144     | 255.255.255.252    | 10.29.14.147       | A7          |
| 15  | Frieren          | eth0      | 10.29.14.146      | 2           | 10.29.14.144     | 255.255.255.252    | 10.29.14.147       | A7          |
| 16  | Aura             | eth1      | 10.29.14.149      | 2           | 10.29.14.148     | 255.255.255.252    | 10.29.14.151       | A8          |
| 17  | Heiter           | eth0      | 10.29.14.150      | 2           | 10.29.14.148     | 255.255.255.252    | 10.29.14.151       | A8          |
| 18  | Heiter           | eth1      | 10.29.0.1         | 2046        | 10.29.0.0        | 255.255.248.0      | 10.29.7.255        | A9          |
| 19  | TurkRegion       | eth0      | 10.29.0.2 - 10.29.4.0 | 2046     | 10.29.0.0        | 255.255.248.0      | 10.29.7.255        | A9          |
| 20  | Heiter           | eth2      | 10.29.8.1         | 1022        | 10.29.8.0        | 255.255.252.0      | 10.29.11.255       | A10         |
| 21  | Sein             | eth0      | 10.29.8.2         | 1022        | 10.29.8.0        | 255.255.252.0      | 10.29.11.255       | A10         |
| 22  | GrobeForest       | eth0      | 10.29.8.3 - 10.29.10.3 | 1022   | 10.29.8.0        | 255.255.252.0      | 10.29.11.255       | A10         |

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

## Soal 2

## Soal 3

## Soal 4

## Soal 5

## Soal 6

## Soal 7

## Soal 8

## Soal 9

## Soal 10