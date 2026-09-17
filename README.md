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


---
 
## Soal 11 : Telnet

### Tujuan
Membuktikan bahwa Telnet mengirim data sesi (termasuk username & password) dalam bentuk **plaintext**, sehingga bisa terlihat langsung saat trafik ditangkap di Wireshark.

### Langkah 1 

Di console **Chisa**, buat user baru:
```bash
adduser phantom_user
```
Isi password: `wired_ghost`

Verifikasi user sudah dibuat:
```bash
cat /etc/passwd | grep phantom_user
```

### Langkah 2 — Jalankan Telnet Server di Chisa

```bash
telnetd -p 23
```

Pastikan port 23 sudah listening:
```bash
ss -lnt | grep ':23'
```
Target: port 23 statusnya `LISTEN`

### Langkah 3 — Koneksi dari Eiri

Cek konektivitas ke Chisa dulu:
```bash
ping -c 4 192.233.2.2
```

Lalu login via Telnet:
```bash
telnet 192.233.2.2
```
Masukkan kredensial:
- Username: `phantom_user`
- Password: `wired_ghost`

Setelah masuk, verifikasi:
```bash
whoami
```
Target output: `phantom_user`

Keluar dari sesi:
```bash
exit
```

### Langkah 4 — Capture & Analisis di Wireshark

1. Mulai **capture** pada link yang dilalui trafik Eiri → Chisa (lakukan ini **sebelum** membuat sesi Telnet baru).
2. Buat sesi Telnet baru dari Eiri, login ulang dengan `phantom_user` / `wired_ghost`.
3. Terapkan display filter:
   ```
   tcp.port == 23
   ```
4. Klik salah satu paket Telnet → klik kanan → **Follow → TCP Stream**.
5. Cari di isi stream: `phantom_user` dan `wired_ghost` — keduanya akan terlihat jelas sebagai teks biasa.

### Kesimpulan

Telnet tidak mengenkripsi data sesi. Karena itu, username dan password yang dikirim antara client dan server dapat terbaca langsung sebagai plaintext saat trafik dianalisis di Wireshark. Telnet bersifat interaktif sehingga input pengguna bisa dikirim segera setelah karakter diketik — dalam kondisi tertentu, beberapa karakter bisa terlihat di segmen TCP yang berbeda, karena TCP adalah *byte stream* dan tidak menjamin satu karakter selalu berada dalam satu paket yang sama.

### Screenshot yang Diperlukan
- [ ] Chisa: hasil `cat /etc/passwd | grep phantom_user`
- [ ] Eiri: hasil `telnet 192.233.2.2` dan `whoami`
- [ ] Wireshark: filter `tcp.port == 23`
- [ ] Follow TCP Stream: menampilkan `phantom_user` dan `wired_ghost`

### Script Otomatisasi (Chisa)

```bash
cat > /root/no11_telnet.sh <<'EOF'
#!/bin/sh

echo "=== MEMBUAT USER TELNET ==="
adduser -D phantom_user 2>/dev/null || true
echo 'phantom_user:wired_ghost' | chpasswd

echo "=== MENJALANKAN TELNET SERVER ==="
telnetd -p 23

echo "=== VERIFIKASI USER ==="
grep '^phantom_user:' /etc/passwd

echo "=== VERIFIKASI PORT 23 ==="
ss -lnt | grep ':23'
EOF

chmod +x /root/no11_telnet.sh
/root/no11_telnet.sh
```

---

## Soal 12 

### Tujuan

Alice memindai Knights menggunakan Netcat untuk membuktikan status tiga port:
| Port | Status   |
|------|----------|
| 22   | Terbuka  |
| 80   | Terbuka  |
| 7777 | Tertutup |

### Langkah 1 — Cek IP Knights

```bash
ip -br a
```
IP yang digunakan: `192.233.3.2`

### Langkah 2 — Buka Port 22 & 80 di Knights

```bash
nc -l -p 22 &
nc -l 80 &
```
⚠️ Jangan tekan `Ctrl+C` — biarkan kedua proses tetap berjalan di background.

### Langkah 3 — Verifikasi Port Listening

```bash
ss -lnt
```
Target: muncul `0.0.0.0:22` dan `0.0.0.0:80`

Pastikan port 7777 **sengaja tidak dibuat listener**:
```bash
ss -lnt | grep ':7777'
```
Target: tidak ada output.

