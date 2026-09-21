# PERTEMUAN 5 — Layanan DHCP & Access Control List (ACL) Dasar

**SI2514011 | Sub-CPMK-3 (C3) | Cisco Packet Tracer**

> Target akhir pertemuan (penutup 5 pertemuan): PC mendapat IP otomatis lewat DHCP, dan Tamu tidak bisa lagi mengakses jaringan Staf berkat ACL.

---

## Tujuan

Mahasiswa bisa mengaktifkan DHCP agar PC mendapat IP otomatis, dan membatasi akses Tamu ke Staf memakai ACL standard.

## Materi : DHCP & Access Control List

### 1. DHCP — Distribusi IP Otomatis

**Masalah tanpa DHCP:** Setiap kali ada PC baru yang disambungkan ke jaringan, admin harus datang ke PC tersebut, membuka pengaturan jaringan, dan mengisi IP, subnet mask, serta gateway secara manual. Bayangkan melakukan ini untuk 200 PC di kampus.

**Solusinya: DHCP** (*Dynamic Host Configuration Protocol*). Dengan DHCP, ada satu server (di modul ini, peran server dimainkan oleh router) yang bertugas **membagikan pengaturan jaringan secara otomatis** ke setiap PC yang memintanya.

#### Proses DHCP (Hafal 4 langkah ini: D-O-R-A)

| Langkah | Siapa yang Kirim | Ke Siapa | Isi Pesan |
|---|---|---|---|
| **1. Discover** | PC (klien) | Broadcast ke semua | *"Hei, ada DHCP server tidak di sini? Saya butuh IP!"* |
| **2. Offer** | Router/Server DHCP | PC | *"Ada! Saya tawarkan IP `192.168.10.10` untukmu."* |
| **3. Request** | PC (klien) | Broadcast | *"Oke, saya mau pakai IP `192.168.10.10` yang ditawarkan."* |
| **4. Acknowledge** | Router/Server DHCP | PC | *"Dikonfirmasi. IP `192.168.10.10` resmi milikmu untuk sementara."* |

#### DHCP Pool dan Excluded Address

- **Pool:** Rentang alamat IP yang boleh dibagikan ke klien. Misal, pool STAF berisi semua IP di `192.168.10.0/27`.
- **Excluded Address:** Alamat yang **dikecualikan** dari pool sehingga tidak ikut dibagikan. Alamat gateway wajib dikecualikan — kalau gateway sampai ikut dibagikan ke PC klien, maka gateway akan punya IP yang sama dengan PC lain dan jaringan kacau.

> **Aturan penting:** Perintah `ip dhcp excluded-address` **harus dijalankan sebelum** mendefinisikan pool-nya.

---

### 2. ACL — Penjaga Pintu Lalu Lintas Jaringan

Bayangkan ACL (*Access Control List*) seperti **daftar tamu di pintu masuk gedung**. Satpam memeriksa setiap orang yang datang dari atas ke bawah daftar: jika ada aturan yang cocok, langsung dieksekusi — boleh masuk atau ditolak. Jika sampai bawah tidak ada yang cocok, orang tersebut **otomatis ditolak** (inilah *implicit deny*).

#### ACL Standard (nomor 1–99)

ACL standard hanya menyaring berdasarkan satu kriteria: **alamat IP sumber** (dari mana paket datang). Ini paling sederhana tapi paling terbatas — tidak bisa membedakan jenis aplikasi, port, atau tujuan paket.

> **ACL Extended (nomor 100–199):** Bisa menyaring berdasarkan IP sumber, IP tujuan, protokol, dan nomor port. Lebih fleksibel, tapi tidak dibahas di modul ini.

#### Wildcard Mask — Kebalikan Subnet Mask

Saat menulis aturan ACL, perlu menyebut rentang IP yang diatur. Cara menyebut rentang ini memakai **wildcard mask**, bukan subnet mask biasa.

Wildcard mask adalah **kebalikan bit** dari subnet mask:
- Subnet mask `/28` = `255.255.255.240`
- Wildcard mask-nya = `0.0.0.15` (bit yang `1` di subnet mask menjadi `0`, dan sebaliknya)

> **Cara mudah menghitungnya:** `255.255.255.255 − subnet mask = wildcard mask`  
> Contoh: `255.255.255.255 − 255.255.255.240 = 0.0.0.15`

