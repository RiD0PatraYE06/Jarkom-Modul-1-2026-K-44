# Jarkom-Modul-1-2026-K-44

## **Soal 1: Topologi & Konfigurasi Alokasi IP Address**

> **Deskripsi Soal:**
> Lain yang berperan sebagai Router membuat tiga Switch/Gateway:
> * **Switch 1:** menuju entitas **Alice** dan **Mika**
> 
> * **Switch 2:** menuju entitas **Chisa**
> 
> * **Switch 3:** menuju entitas **Knights** dan **Eiri**
> 
> 
> 
> Kelima entitas dikonfigurasi sebagai **Client** di GNS3 menggunakan IP Prefix kelompok.
> 
> 

---

### **1. Rancangan Topologi & Pembagian IP Address**

Berikut adalah pemetaan interface dan pengalamatan IP yang disesuaikan dengan skema Subnetting.

(Catatan: Anda dapat menyesuaikan prefix IP `192.168.x.x` di bawah ini dengan IP prefix kelompok Anda, misalnya `10.4.x.x`).

| Node | Type | Interface | IP Address / Netmask | Default Gateway | Terhubung Ke |
| --- | --- | --- | --- | --- | --- |
| **Lain** | Router | `eth0` | DHCP (NAT) | Auto (dari NAT) | Internet / NAT GNS3 |
|  |  | `eth1` | `192.168.1.1/24` | - | Switch 1 |
|  |  | `eth2` | `192.168.2.1/24` | - | Switch 2 |
|  |  | `eth3` | `192.168.3.1/24` | - | Switch 3 |
| **Alice** | Client | `eth0` | `192.168.1.2/24` | `192.168.1.1` | Switch 1 |
| **Mika** | Client | `eth0` | `192.168.1.3/24` | `192.168.1.1` | Switch 1 |
| **Chisa** | Client | `eth0` | `192.168.2.2/24` | `192.168.2.1` | Switch 2 |
| **Knights** | Client | `eth0` | `192.168.3.2/24` | `192.168.3.1` | Switch 3 |
| **Eiri** | Client | `eth0` | `192.168.3.3/24` | `192.168.3.1` | Switch 3 |

---

### **2. Script Konfigurasi Per Node**

Sesuai **Aturan Praktikum Poin 5**, semua script wajib diletakkan di direktori `/root`.

---

#### **A. Router Lain**

Buka console node **Lain**, buat script `/root/setup_lain.sh`:

```bash
cat << 'EOF' > /root/setup_lain.sh
#!/bin/bash

# Atur IP Address pada interface internal
ip addr add 192.168.1.1/24 dev eth1
ip link set dev eth1 up

ip addr add 192.168.2.1/24 dev eth2
ip link set dev eth2 up

ip addr add 192.168.3.1/24 dev eth3
ip link set dev eth3 up

echo "Konfigurasi IP pada Router Lain selesai."
EOF

chmod +x /root/setup_lain.sh
bash /root/setup_lain.sh

```

---

#### **B. Client Alice (di bawah Switch 1)**

Buka console node **Alice**, buat script `/root/setup_alice.sh`:

```bash
cat << 'EOF' > /root/setup_alice.sh
#!/bin/bash

ip addr add 192.168.1.2/24 dev eth0
ip link set dev eth0 up
ip route add default via 192.168.1.1

echo "Konfigurasi IP Alice selesai."
EOF

chmod +x /root/setup_alice.sh
bash /root/setup_alice.sh

```

---

#### **C. Client Mika (di bawah Switch 1)**

Buka console node **Mika**, buat script `/root/setup_mika.sh`:

```bash
cat << 'EOF' > /root/setup_mika.sh
#!/bin/bash

ip addr add 192.168.1.3/24 dev eth0
ip link set dev eth0 up
ip route add default via 192.168.1.1

echo "Konfigurasi IP Mika selesai."
EOF

chmod +x /root/setup_mika.sh
bash /root/setup_mika.sh

```

---

#### **D. Client Chisa (di bawah Switch 2)**

Buka console node **Chisa**, buat script `/root/setup_chisa.sh`:

```bash
cat << 'EOF' > /root/setup_chisa.sh
#!/bin/bash

ip addr add 192.168.2.2/24 dev eth0
ip link set dev eth0 up
ip route add default via 192.168.2.1

echo "Konfigurasi IP Chisa selesai."
EOF

chmod +x /root/setup_chisa.sh
bash /root/setup_chisa.sh

```

---

#### **E. Client Knights (di bawah Switch 3)**

Buka console node **Knights**, buat script `/root/setup_knights.sh`:

```bash
cat << 'EOF' > /root/setup_knights.sh
#!/bin/bash

ip addr add 192.168.3.2/24 dev eth0
ip link set dev eth0 up
ip route add default via 192.168.3.1

echo "Konfigurasi IP Knights selesai."
EOF

chmod +x /root/setup_knights.sh
bash /root/setup_knights.sh

```