### Langkah 4 — Scan dari Alice

```bash
nc -zv 192.233.3.2 22
nc -zv 192.233.3.2 80
nc -zv 192.233.3.2 7777
```

| Port | Hasil                |
|------|-----------------------|
| 22   | `succeeded`           |
| 80   | `succeeded`           |
| 7777 | `failed` / `refused`  |

### Langkah 5 — Capture & Analisis di Wireshark

1. Mulai **capture** di Knights **sebelum** Alice melakukan scan.
2. Terapkan display filter:
   ```
   tcp.port == 22 || tcp.port == 80 || tcp.port == 7777
   ```

**Untuk port terbuka (22 & 80):**
- Urutan paket: `SYN` (Alice→Knights) → `SYN, ACK` (Knights→Alice) → `ACK` (Alice→Knights)
- Klik paket `[SYN, ACK]` → buka **Transmission Control Protocol → Flags**
- Target: `SYN = 1`, `ACK = 1`
- **:** port menerima permintaan koneksi dan merespons dengan SYN-ACK.

**Untuk port tertutup (7777):**
- Urutan paket: `SYN` (Alice→Knights) → `RST, ACK` (Knights→Alice)
- Klik paket `[RST, ACK]` → buka **Transmission Control Protocol → Flags**
- Target: `RST = 1`, `ACK = 1`
- **:** koneksi ditolak/reset karena tidak ada layanan yang menerima koneksi di port tersebut.

### Tabel Hasil Akhir

| Port | Status   | Respons  |
|------|----------|----------|
| 22   | Terbuka  | SYN-ACK  |
| 80   | Terbuka  | SYN-ACK  |
| 7777 | Tertutup | RST-ACK  |

### Kesimpulan

Pada port 22 dan 80 yang terbuka, Knights merespons SYN dari Alice dengan SYN-ACK — flag `SYN` menandakan tanggapan terhadap permintaan pembentukan koneksi, sedangkan `ACK` menandakan acknowledgment atas segmen yang diterima. Pada port 7777 yang tertutup, Knights merespons dengan RST-ACK, menunjukkan koneksi ditolak karena tidak ada layanan yang berjalan di port tersebut.

---

## Soal 13

---

## Soal 14 — Analisis `wired_bruteforce.pcapng`

### Tujuan
Menemukan 5 hal dari file capture:
1. IP attacker
2. IP + port target
3. Password user `lain_admin`
4. Jenis & versi web server
5. Validasi jawaban lewat socket

### Langkah 1 — Buka File PCAP

Di Wireshark:
```
File → Open → wired_bruteforce.pcapng
```

### Langkah 2 — Cari Request Login

Terapkan filter:
```
http.request.method == "POST"
```

Kalau mau langsung cari berdasarkan username, bisa pakai:
```
tcp contains "lain_admin"
```
atau
```
frame contains "lain_admin"
```

### Langkah 3 — Identifikasi IP Attacker & Target

Klik paket `POST /login.php HTTP/1.1`, lalu lihat:
- **Source** → IP attacker
- **Destination** → IP target

✅ Hasil:
| Keterangan   | Nilai         |
|--------------|---------------|
| IP Attacker  | 172.26.7.50   |
| IP Target    | 172.26.7.100  |

### Langkah 4 — Cari Port Target

Masih di paket yang sama, buka bagian **Transmission Control Protocol → Dst Port**.

✅ Hasil: port `8080` → target lengkap: **172.26.7.100:8080**

### Langkah 5 — Temukan Password `lain_admin`

Klik kanan paket yang mengandung `username=lain_admin` → **Follow → TCP Stream**.

Di dalam stream akan terlihat:
```
username=lain_admin
password=wired_pr0tocol_7
```
Diikuti response sukses:
```
HTTP/1.1 200 OK
Success! Login successful.
```

### Langkah 6 — Identifikasi Web Server

Terapkan filter:
```
http.response
```
atau lebih spesifik:
```
http.response && http contains "Server:"
```

✅ Hasil: `Server: Apache/2.4.62`

### Langkah 7 — Validasi via Socket

Jalankan di terminal:
```bash
nc 10.4.89.247 3401
```
Jawab pertanyaan yang muncul sesuai urutan prompt, dengan jawaban:
```
172.26.7.50
172.26.7.100:8080
wired_pr0tocol_7
Apache/2.4.62
```
⚠️ Jangan ikut mengetik karakter `>` — itu hanya tanda prompt, bukan bagian dari jawaban.

