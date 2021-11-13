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
        directory "/var/cache/bind";

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
	
#### Membuat Domain pada **DNS Server (EniesLobby)**
	
Mengakses file `/etc/bind/named.conf.local`

	zone "jualbelikapal.a05.com" {
	     type master;
	     file "/etc/bind/kaizoku/jualbelikapal.a05.com";
	};
	
Lalu melakukan beberapa hal berikut
	
	mkdir /etc/bind/kaizoku
	
	cp /etc/bind/db.local /etc/bind/kaizoku/jualbelikapal.a05.com
	
	
Mengubah konfigurasi pada file `/etc/bind/kaizoku/jualbelikapal.a05.com`
	
	
	;
	; BIND data file for local loopback interface
	;
	$TTL    604800
	@       IN      SOA     jualbelikapal.a05.com. root.jualbelikapal.a05.com. (
			     2021100401         ; Serial
				 604800         ; Refresh
				  86400         ; Retry
				2419200         ; Expire
				 604800 )       ; Negative Cache TTL
	;
	@       IN      NS      jualbelikapal.a05.com.
	@       IN      A       192.171.2.3
	www     IN      CNAME   jualbelikapal.a05.com.
	@       IN      AAAA    ::1

	
	
Melakukan restart pada DNS Server
	```
	service bind9 restart
	```


#### Pada **Proxy Server (Water7)** melakukan beberapa konfigurasi berikut
	
	mv /etc/squid/squid.conf /etc/squid/squid.conf.bak
	
	
Mengubah konfigurasi pada file `/etc/squid/squid.conf`
	
	
	http_port 5000
	visible_hostname jualbelikapal.a05.com
	http_access allow all
	
	
Melakukan restart pada Squid
	```
	service squid restart
	```
	
#### Pada bagian **Client Loguetown**
Aktifkan proxy
	
	
	export http_proxy="http://jualbelikapal.a05.com:5000"
	
	
Cek Proxy
	
	
	env | grep -i proxy
	
	
Maka akan menampilkan
	