---

#### **F. Client Eiri (di bawah Switch 3)**

Buka console node **Eiri**, buat script `/root/setup_eiri.sh`:

```bash
cat << 'EOF' > /root/setup_eiri.sh
#!/bin/bash

ip addr add 192.168.3.3/24 dev eth0
ip link set dev eth0 up
ip route add default via 192.168.3.1

echo "Konfigurasi IP Eiri selesai."
EOF

chmod +x /root/setup_eiri.sh
bash /root/setup_eiri.sh

```

---

### **3. Verifikasi Soal 1**

Untuk memastikan Soal 1 selesai dengan baik, jalankan perintah berikut di masing-masing node:

* Pada **Router Lain**:
```bash
ip -br a

```


*Pastikan `eth1`, `eth2`, dan `eth3` sudah terpasang IP masing-masing.*
* Pada **Client (misal Alice / Knights)**:
```bash
ip -br a
ip route

```


*Pastikan IP sudah sesuai subnet dan `default via` menunjuk ke IP interface router Lain yang sesuai.*

---

Mantap! Lanjut ke **Nomor 2** untuk menyambungkan router Lain ke internet publik lewat DHCP pada interface `eth0`.

Jalankan script ini di node **Router Lain** (`/root/setup_nat_dhcp.sh`):

```bash
cat << 'EOF' > /etc/network/interfaces
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
    address 192.233.1.1
    netmask 255.255.255.0

auto eth2
iface eth2 inet static
    address 192.233.2.1
    netmask 255.255.255.0

auto eth3
iface eth3 inet static
    address 192.233.3.1
    netmask 255.255.255.0
EOF

service networking restart

```

**Cara Verifikasi:**
Jalankan perintah `ping -c 4 8.8.8.8` di terminal Router Lain. Jika muncul respons *reply*, berarti interface `eth0` berhasil mendapatkan IP DHCP dan terhubung ke internet.

---

Modulnya sudah tersimpan. Sekarang kita lanjut ke **Nomor 3**.

Pada nomor 3, tujuannya membuat seluruh Entitas (Alice, Mika, Chisa, Knights, Eiri) saling terhubung lintas subnet via router Lain. Karena ketiga switch terhubung langsung ke port router (`eth1`, `eth2`, `eth3`), kamu cukup mengaktifkan **Kernel IP Forwarding** di Router Lain.

**Konfigurasi di Node Router Lain**

Jalankan perintah ini di terminal Router Lain:

```bash
sysctl -w net.ipv4.ip_forward=1

```

Agar fitur forwarding ini tidak hilang saat router di-restart, tambahkan baris `up sysctl -w net.ipv4.ip_forward=1` ke file `/etc/network/interfaces`:

```bash
cat << 'EOF' > /etc/network/interfaces
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet dhcp
    up sysctl -w net.ipv4.ip_forward=1

auto eth1
iface eth1 inet static
    address 192.233.1.1
    netmask 255.255.255.0

auto eth2
iface eth2 inet static
    address 192.233.2.1
    netmask 255.255.255.0

auto eth3
iface eth3 inet static
    address 192.233.3.1
    netmask 255.255.255.0
EOF

service networking restart

```

**Cara Verifikasi Nomor 3**

Lakukan uji tes koneksi silang antar-subnet. Coba jalankan perintah berikut di terminal node **Alice** (`192.233.1.2`):

* Ping ke Chisa (Subnet 2): `ping -c 3 192.233.2.2`
* Ping ke Eiri (Subnet 3): `ping -c 3 192.233.3.3`

Jika paket *reply* diterima, konfigurasi routing antar-entitas sudah berhasil.

Kabari kalau ping antar-client sudah jalan, nanti kita lanjut ke Nomor 4 (NAT Masquerade & DNS Client)!

---

Konfigurasi Nomor 4 dibagi menjadi dua bagian: mengatur **NAT Masquerade** di Router Lain dan mendaftarkan **DNS Resolver** di setiap node Client.

**1. Konfigurasi NAT Masquerade di Router Lain**

Jalankan perintah `iptables` ini di terminal **Router Lain** agar paket IP privat dari semua client disamarkan menggunakan IP `eth0` saat keluar ke internet:

```bash
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE

```

Agar rule NAT ini otomatis dipasang setiap kali network restart, masukkan perintah tersebut ke file `/etc/network/interfaces` di Router Lain:

```bash
cat << 'EOF' > /etc/network/interfaces
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet dhcp
    up sysctl -w net.ipv4.ip_forward=1
    up iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE

auto eth1
iface eth1 inet static
    address 192.233.1.1
    netmask 255.255.255.0

auto eth2
iface eth2 inet static
    address 192.233.2.1
    netmask 255.255.255.0

auto eth3
iface eth3 inet static
    address 192.233.3.1
    netmask 255.255.255.0
EOF

service networking restart

```