### Hasil Soal 14

| Item          | Hasil                     |
|---------------|---------------------------|
| IP Attacker   | 172.26.7.50               |
| Target        | 172.26.7.100:8080         |
| Username      | lain_admin                |
| Password      | wired_pr0tocol_7          |
| Web Server    | Apache/2.4.62             |
| Socket        | `nc 10.4.89.247 3401`     |
| Flag | `KOMJAR26{W1r3d_Brut3_AAjooeohgP3C6K4cHa0NyAQAG}` |


## Filter Wireshark

**Soal 14**
```
http
http.request
http.request.method == "POST"
tcp contains "lain_admin"
frame contains "lain_admin"
http.response
http.response && http contains "Server:"
```
---
## Soal 15 — Analisis `wired_usb_hid.pcap` (USB HID Keyboard)

### Tujuan
Menemukan 4 hal dari file capture:
1. Vendor ID (VID) perangkat USB
2. Product ID (PID) perangkat USB
3. Device address perangkat
4. Pesan rahasia hasil rekaman keystroke
5. Validasi jawaban lewat socket

### Langkah 1 — Buka File PCAP

Di Wireshark:
```
File → Open → wired_usb_hid.pcap
```

### Langkah 2 — Tampilkan Traffic USB

Terapkan filter:
```
usb
```
Cari packet yang berisi *device descriptor* (informasi identitas perangkat).

### Langkah 3 — Identifikasi Vendor ID & Product ID

Cari packet:
```
GET DESCRIPTOR Response DEVICE
```
Klik packet tersebut, lalu buka **USB → Device Descriptor**.

✅ Hasil:
| Item      | Nilai    | Keterangan            |
|-----------|----------|------------------------|
| idVendor  | 0x046d   | Logitech, Inc.         |
| idProduct | 0xc31c   | Keyboard K120           |

### Langkah 4 — Identifikasi Device Address

⚠️ Jangan pakai angka `0` dari packet descriptor awal — itu bukan device address yang dimaksud. Cari packet **interrupt** milik keyboard/HID.

Terapkan filter:
```
usb.capdata && usb.device_address == 7
```
Pada bagian **USB URB**, akan terlihat:
```
Device address: 7
```
✅ Hasil: **Device Address = 7**

### Langkah 5 — Decode Pesan Rahasia dari Keystroke

Gunakan filter yang sama:
```
usb.capdata && usb.device_address == 7
```
Perhatikan kolom **Leftover Capture Data** pada setiap paket.

- Abaikan report yang isinya semua nol: `0000000000000000` (artinya tidak ada tombol ditekan)
- Cari report yang punya byte non-zero, contoh: `02001a0000000000`

Cara membaca 1 report HID keyboard (8 byte):
```
Byte 1 = Modifier (misal 02 = Left Shift)
Byte 3 = Keycode tombol yang ditekan (misal 1a = tombol W)
```
Jadi `02001a0000000000` → Shift + W → karakter **`W`**

⚠️ **Penting:** untuk mendapatkan pesan rahasia yang benar, decode **seluruh 8-byte HID report** satu per satu (termasuk byte modifier-nya), jangan hanya melihat byte keycode saja — karena huruf besar/kecil ditentukan oleh modifier Shift.

### Langkah 6 — Validasi via Socket

Jalankan di terminal:
```bash
nc 10.4.89.247 3402
```
Jawab sesuai urutan prompt yang muncul:
```
Vendor ID       → 0x046d
Product ID      → 0xc31c
Device Address  → 7
Secret Message  → (hasil decoding lengkap dari Langkah 5)
```
⚠️ Jangan menebak kapitalisasi atau karakter kalau validator menolak — pastikan hasil decoding benar-benar berasal dari seluruh HID report, bukan cuma byte keycode.

### Ringkasan Hasil Soal 15

| Item            | Hasil                          |
|-----------------|---------------------------------|
| Vendor ID       | 0x046d                          |
| Product ID      | 0xc31c                          |
| Device Address  | 7                                |
| Secret Message  | *(isi hasil final validator)*   |
| Socket          | `nc 10.4.89.247 3402`           |
| Flag | `KOMJAR26{USB_K3ystr0k3_fVuUjFmLbe0Am4cid1YVdfeB6}` |


## Filter Wireshark

