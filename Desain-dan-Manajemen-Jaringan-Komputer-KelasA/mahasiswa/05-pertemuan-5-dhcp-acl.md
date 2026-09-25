# PERTEMUAN 5 — Access Control List (ACL) & Network Security

**SI2514011 | Sub-CPMK-3 (C3) | Cisco Packet Tracer**

> Target akhir pertemuan: Anda memahami konsep pengamanan lalu lintas jaringan menggunakan Access Control List (ACL), perbedaan Standard dan Extended ACL, serta mampu mengimplementasikan Extended ACL di router untuk menyaring dan membatasi akses antar-VLAN.

---

## Tujuan

1. Mahasiswa memahami konsep pengamanan lalu lintas jaringan menggunakan Access Control List (ACL).
2. Mahasiswa memahami perbedaan antara Standard ACL dan Extended ACL.
3. Mahasiswa mampu mengimplementasikan ACL di router untuk menyaring (*filter*) dan membatasi akses antar-VLAN atau menuju layanan tertentu.
4. Mahasiswa mampu melakukan *troubleshooting* dasar pada konfigurasi jaringan yang menerapkan ACL.

---

## Perlengkapan Praktikum

- Cisco Packet Tracer

---

## Materi Singkat Terkait

### 1. Apa Itu Access Control List (ACL)?

Access Control List (ACL) adalah sekumpulan aturan (*rules*) yang digunakan oleh router untuk mengevaluasi dan mengontrol lalu lintas data (*traffic*) yang melintas. Aturan ACL dapat berupa **permit** (mengizinkan) atau **deny** (menolak) paket berdasarkan kriteria tertentu.

### 2. Jenis-Jenis ACL

| Jenis ACL | Rentang Nomor / Tipe | Kriteria Penyaringan | Penempatan Terbaik |
|---|---|---|---|
| **Standard ACL** | 1 – 99 & 1300 – 1999 | Hanya memeriksa Alamat IP Asal (*Source IP*). | Ditempatkan sesedikit/sedekat mungkin dengan Tujuan (*Destination*). |
| **Extended ACL** | 100 – 199 & 2000 – 2699 | Memeriksa *Source IP*, *Destination IP*, Protokol (TCP/UDP/ICMP), dan *Port Number*. | Ditempatkan sedekat mungkin dengan Sumber (*Source*). |

#### Wildcard Mask — Penentu Rentang IP
Saat mendefinisikan aturan ACL, rentang alamat IP ditentukan menggunakan **wildcard mask** (kebalikan bit dari subnet mask):
- Subnet mask `/27` (`255.255.255.224`) $\rightarrow$ Wildcard mask: `0.0.0.31`
- Subnet mask `/28` (`255.255.255.240`) $\rightarrow$ Wildcard mask: `0.0.0.15`

### 3. Aturan Emas ACL (Implicit Deny All)

Setiap baris daftar ACL dievaluasi secara berurutan dari atas ke bawah (*top-down*). Jika ada paket yang cocok (*match*) dengan suatu baris aturan, router akan mengeksekusi tindakan (*permit/deny*) dan menghentikan pencocokan baris berikutnya.

> **PENTING (Implicit Deny):**  
> Di bagian paling akhir dari setiap ACL, terdapat aturan tersembunyi (*implicit rule*) yaitu **`deny ip any any`** (menolak seluruh paket yang tidak cocok dengan aturan di atasnya).  
> Oleh karena itu, jika membuat aturan *deny*, pastikan untuk menambahkan aturan **`permit ip any any`** di akhirnya agar *traffic* lain tidak ikut terblokir!

---

## Langkah Praktikum

**Studi Kasus Keamanan:**  
Divisi Tamu (VLAN 20) tidak boleh mengakses IP PC Staf (VLAN 10), tetapi Tamu tetap boleh mengakses Internet / Gateway mereka sendiri (`192.168.10.33`).

### 1. Konfigurasi Extended ACL di Router

Buat aturan Extended ACL dengan nomor **100** di Router:

```ios
Router> enable
Router# configure terminal

! Blokir ICMP/IP dari network Tamu (192.168.10.32/28) ke network Staf (192.168.10.0/27)
Router(config)# access-list 100 deny ip 192.168.10.32 0.0.0.15 192.168.10.0 0.0.0.31

! Izinkan sisa traffic lainnya dari Tamu
Router(config)# access-list 100 permit ip any any
```

### 2. Terapkan ACL pada Sub-Interface Router

Pasang ACL 100 pada sub-interface VLAN 20 (`Gi0/0.20`) untuk arah paket masuk (*inbound / in*):

```ios
Router(config)# interface gigabitEthernet 0/0.20
Router(config-subif)# ip access-group 100 in
Router(config-subif)# exit
```

### 3. Pengujian ACL & Troubleshooting

1. **Uji dari PC Tamu ke PC Staf:**  
   Lakukan `ping` dari PC Tamu ke salah satu PC Staf. Hasil harus **Destination Host Unreachable** (ditolak oleh ACL router).
2. **Uji dari PC Tamu ke Gateway:**  
   Lakukan `ping` dari PC Tamu ke Gateway mereka (`192.168.10.33`). Hasil harus **Reply / Sukses**.
3. **Uji dari PC Staf ke PC Tamu:**  
   Lakukan `ping` dari PC Staf ke PC Tamu. Hasil tetap **Reply** (karena ACL hanya dipasang pada *traffic* masuk di sub-interface VLAN 20).
4. **Verifikasi Pencocokan Paket:**  
   Jalankan perintah berikut di CLI Router untuk melihat statistik paket yang terkena aturan ACL:
   ```ios
   Router# show access-lists
   ```
   Perhatikan counter (*hit count*) pada baris `deny` yang bertambah ketika PC Tamu mencoba mengirimkan paket ke Staf.

---

## Tugas 5

Terapkan Extended ACL pada jaringan yang telah dibangun di Pertemuan 4.

**Ketentuan:**
1. Divisi Tamu dibatasi sehingga tidak dapat melakukan ping ke seluruh PC Staf, tetapi tetap dapat melakukan ping ke Gateway mereka sendiri (`192.168.10.33`).
2. Tampilkan output `show access-lists` pada laporan praktikum untuk membuktikan adanya *hit count* paket yang diblokir.

**Format Pengumpulan Tugas:**
Mahasiswa mengumpulkan arsip file `.zip` dengan format nama `DMJK_A_P05_<NIM>_<NamaLengkap>.zip` yang berisi:
1. File simulasi `.pkt` (nama file: `DMJK_A_P05_<NIM>_<NamaLengkap>.pkt`)
2. Screenshot hasil uji ping sebelum dan sesudah dipasang ACL serta output `show access-lists`.
3. Laporan ringkas `.pdf` (nama file: `DMJK_A_P05_<NIM>_<NamaLengkap>.pdf`) disusun mengacu pada [Template Laporan Praktikum](https://docs.google.com/document/d/1ChvPwSa-9h_i8z8RE195jK_iNLz7sTdK/edit?usp=drivesdk&ouid=101845457565241443935&rtpof=true&sd=true).

---

**Selanjutnya:** Tugas Besar — materi dan ketentuan akan diberikan langsung oleh dosen pengampu.