**2. Konfigurasi DNS Resolver di Setiap Node Client**

Jalankan perintah berikut di terminal **semua Client** (Alice, Mika, Chisa, Knights, dan Eiri) untuk mengeset DNS resolver ke `8.8.8.8`:

```bash
echo "nameserver 8.8.8.8" > /etc/resolv.conf

```

**3. Verifikasi Konektivitas Client**

Buka terminal di salah satu client (misalnya **Alice** atau **Mika**) dan uji konektivitas internetnya:

* Ping IP publik: `ping -c 3 8.8.8.8`

* Ping domain web: `ping -c 3 google.com`


Kalau keduanya memberikan balasan *reply*, berarti Nomor 4 sudah beres. Mau langsung lanjut ke Nomor 5 untuk bikin script `/root/cek_status.sh`?

---

Berikut cara pengerjaan **Nomor 5** pada node **Router Lain**:

**1. Buat Script `/root/cek_status.sh**`

Jalankan perintah berikut di terminal Router Lain untuk membuat script verifikasi sesuai permintaan soal:

```bash
cat << 'EOF' > /root/cek_status.sh
#!/bin/bash
echo "=== RINGKASAN INTERFACE ==="
ip -br a
echo ""
echo "=== STATUS TABEL NAT ==="
iptables -t nat -L -v -n
EOF

chmod +x /root/cek_status.sh

```

**2. Verifikasi Persistensi Konfigurasi**

Pastikan file `/etc/network/interfaces` di Router Lain sudah memuat opsi `up` agar IP forwarding dan NAT Masquerade tidak hilang saat di-restart:

```bash
cat << 'EOF' > /etc/network/interfaces
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet dhcp
    up sysctl -w net.ipv4.ip_forward=1
    up iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE

auto eth1
iface eth1 inet static
    address 192.233.1.1
    netmask 255.255.255.0

auto eth2
iface eth2 inet static
    address 192.233.2.1
    netmask 255.255.255.0

auto eth3
iface eth3 inet static
    address 192.233.3.1
    netmask 255.255.255.0
EOF

```

**3. Pengujian**

Jalankan script verifikasi dengan perintah:

```bash
/root/cek_status.sh

```

Coba *restart* node Router Lain di GNS3, lalu jalankan kembali `/root/cek_status.sh`. Jika daftar interface dan aturan `MASQUERADE` tetap muncul setelah reboot, berarti Nomor 5 sudah selesai.

Mau lanjut ke Nomor 6 (packet sniffing DNS/ICMP di node Mika)?

---

Jalankan script generator tersebut di node **Mika** dan tangkap paketnya menggunakan Wireshark.

**1. Eksekusi Generator Trafik di Node Mika**

Buat dan jalankan file script `traffic_protocol7.sh` di folder `/root` node Mika:

```bash
cat << 'EOF' > /root/traffic_protocol7.sh
#!/bin/bash
echo "============================================"
echo "  Protocol 7 Traffic Generator v2026"
echo "  Node: Mika Iwakura"
echo "============================================"
echo "[*] Generating DNS & ICMP traffic..."

ping -c 5 8.8.8.8 &
ping -c 5 1.1.1.1 &
ping -c 3 its.ac.id &

nslookup google.com 8.8.8.8 &
nslookup its.ac.id 8.8.8.8 &
nslookup github.com 1.1.1.1 &
dig @8.8.8.8 example.com A &
dig @1.1.1.1 cloudflare.com AAAA &

wait
echo "[*] Traffic generation complete."
echo "[*] Check Wireshark for captured packets."
EOF

chmod +x /root/traffic_protocol7.sh
/root/traffic_protocol7.sh

```

**2. Packet Sniffing & Display Filter di Wireshark**

1. Klik kanan pada link kabel antara **Mika** dan **Switch 1** di GNS3, lalu pilih **Start capture**.


2. Buka Wireshark, lalu masukkan perintah filter ini pada baris *Display Filter*:


```text
dns or icmp

```



**3. Ringkasan Paket yang Lolos Filter (Untuk Laporan Resmi)**

* **Trafik ICMP**:
* 5 paket Echo Request & 5 Echo Reply ke `8.8.8.8`.


* 5 paket Echo Request & 5 Echo Reply ke `1.1.1.1`.


* 3 paket Echo Request & 3 Echo Reply ke host `its.ac.id`.




* **Trafik DNS**:
* Standard Query & Response (Record A) untuk domain `google.com` dan `its.ac.id` via DNS Server `8.8.8.8`.


* Standard Query & Response (Record A) untuk domain `github.com` via DNS Server `1.1.1.1`.