**Soal 15**
```
usb
usb.capdata
usb.capdata && usb.device_address == 7
```


---

## Soal 16 — Analisis `wired_ftp_theft.pcap` (FTP Theft)

### Tujuan
Menemukan 5 hal dari file capture:
1. IP server FTP
2. Banner software FTP
3. Username attacker
4. Password attacker
5. Ukuran file yang dicuri (`knights_payload.exe`)
6. Validasi jawaban lewat socket

### Langkah 1 — Buka File PCAP

```
File → Open → wired_ftp_theft.pcap
```

### Langkah 2 — Tampilkan Traffic FTP

Terapkan filter:
```
ftp
```

### Langkah 3 — Identifikasi Banner FTP & IP Server

Cari response:
```
220 Welcome to Wired FTP Server (vsftpd 3.0.5)
```
Pada packet ini, lihat kolom **Source** → itu adalah IP server FTP.

✅ Hasil:
| Item         | Nilai            |
|--------------|-------------------|
| IP Server    | 198.51.100.7      |
| Banner       | vsftpd 3.0.5      |

### Langkah 4 — Identifikasi Username

Terapkan filter:
```
ftp.request.command == "USER"
```
Ditemukan:
```
USER knights_agent
```
✅ Hasil: **Username = knights_agent**

### Langkah 5 — Identifikasi Password

Terapkan filter:
```
ftp.request.command == "PASS"
```
Ditemukan:
```
PASS N4v1_s3cur3_2026
```
✅ Hasil: **Password = N4v1_s3cur3_2026**

### Langkah 6 — Identifikasi File & Ukurannya

Terapkan filter:
```
ftp.request.command == "SIZE"
```
Ditemukan:
```
SIZE knights_payload.exe
```
Response:
```
213 524288
```
✅ Hasil: **File size = 524288 bytes**

Pastikan file benar-benar diunduh dengan filter:
```
ftp.request.command == "RETR"
```
Ditemukan:
```
RETR knights_payload.exe
```
Diikuti response transfer:
```
150 Opening BINARY mode data connection for knights_payload.exe (524288 bytes)
226 Transfer complete.
```

### Langkah 7 — Validasi via Socket

Jalankan di terminal:
```bash
nc 10.4.89.247 3403
```
Jawab sesuai urutan prompt:
```
IP Server   → 198.51.100.7
Banner      → vsftpd 3.0.5
Username    → knights_agent
Password    → N4v1_s3cur3_2026
File Size   → 524288
```

### Ringkasan Hasil Soal 16

| Item          | Hasil                     |
|---------------|---------------------------|
| IP FTP Server | 198.51.100.7               |
| Banner        | vsftpd 3.0.5                |
| Username      | knights_agent               |
| Password      | N4v1_s3cur3_2026            |
| File Size     | 524288 bytes                 |
| Socket        | `nc 10.4.89.247 3403`       |
| Flag | `KOMJAR26{FTP_Th3ft_IaANBuM6U4F8KY7MiK3Rj5mgd}` |

### Screenshot yang Diperlukan
- [ ] Banner FTP (response `220 ...`)
- [ ] Command `USER` dan `PASS`
- [ ] Command `SIZE` dan `RETR` untuk `knights_payload.exe`
- [ ] Hasil validasi `nc 10.4.89.247 3403`

---

## Filter Wireshark

**Soal 16**
```
ftp
ftp.request.command == "USER"
ftp.request.command == "PASS"
ftp.request.command == "RETR"
ftp.request.command == "SIZE"
ftp.response.code == 220
ftp-data
```

## Referensi Command Validasi

| Soal | Command                  |
|------|---------------------------|
| 15   | `nc 10.4.89.247 3402`     |
| 16   | `nc 10.4.89.247 3403`     |

---

## Checklist Akhir

### Soal 15
- [ ] File `wired_usb_hid.pcap` berhasil dibuka
- [ ] Vendor ID ditemukan (0x046d)
- [ ] Product ID ditemukan (0xc31c)
- [ ] Device Address ditemukan (7)
- [ ] Pesan rahasia berhasil di-decode dari seluruh HID report
- [ ] Validasi socket port 3402 berhasil
- [ ] Screenshot lengkap