#### Arah ACL: `in` vs `out`

ACL dipasang pada sebuah interface router dengan salah satu arah:

| Arah | Menyaring lalu lintas yang... | Analogi |
|---|---|---|
| **`in`** | ...masuk **ke** router melalui interface itu | Satpam di pintu masuk gedung |
| **`out`** | ...keluar **dari** router melalui interface itu | Satpam di pintu keluar gedung |

Di modul ini, ACL dipasang **`out`** pada sub-interface Staf (`Gi0/0.10`). Artinya: setiap paket yang hendak **dikirimkan router ke jaringan Staf** akan diperiksa terlebih dahulu — apakah boleh masuk ke Staf atau tidak.

#### Implicit Deny — Aturan Tersembunyi di Akhir Setiap ACL

Setiap ACL Cisco **selalu memiliki satu baris aturan tak terlihat** di paling bawah:  
`deny any` — tolak semua yang tidak cocok dengan aturan di atasnya.

Inilah mengapa di kasus ini perlu menambahkan `permit any` secara eksplisit di baris terakhir — supaya lalu lintas dari selain Tamu (termasuk Staf sendiri yang balik ke jaringan mereka) tidak ikut diblokir oleh *implicit deny*.

#### Kebijakan di Modul Ini

| Dari | Ke | Diizinkan? | Alasannya |
|---|---|---|---|
| Tamu (`192.168.10.32/28`) | Staf (`192.168.10.0/27`) | ❌ Ditolak | Aturan `deny 192.168.10.32 0.0.0.15` pada ACL |
| Staf atau lainnya | Staf | ✅ Diizinkan | Aturan `permit any` di bawah deny |
| Staf (`192.168.10.0/27`) | Tamu (`192.168.10.32/28`) | ✅ Diizinkan | Tidak ada ACL yang dipasang di sub-interface Tamu |

## Langkah Praktikum

### 1. Aktifkan DHCP

```
ip dhcp excluded-address 192.168.10.1
ip dhcp pool STAF
 network 192.168.10.0 255.255.255.224
 default-router 192.168.10.1

ip dhcp excluded-address 192.168.10.33
ip dhcp pool TAMU
 network 192.168.10.32 255.255.255.240
 default-router 192.168.10.33
```

Ubah IP configuration PC dari **Static** menjadi **DHCP**, lalu di Command Prompt jalankan `ipconfig /renew`. Buktikan setiap PC mendapat IP sesuai subnet-nya secara otomatis.

### 2. Pasang ACL

```
access-list 10 deny 192.168.10.32 0.0.0.15
access-list 10 permit any
interface gigabitEthernet0/0.10
 ip access-group 10 out
```

Baris `deny` menolak seluruh alamat dari blok Tamu (`192.168.10.32/28`), baris `permit any` mengizinkan sisanya. ACL dipasang **keluar (`out`)** pada sub-interface Staf, artinya menyaring lalu lintas yang **akan masuk** ke jaringan Staf.

### 3. Verifikasi

- PC Tamu → PC Staf: `ping` harus **gagal**
- PC Staf → PC Tamu: `ping` harus **tetap berhasil**
- Jalankan `show access-lists`, periksa counter (match count) pada baris `deny` bertambah setelah PC Tamu mencoba ping ke Staf

## Tugas 5 (Penutup 5 Pertemuan)

1. Aktifkan DHCP untuk kedua VLAN (Staf dan Tamu), buktikan PC mendapat IP otomatis sesuai subnet-nya
2. Terapkan ACL yang menolak Tamu mengakses Staf, sementara Staf tetap bisa mengakses Tamu
3. Verifikasi kedua arah dengan `ping` dan `show access-lists`

**Kumpulkan (final, penutup 5 pertemuan):**
1. File `.pkt` final
2. Output `show access-lists`
3. Screenshot ping (Tamu→Staf gagal, Staf→Tamu berhasil) dan bukti IP dari DHCP (`ipconfig` kedua PC)
4. Laporan 1 halaman memakai [Lampiran B](lampiran/B-template-laporan.md) — mencakup topologi akhir, tabel IP plan lengkap (Pertemuan 2–5), konfigurasi DHCP dan ACL yang dipasang, dan bukti verifikasi

---

**Selanjutnya:** Tugas besar — materi dan ketentuan akan diberikan langsung oleh dosen pengampu.