* Query `dig` Record A untuk `example.com` via `8.8.8.8`.


* Query `dig` Record AAAA (IPv6) untuk `cloudflare.com` via `1.1.1.1`.





Ambil *screenshot* tampilan Wireshark yang sudah terfilter `dns or icmp` untuk dilampirkan.

![](./assets/6.png)

Kabari kalau screenshot nomor 6 sudah siap, kita bisa langsung lanjut bikin FTP Server vsFTPd di node Chisa untuk Nomor 7!

---

Yap, screenshot Wireshark kamu buat nomor 6 udah 100% bener! Semua trafik DNS dan ICMP hasil eksekusi `traffic_protocol7.sh` udah berhasil tersaring dengan rapi.

Sekarang kita lanjut ke **Nomor 7**. Di nomor ini kamu diminta setup **FTP Server (`vsftpd`)** di node **Chisa** dengan aturan:

* Shared folder: `/var/wired/data`

* User `alice`: Hak akses Read & Write


* User `mika`: Hak akses Read-only


* User `eiri`: Di-blacklist (tidak bisa login)



---

### 1. Konfigurasi di Node Chisa

Jalankan script ini di terminal **Chisa** (`192.233.2.2`) buat install `vsftpd`, buat user, dan atur hak aksesnya:

```bash
cat << 'EOF' > /root/setup_ftp_chisa.sh
#!/bin/sh
apk update && apk add vsftpd

# Buat direktori penyimpanan data
mkdir -p /var/wired/data
mkdir -p /usr/share/vsftpd/empty
chmod 777 /var/wired/data

# Buat akun user sistem di Alpine
adduser -D -s /bin/sh alice 2>/dev/null || true
adduser -D -s /bin/sh mika 2>/dev/null || true
adduser -D -s /bin/sh eiri 2>/dev/null || true

# Set password user
echo "alice:alice123" | chpasswd
echo "mika:mika123" | chpasswd
echo "eiri:eiri123" | chpasswd

# Konfigurasi utama vsftpd di Alpine
mkdir -p /etc/vsftpd
cat << 'CONF' > /etc/vsftpd/vsftpd.conf
anonymous_enable=NO
local_enable=YES
write_enable=YES
dirmessage_enable=YES
use_localtime=YES
xferlog_enable=YES
connect_from_port_20=YES
secure_chroot_dir=/usr/share/vsftpd/empty
seccomp_sandbox=NO

local_root=/var/wired/data
allow_writeable_chroot=YES

# Blacklist user eiri
userlist_enable=YES
userlist_file=/etc/vsftpd.userlist
userlist_deny=YES

# Konfigurasi per-user
user_config_dir=/etc/vsftpd_user_config
CONF

# Masukkan eiri ke daftar blacklist
echo "eiri" > /etc/vsftpd.userlist
echo "eiri" >> /etc/ftpusers

# Atur izin khusus per user (alice = RW, mika = Read-Only)
mkdir -p /etc/vsftpd_user_config
echo "write_enable=YES" > /etc/vsftpd_user_config/alice
echo "write_enable=NO" > /etc/vsftpd_user_config/mika

# Restart daemon vsftpd
pkill vsftpd || true
vsftpd /etc/vsftpd/vsftpd.conf &
EOF

chmod +x /root/setup_ftp_chisa.sh
/root/setup_ftp_chisa.sh

```

---

### 2. Cara Pembuktian & Verifikasi Soal Nomor 7

**A. Bukti 1: Membuat file `signal_alice.txt` dari node Alice (Read & Write)**

Jalankan perintah ini di terminal node **Alice**:

```bash
# Buat file lokal di Alice
echo "Pesan rahasia dari Alice" > /root/signal_alice.txt

# Login FTP ke Chisa dan upload file
ftp 192.233.2.2

```

Saat diminta masukin kredensial:

* User: `alice`
* Password: `alice123`

Setelah berhasil masuk prompt `ftp>`, ketik perintah berikut buat upload file:

```text
put /root/signal_alice.txt signal_alice.txt
ls
quit

```

*(Jika file `signal_alice.txt` berhasil ter-upload dan muncul pas `ls`, artinya hak akses `alice` bener)*.

---

**B. Bukti 2: Uji coba login dari node Eiri (Blacklist)**

Jalankan perintah ini di terminal node **Eiri**:

```bash
ftp 192.233.2.2

```

Saat diminta masukin kredensial:

* User: `eiri`
* Password: `eiri123`

Server bakalan langsung nolak login dengan pesan respons: `530 Permission denied.` atau `Login incorrect.`. Ini ngebuktiin user `eiri` berhasil diblokir.

Screenshot kedua proses di atas buat dilampirin ke laporan. Kalau udah beres, kabari ya biar kita lanjut ngerjain nomor 8!

---