![image](https://user-images.githubusercontent.com/80946219/141446549-584b4e29-aff6-48ab-b4c0-e232d5798025.png)
	
Cek proxy dengan perintah `lynx google.com`. Jika berhasil maka akan menampilkan tampilan seperti berikut.
	
![image](https://user-images.githubusercontent.com/80946219/141447065-c8f92ee2-5b28-47ac-9520-48b3a443ae39.png)
	
	
 
## Soal 9
  Agar transaksi jual beli lebih aman dan pengguna website ada dua orang, proxy dipasang autentikasi user proxy dengan enkripsi MD5 dengan dua username, yaitu luffybelikapalyyy dengan password luffy_yyy dan zorobelikapalyyy dengan password zoro_yyy (9)
  ### Jawaban : 
	
#### Pada **Proxy Server (Water7)**

Lakukan penginstalan apache2-utils
	
	apt-get update
	apt-get install apache2-utils -y
	
	
Lalu masukkan perintah berikut
	
	htpasswd -b -c -m /etc/squid/passwd luffybelikapala05 luffy_a05
	
	htpasswd -b -m /etc/squid/passwd zorobelikapala05 zoro_a05
	
`-m` sebagai kode bahwa password di enkripsi dengan metode MD5
	
Cek pada file `/etc/squid/passwd` apakah data user sudah masuk. Jika sudah maka akan terlihat seperti berikut.
![image](https://user-images.githubusercontent.com/80946219/141448028-f799c333-d1d6-4604-a5b6-6b5f058ef0bd.png)

Ubah konfigurasi pada file `/etc/squid/squid.conf` menjadi seperti berikut
	

	http_port 5000
	visible_hostname jualbelikapal.a05.com

	auth_param basic program /usr/lib/squid/basic_ncsa_auth /etc/squid/passwd
	auth_param basic children 5
	auth_param basic realm Proxy
	auth_param basic credentialsttl 2 hours
	auth_param basic casesensitive on
	acl USERS proxy_auth REQUIRED
	http_access allow USERS

	
Restart Squid

	service squid restart

	
#### Pada bagian **Client Loguetown**
Aktifkan proxy

	export http_proxy="http://jualbelikapal.a05.com:5000"

	
Coba akses `http://its.ac.id` dengan command `lynx`. Maka akan menampilkan hal berikut.
![image](https://user-images.githubusercontent.com/80946219/141449147-6c63213e-333f-44d2-bff0-1fb22d0a2622.png)

Ketika memasukkan username dan password barulah bisa diakses
![image](https://user-images.githubusercontent.com/80946219/141449520-552fca6f-9009-46da-863a-30c2353c8cec.png)
	
![image](https://user-images.githubusercontent.com/80946219/141449548-28c29166-b227-4bf2-85be-81bdc999a4e1.png)
![image](https://user-images.githubusercontent.com/80946219/141449450-10e7f96b-5d7c-41a7-b60b-3b4bb4127b9a.png)
	
	
## Soal 10
  Transaksi jual beli tidak dilakukan setiap hari, oleh karena itu akses internet dibatasi hanya dapat diakses setiap hari Senin-Kamis pukul 07.00-11.00 dan setiap hari Selasa-Jum’at pukul 17.00-03.00 keesokan harinya (sampai Sabtu pukul 03.00) (10). 
### Jawaban : 
	
#### Pada Water7
Modifikasi file `/etc/squid/acl.conf`
	acl LIBUR1 time S 00:00-24:00
	acl LIBUR2 time MT 00:00-07:00
	acl LIBUR3 time M 11:00-24:00
	acl LIBUR4 time TWH 11:00-17:00
	acl LIBUR5 time WHF 03:00-07:00
	acl LIBUR6 time A 03:00-14:00

Modifikasi juga file `/etc/squid/squid.conf`
	include /etc/squid/acl.conf

	http_port 5000
	visible_hostname jualbelikapal.a05.com

	http_access deny LIBUR1
	http_access deny LIBUR2
	http_access deny LIBUR3
	http_access deny LIBUR4
	http_access deny LIBUR5
	http_access deny LIBUR6

	auth_param basic program /usr/lib/squid/basic_ncsa_auth /etc/squid/pasauth_param basic children 5
	auth_param basic realm Proxy
	auth_param basic credentialsttl 2 hours
	auth_param basic casesensitive on
	acl USERS proxy_auth REQUIRED
	http_access allow USERS
	
Restart Squid

	service squid restart
	
#### Pada Loguetown
Export proxy
	
![10-1](https://user-images.githubusercontent.com/80946219/141480564-6d808a92-5836-4089-b3ac-a81c841b8b7b.jpg)

Ubah tanggal menjadi hari tidak bisa diakses
	
![10-2](https://user-images.githubusercontent.com/80946219/141480632-38ddb23e-3418-4527-874f-9090fb9184e7.jpg)
	
Maka akses akan ditolak
	
![10-3](https://user-images.githubusercontent.com/80946219/141480713-24ae0562-be4a-4d62-83d8-3aec580dd531.jpg)

Jika hari sesuai dengan hari yang bisa diakses
	
![10-3-1](https://user-images.githubusercontent.com/80946219/141480792-f957da1a-be77-4bcc-abe9-1446ed9e2702.jpg)
![10-4](https://user-images.githubusercontent.com/80946219/141480832-095c5a7f-4480-470f-bab9-889ba8855dc3.jpg)
	
## Soal 11
Agar transaksi bisa lebih fokus berjalan, maka dilakukan redirect website agar mudah mengingat website transaksi jual beli kapal. Setiap mengakses google.com, akan diredirect menuju super.franky.yyy.com dengan website yang sama pada soal shift modul 2. Web server super.franky.yyy.com berada pada node Skypie

### Jawaban 
Pertama kita config domain franky.a05.com di EnniesLobby yang berada di file named.conf.local.
	
![image](https://user-images.githubusercontent.com/42856438/141607805-f91f0785-8173-4021-9b0b-9a30ef45c951.png)
	
Kemudian buka file franky.a05.com, lalu edit seperti gambar dibawah ini
![image](https://user-images.githubusercontent.com/42856438/141607854-3db00816-c184-4bf9-8a63-28ec92150fe3.png)
	
Kemudian jangan lupa untuk restart apache. Jika sudah, maka kita edit konfigurasi pada file super.franky.a05.com.conf di nodes Skypie. Ubah menjadi seperti gambar dibawah ini 
![image](https://user-images.githubusercontent.com/42856438/141607968-742bc03e-092d-4939-a070-cdebfd4c57ac.png)

Kemudian pindah ke root pada skypie lalu download file dari https://raw.githubusercontent.com/FeinardSlim/Praktikum-Modul-2-Jarkom/main/super.franky.zip. Jika sudah, unzip pada direktori /var/www dan buat direktori super.franky.a05.com. 

Lalu aktifkan dengan command ```a2ensite super.franky.a05.com```
	
Selanjutnya yaitu pindah ke nodes Water7, buka file /etc/squid/squid.conf. Kemudian ketik config dibawah ini
![image](https://user-images.githubusercontent.com/42856438/141608224-47c073b0-cf5c-4f5f-b02e-98b3f86d839f.png)

Kemudian ```service squid restart```

Lalu tes pada Loguetown dengan mengetikkan ```lynx google.com```. Maka hasilnya akan seperti berikut
![image](https://user-images.githubusercontent.com/42856438/141608291-2a11543b-5fc7-4597-bb27-6b4577843f72.png)


## Soal 12
Saatnya berlayar! Luffy dan Zoro akhirnya memutuskan untuk berlayar untuk mencari harta karun di super.franky.yyy.com. Tugas pencarian dibagi menjadi dua misi, Luffy bertugas untuk mendapatkan gambar (.png, .jpg), sedangkan Zoro mendapatkan sisanya. Karena Luffy orangnya sangat teliti untuk mencari harta karun, ketika ia berhasil mendapatkan gambar, ia mendapatkan gambar dan melihatnya dengan kecepatan 10 kbps 
	
### Jawaban 
Buka /etc/squid/squid.conf pada Water7. Kemudian tambahkan konfigurasi berikut
![image](https://user-images.githubusercontent.com/42856438/141608733-a1934593-4dc6-4cb6-810d-6bb027584a05.png)

Kemudian coba tes download gambar pada Loguetwon. Ketik ```lynx google.com``` menggunakan username dan password luffy
![image](https://user-images.githubusercontent.com/42856438/141608655-2ad9fc32-8266-42b2-b4f5-59c9b7daf4fa.png)
Kecepatan dibatasi menjadi 10 kbps

Gambar berhasil terdownload.

![image](https://user-images.githubusercontent.com/42856438/141608722-420748a5-b653-40cf-9988-efe89f87de88.png)

	
## Soal 13
Sedangkan, Zoro yang sangat bersemangat untuk mencari harta karun, sehingga kecepatan kapal Zoro tidak dibatasi ketika sudah mendapatkan harta yang diinginkannya

### Jawaban 
Sama seperti soal sebelumnya, namun tambahkan konfigurasi berikut.
![image](https://user-images.githubusercontent.com/42856438/141608842-d2e2c93c-8628-49ec-a00f-c85d55767574.png)
Kemudian jangan lupa restart proxynya. 
	
Kemudian tes download gambar pada Loguetown. Ketik ```lynx google.com``` menggunakan username dan password zoro. 
![image](https://user-images.githubusercontent.com/42856438/141608924-e645e7e6-bfc4-4955-b1d1-c3425b2be46f.png)
Dan file car.jpg berhasil didownload tanpa limit kecepatan. 

	