### Soal 16
- [ ] File `wired_ftp_theft.pcap` berhasil dibuka
- [ ] IP server FTP ditemukan (198.51.100.7)
- [ ] Banner FTP ditemukan (vsftpd 3.0.5)
- [ ] Username ditemukan (knights_agent)
- [ ] Password ditemukan (N4v1_s3cur3_2026)
- [ ] Ukuran file ditemukan (524288 bytes)
- [ ] Validasi socket port 3403 berhasil
- [ ] Screenshot lengkap

---

## Soal 17 — Analisis `wired_http_c2.pcap`

### Tujuan
Menemukan 4 hal dari file capture:
1. Domain pada header Host
2. IP server penyerang
3. Nama file executable yang diunduh
4. HTTP status code
5. Validasi jawaban lewat socket

### Langkah 1 — Buka File PCAP

```
File → Open → wired_http_c2.pcap
```

### Langkah 2 — Cari Traffic HTTP

Filter umum:
```
http
```
Lebih spesifik ke request saja:
```
http.request
```

### Langkah 3 — Cari Download Malware

Filter:
```
http.request.method == "GET"
```
atau langsung cari nama file:
```
tcp contains "navi_agent.exe"
```

Request yang ditemukan:
```
GET /navi_agent.exe HTTP/1.1
```

### Langkah 4 — Identifikasi Domain (Host)

Pada header HTTP request, cari baris:
```
Host: wired-update.net
```
✅ Hasil: `wired-update.net`

### Langkah 5 — Identifikasi IP Server

Pada packet request yang sama, lihat kolom **Destination**.

✅ Hasil: `203.0.113.42`

### Langkah 6 — Identifikasi Nama File Executable

Terlihat dari request maupun response:
```
GET /navi_agent.exe HTTP/1.1
filename="navi_agent.exe"
```
✅ Hasil: `navi_agent.exe`

### Langkah 7 — Cek HTTP Status Code

Pada HTTP response:
```
HTTP/1.1 200 OK
```
✅ Hasil: `200`

### Langkah 8 — Validasi via Socket

Jalankan:
```bash
nc 10.4.89.247 3404
```
Jawab sesuai urutan prompt:
```
wired-update.net
203.0.113.42
navi_agent.exe
200
```

### Hasil Soal 17

| Item          | Hasil                     |
|---------------|---------------------------|
| Domain/Host   | wired-update.net          |
| IP Server     | 203.0.113.42               |
| File          | navi_agent.exe             |
| HTTP Status   | 200                         |
| Socket        | `nc 10.4.89.247 3404`      |
| Flag | `KOMJAR26{Navi_C2_D0wnl04d_m50RvxYQlKDy89iXKyJR0az5R}` |

### Screenshot yang Diperlukan
- [ ] Paket `GET /navi_agent.exe` beserta header Host
- [ ] Source/Destination yang menunjukkan IP server
- [ ] Response `200 OK` beserta nama file
- [ ] Hasil validasi `nc 10.4.89.247 3404`

---

## Filter Wireshark

**Soal 17**
```
http
http.request
http.request.method == "GET"
tcp contains "navi_agent.exe"
frame contains "navi_agent.exe"
http.response
```

---

## Checklist Akhir

### Soal 14
- [ ] File `wired_bruteforce.pcapng` berhasil dibuka
- [ ] IP attacker teridentifikasi (172.26.7.50)
- [ ] IP + port target teridentifikasi (172.26.7.100:8080)
- [ ] Password `lain_admin` ditemukan via TCP Stream
- [ ] Versi web server ditemukan (Apache/2.4.62)
- [ ] Validasi socket port 3401 berhasil
- [ ] Screenshot lengkap

### Soal 17
- [ ] File `wired_http_c2.pcap` berhasil dibuka
- [ ] Domain/Host teridentifikasi (wired-update.net)
- [ ] IP server teridentifikasi (203.0.113.42)
- [ ] Nama file executable ditemukan (navi_agent.exe)
- [ ] HTTP status code ditemukan (200)
- [ ] Validasi socket port 3404 berhasil
- [ ] Screenshot lengkap


---

## Soal 18

| Flag | `KOMJAR26{SMB_Tr4nsf3r_kpOj6Kwh8azA32lpWii7iZ3ru}` |

---

## Soal 19

| Flag | `KOMJAR26{SMTP_Ext0rt10n_ljhaA4kVFGHME79UJ16Dsvtj4}` |

---

## Soal 20

| Flag | `KOMJAR26{TLS_D3crypt_2NNInj3GnS9dnJCfsvLefyIyJ}` |

---

