# PERTEMUAN 3 — VLAN & Switching

**SI2514011 | Sub-CPMK-3 (C3) | Cisco Packet Tracer**

> Target akhir pertemuan: Topologi dua switch dari Pertemuan 2 digantikan satu switch dengan dua VLAN, dan Anda dapat membuktikan VLAN benar-benar memisahkan lalu lintas.

---

## Tujuan

Mahasiswa memahami VLAN sebagai pemisah logis dan bisa menggantikan pemisahan fisik (2 switch) pada Pertemuan 2 menjadi 1 switch dengan 2 VLAN.

---

## Perlengkapan Praktikum

- Cisco Packet Tracer

---

### 1. Cara Kerja Switch (Fondasi Sebelum VLAN)

Switch adalah perangkat yang menghubungkan banyak perangkat dalam satu jaringan lokal. Cara kerjanya cerdas: switch **belajar** alamat MAC dari setiap perangkat yang terhubung ke port-nya, lalu menyimpannya di sebuah tabel bernama **MAC Address Table** (atau CAM Table).

Alur yang terjadi saat PC-A mengirim data ke PC-B:
1. Switch melihat MAC address tujuan di data kiriman PC-A.
2. Switch mencari di MAC Address Table-nya: *"Port berapa yang punya MAC address PC-B?"*
3. Jika ditemukan → data dikirim **langsung ke port itu saja**, tidak disebar ke semua port.
4. Jika belum ditemukan → data di-*flood* ke semua port kecuali port asal, sambil menunggu balasan untuk belajar lokasinya.

> **Poin penting:** Berbeda dari *hub* jaman dulu yang meneruskan data ke **semua** port tanpa kecuali, switch jauh lebih efisien karena data hanya sampai ke tujuan yang benar.

---

### 2. Kenapa VLAN Dibutuhkan

Bayangkan kantor dengan 1 switch besar yang menghubungkan 50 komputer dari berbagai divisi (Staf, HRD, Keuangan, Tamu). Tanpa VLAN:
- Semua 50 komputer berada dalam **satu jaringan besar** yang sama.
- Siaran (*broadcast*) dari satu komputer akan menjangkau seluruh 50 komputer lainnya — membuang-buang bandwidth.
- Tamu bisa mengakses data yang seharusnya hanya untuk Staf.

Dengan VLAN, satu switch fisik dipecah menjadi **beberapa jaringan logis yang terisolasi**. Analoginya: **satu gedung apartemen dengan beberapa lantai yang berbeda pintu masuk** — orang di lantai 3 tidak bisa masuk ke unit lantai 7 meskipun mereka ada di gedung yang sama.

---

### 3. VLAN ID — Tanda Pengenal Tiap VLAN

Setiap VLAN diberi nomor identitas yang disebut **VLAN ID**, bernilai antara 1 sampai 4094. VLAN ID inilah yang dipakai switch (dan router) untuk membedakan lalu lintas antar-VLAN. Di modul ini dipakai:
- **VLAN 10** untuk divisi **Staf**
- **VLAN 20** untuk divisi **Tamu**

---

### 4. Jenis Port di Switch: Access vs Trunk

| Jenis Port | Hanya Membawa | Ditandai VLAN? | Dipakai untuk |
|---|---|---|---|
| **Access** | 1 VLAN saja | Tidak — data masuk/keluar tanpa label | Menyambungkan PC/end-device ke switch |
| **Trunk** | Banyak VLAN sekaligus | Ya — setiap frame diberi tag VLAN ID (802.1Q) | Menyambungkan switch ke switch, atau switch ke router |

> **Mengapa trunk port perlu ada tag?**  
> Ketika kabel trunk membawa data dari VLAN 10 dan VLAN 20 secara bersamaan, perlu ada cara untuk membedakan keduanya di ujung penerima. Standar **802.1Q** menyisipkan tag 4-byte ke dalam frame Ethernet berisi VLAN ID, sehingga perangkat di ujung lain tahu data ini milik VLAN berapa.

