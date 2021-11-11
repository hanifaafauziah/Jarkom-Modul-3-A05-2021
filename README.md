# Jarkom-Modul-3-A05-2021

Kelompok A5 :
<li>05111840000138 - Gema Adi Perwira
<li>05111940000024 - Hanifa Fauziah
<li>05111940000111 - Evelyn Sierra


## Soal 1
Luffy bersama Zoro berencana membuat peta tersebut dengan kriteria EniesLobby sebagai DNS Server, Jipangu sebagai DHCP Server, Water7 sebagai Proxy Server (1)
### Jawaban : 
  <ul>
    <li> Membuat Topologi
      <ul>
        <li>Membuat Peta</li>
        <li>Melakukan konfigurasi pada <b>EniesLobby, Jipangu, dan Water 7</b></li>
      </ul>
    </li>
  </ul>
  
  ### Topologi
  ![image](https://user-images.githubusercontent.com/42856438/141307657-45224501-1c09-4e49-bc8d-d793b031f858.png)
  
  Konfigurasi pada **Foosha**

  ```
  auto eth0
  iface eth0 inet dhcp

  auto eth1
  iface eth1 inet static
  address 192.171.1.1
  netmask 255.255.255.0

  auto eth2
  iface eth2 inet static
  address 192.171.2.1
  netmask 255.255.255.0

  auto eth3
  iface eth3 inet static
  address 192.171.3.1
  netmask 255.255.255.0
  ```
  
  Konfigurasi pada **EniesLobby**
  
  ```
  auto eth0
  iface eth0 inet static
	address 192.171.2.2
	netmask 255.255.255.0
	gateway 192.171.2.1

  ```
    
  Konfigurasi pada **Water7**
  
  ```
  auto eth0
  iface eth0 inet static
	address 192.171.2.3
	netmask 255.255.255.0
	gateway 192.171.2.1
  ```
  
  Konfigurasi pada **Jipangu**
  
  ```
  auto eth0
  iface eth0 inet static
	address 192.171.2.4
	netmask 255.255.255.0
	gateway 192.171.2.1

  ```
  
  Melakukan `iptables` pada **Foosha**
  ```
  iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.171.0.0/16
  ```
  
  Pada **EniesLobby** sebagai **DNS Server** jalankan perintah berikut
  
  ```
  echo nameserver 192.168.122.1 > /etc/resolv.conf

  apt-get update

  apt-get install bind9 -y

  service bind9 start
  ```
  Pada **Jipangu** sebagai **DHCP Server**
  
  ```
  echo nameserver 192.168.122.1 > /etc/resolv.conf

  apt-get update -y

  apt-get install isc-dhcp-server -y

  dhcpd --version
  ```
  
  Mengubah isi dari file `/etc/default/isc-dhcp-server` menjadi
  ```
  INTERFACES="eth0"
  ```
  
  Pada **Water7** sebagai **Proxy Server**
  
  ```
  echo nameserver 192.168.122.1 > /etc/resolv.conf

  apt-get update

  apt-get install apt-utils

  apt-get update

  apt-get install squid

  service squid restart
  ```
  
  ### Cek ping google
  ![image](https://user-images.githubusercontent.com/42856438/141307882-4382fc45-727c-46f5-8a7a-905ed474dae7.png)

    
## Soal 2
Foosha sebagai DHCP Relay  
  ### Jawaban : 
  
   Melakukan instalasi DHCP Relay
  ```
  apt-get update

  apt-get install isc-dhcp-relay -y

  dhcrelay --version
  ```
  
  ### DHCP Relay Pada Foosha 
  ![image](https://user-images.githubusercontent.com/42856438/141309510-04869347-abca-46f1-a4f2-6f1ce13df911.png)
  
  ###

## Soal 3 dan 6 
Semua client yang ada HARUS menggunakan konfigurasi IP dari DHCP Server.
Client yang melalui Switch1 mendapatkan range IP dari [prefix IP].1.20 - [prefix IP].1.99 dan [prefix IP].1.150 - [prefix IP].1.169 (3). Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch1 selama 6 menit. Dengan waktu maksimal yang dialokasikan untuk peminjaman alamat IP selama 120 menit. (6)
### Jawaban : 
  
  #### Mengubah Konfigurasi pada Semua Client, yaitu **Alabasta**, **Loguetown**, serta **Tottoland** dan **Skypie** pada file `/etc/network/interfaces` (bisa juga diedit dari GNS3 nya langsung)
  ```
  auto eth0
  iface eth0 inet dhcp
  ```
  
  #### Client pada Switch1 yaitu  **Alabasta** dan **Loguetown**
  Pada **DHCP Server (Jipangu)** ubah `/etc/dhcp/dhcpd.conf`
  ```
  subnet 192.171.1.0 netmask 255.255.255.0 {
    range 192.171.1.20 192.171.1.99; # range 1
    range 192.171.1.150 192.171.1.169; # range 2
    option routers 192.171.1.1;
    option broadcast-address 192.171.1.255;
    option domain-name-servers 192.171.2.2; #IP EniesLobby
    default-lease-time 360; # Waktu selama 6 Menit
    max-lease-time 7200; # Waktu maksimal 120 Menit
  }
  ```
  
## Soal 4 dan 6
Client yang melalui Switch3 mendapatkan range IP dari [prefix IP].3.30 - [prefix IP].3.50 (4). Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch3 selama 12 menit. Dengan waktu maksimal yang dialokasikan untuk peminjaman alamat IP selama 120 menit. (6)
  ### Jawaban : 
  
  #### Client pada Switch2 yaitu  **Tottoland** dan **Skypie**
  Pada **DHCP Server (Jipangu)** ubah `/etc/dhcp/dhcpd.conf`
  ```
  subnet 192.171.3.0 netmask 255.255.255.0 {
      range 192.171.3.30 192.171.3.50;
      option routers 192.171.3.1;
      option broadcast-address 192.171.3.255; 
      option domain-name-servers 192.171.2.2; #IP EniesLobby
      default-lease-time 720; # Waktu selama 12 Menit
      max-lease-time 7200; # Waktu maksimal 120 Menit
  }
  ```
  
## Soal 5
Client mendapatkan DNS dari EniesLobby dan client dapat terhubung dengan internet melalui DNS tersebut. (5)
  ### Jawaban : 
  
  Jangan lupa tambahkan konfigurasi untuk Switch2 di `/etc/dhcp/dhcpd.conf`
  
  ```
  subnet 192.171.2.0 netmask 255.255.255.0 {
    option routers 192.171.2.1;
  }
  ```
  
  Jangan lupa juga melakukan DNS Forwarder dengan mengubah konfigurasi pada **EniesLobby** di file `/etc/bind/named.conf.options`
  
  ```
  options {
        directory \"/var/cache/bind\";

         forwarders {
                192.168.122.1; 
         };

        allow-query{any;};
        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
  };
  ```
  
  Lalu cek dimasing-masing Client
  
  Pada **Loguetown**
  
  ![image](https://user-images.githubusercontent.com/80946219/141328560-8d2f2366-529b-465d-ab25-d18e73067237.png)  
  ![image](https://user-images.githubusercontent.com/80946219/141328435-c8b9ddd1-7dec-4a37-b6e3-c6af80878aa7.png)
  
  Pada **Alabasta**
  
  ![image](https://user-images.githubusercontent.com/80946219/141328862-85124baa-7f10-4325-98fd-6f7436422d57.png)
  ![image](https://user-images.githubusercontent.com/80946219/141328948-a379ee3f-d2cc-438e-802d-32a9ee77acd9.png)

  Pada **Skypie**

  ![image](https://user-images.githubusercontent.com/80946219/141329621-bbb2a83c-ba12-4e77-bf8f-2b6988c88428.png)
  ![image](https://user-images.githubusercontent.com/80946219/141329706-bca92f95-10db-4982-9196-3746e1bbedb2.png)
  
  Pada **Tottoland**
  
  ![image](https://user-images.githubusercontent.com/80946219/141329320-ab37e6d1-5975-4931-9868-8e8f887afc70.png)
  ![image](https://user-images.githubusercontent.com/80946219/141329483-47f12243-ac70-42a3-8242-fe766b25eb94.png)

  
## Soal 7
  Luffy dan Zoro berencana menjadikan Skypie sebagai server untuk jual beli kapal yang dimilikinya dengan alamat IP yang tetap dengan IP [prefix IP].3.69 (7). 
  ### Jawaban : 
  
  Cek
  Menambahkan konfigurasi pada **Jipangu (DHCP Server)** pada `/etc/dhcp/dhcpd.conf`
  
  ```
  host Skypie {
    hardware ethernet 02:69:95:42:d7:26;
    fixed-address 192.171.3.69;
  }
  ```
  
  Mengubah interface pada **Skypie** pada file `/etc/network/interfaces`
  
  ```
  auto eth0
  iface eth0 inet dhcp
  hwaddress ether 02:69:95:42:d7:26
  ```
  
  Sehingga ketika di cek dibagian **Skypie**
  
  ![image](https://user-images.githubusercontent.com/80946219/141331335-7f3e931e-d555-46e2-97eb-5c8f9938b246.png)
  ![image](https://user-images.githubusercontent.com/80946219/141331165-834b907d-9f5d-4ca1-bd7f-b16b68f50d9d.png)
  
  
## Soal 8
  Loguetown digunakan sebagai client Proxy agar transaksi jual beli dapat terjamin keamanannya, juga untuk mencegah kebocoran data transaksi. Pada Loguetown, proxy harus bisa diakses dengan nama jualbelikapal.yyy.com dengan port yang digunakan adalah 5000 (8). 
  ### Jawaban : 
## Soal 9
  Agar transaksi jual beli lebih aman dan pengguna website ada dua orang, proxy dipasang autentikasi user proxy dengan enkripsi MD5 dengan dua username, yaitu luffybelikapalyyy dengan password luffy_yyy dan zorobelikapalyyy dengan password zoro_yyy (9)
  ### Jawaban : 
## Soal 10
  Transaksi jual beli tidak dilakukan setiap hari, oleh karena itu akses internet dibatasi hanya dapat diakses setiap hari Senin-Kamis pukul 07.00-11.00 dan setiap hari Selasa-Jum’at pukul 17.00-03.00 keesokan harinya (sampai Sabtu pukul 03.00) (10). 
### Jawaban : 