---

### 5. Yang Perlu Diingat: VLAN Memisahkan, Bukan Mengamankan Sepenuhnya

VLAN yang berbeda tidak bisa saling berkomunikasi secara langsung — **tapi ini bukan berarti aman selamanya**. Begitu ada router yang menghubungkan kedua VLAN (yang kita lakukan di Pertemuan 4), isolasi itu hilang lagi dan seluruh divisi bisa saling berkomunikasi. Untuk membatasi kembali komunikasi antar-VLAN secara selektif, dibutuhkan **ACL** (Pertemuan 5).

## Langkah Praktikum

1. **Buat VLAN di Switch**  
   Buka CLI Switch, buat VLAN 10 (Staf) dan VLAN 20 (Tamu):
   ```ios
   Switch> enable
   Switch# configure terminal
   Switch(config)# vlan 10
   Switch(config-vlan)# name Staf
   Switch(config-vlan)# exit
   Switch(config)# vlan 20
   Switch(config-vlan)# name Tamu
   Switch(config-vlan)# exit
   ```

2. **Alokasikan Port Access ke VLAN**  
   Daftarkan port FastEthernet0/1 dan Fa0/2 ke VLAN 10, serta Fa0/3 dan Fa0/4 ke VLAN 20:
   ```ios
   Switch(config)# interface range fa0/1-2
   Switch(config-if-range)# switchport mode access
   Switch(config-if-range)# switchport access vlan 10
   Switch(config-if-range)# exit
   Switch(config)# interface range fa0/3-4
   Switch(config-if-range)# switchport mode access
   Switch(config-if-range)# switchport access vlan 20
   Switch(config-if-range)# exit
   ```

3. **Verifikasi VLAN**  
   Jalankan perintah `show vlan brief` di Switch untuk memastikan port sudah terasosiasi dengan VLAN yang benar.

4. **Uji Konektivitas Antar-VLAN (Pengujian Isolasi)**  
   - Lakukan `ping` dari PC Staf 1 ke PC Staf 2 (VLAN 10 yang sama). Hasil harus **Reply / Sukses**.
   - Lakukan `ping` dari PC Staf 1 ke PC Tamu 1 (VLAN 20 yang beda). Hasil harus **Request Timed Out / Gagal**. Hal ini membuktikan bahwa VLAN berhasil mengisolasi lalu lintas data secara logis pada satu switch.

---

## Tugas 3

Bangun topologi 1 Switch + 4 PC yang terbagi dalam 2 VLAN (VLAN 10 Staf & VLAN 20 Tamu).

**Ketentuan:**
1. Konfigurasikan port access switch sesuai pembagian divisi.
2. Buktikan isolasi jaringan: PC sesama VLAN dapat saling ping, sedangkan PC beda VLAN tidak dapat terhubung.

**Format Pengumpulan Tugas:**
Mahasiswa mengumpulkan arsip file `.zip` dengan format nama `DMJK_A_P03_<NIM>_<NamaLengkap>.zip` yang berisi:
1. File simulasi `.pkt` (nama file: `DMJK_A_P03_<NIM>_<NamaLengkap>.pkt`)
2. Screenshot hasil uji ping sukses (sesama VLAN) dan gagal (antar VLAN) serta output `show vlan brief`.
3. Laporan ringkas `.pdf` (nama file: `DMJK_A_P03_<NIM>_<NamaLengkap>.pdf`) disusun mengacu pada [Template Laporan Praktikum](https://docs.google.com/document/d/1ChvPwSa-9h_i8z8RE195jK_iNLz7sTdK/edit?usp=drivesdk&ouid=101845457565241443935&rtpof=true&sd=true).

---

**Pertemuan selanjutnya:** [04 — Routing Dasar & Inter-VLAN Routing](04-pertemuan-4-routing-dasar.md)
