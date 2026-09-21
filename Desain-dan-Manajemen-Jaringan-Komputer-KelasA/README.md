# DMJK — Desain dan Manajemen Jaringan Komputer (Kelas A)

**SI2514011 | 4 SKS | Semester 3 | Ganjil 2026/2027 | Sistem Informasi ITK | Cisco Packet Tracer**

Modul praktikum untuk 5 pertemuan pertama Kelas A. Disusun sebagai fondasi awal pembelajaran, sebelum masuk ke Tugas Besar

---

## Deskripsi Mata Kuliah (CPMK 1 di SIMKUR)

> Mampu menganalisis arsitektur, metodologi desain jaringan, dan manajemen infrastruktur jaringan termasuk protokol komunikasi, jaringan nirkabel, IoT, dan teknologi generasi terbaru untuk mendukung kebutuhan organisasi. (C4)

---

## Capaian yang Disasar 5 Pertemuan Ini

Mengacu pada CPMK-1 resmi mata kuliah (lihat [kurikulum.itk.ac.id/guest/silabus/3720](https://kurikulum.itk.ac.id/guest/silabus/3720)):

| Sub-CPMK | Level | Bunyi Singkat | Disentuh di Pertemuan |
|---|:---:|---|:---:|
| **Sub-CPMK-1** | C4 | Konsep fundamental & analisis paket | 1 |
| **Sub-CPMK-2** | C3 | Addressing IPv4 & subnetting | 2 |
| **Sub-CPMK-3** | C3 | Switching, routing, layanan jaringan (DHCP), kontrol akses | 3, 4, 5 |

*Catatan:* Sub-CPMK-4 (diagnosis), Sub-CPMK-5 (wireless/IoT/cloud), dan Sub-CPMK-6 (integrasi proyek enterprise) **tidak** ada karena itu sudah masuk studi kasus.

---

## Susunan 5 Pertemuan Praktikum

| Pertemuan | Topik Utama | Sub-CPMK | Modul Mahasiswa | Luaran & Pengumpulan |
|:---:|---|:---:|---|---|
| **1** | Konsep Dasar Jaringan & Pengenalan Packet Tracer | 1 | [01-pertemuan-1-konsep-dasar.md](mahasiswa/01-pertemuan-1-konsep-dasar.md) | File `.pkt` (4 PC + 1 Switch) + Screenshot Ping |
| **2** | Pengalamatan IPv4 & Subnetting Dasar | 2 | [02-pertemuan-2-addressing-subnetting.md](mahasiswa/02-pertemuan-2-addressing-subnetting.md) | File `.pkt` (2 Subnet + Router) + Laporan PDF |
| **3** | VLAN & Switching | 3 | [03-pertemuan-3-vlan-switching.md](mahasiswa/03-pertemuan-3-vlan-switching.md) | File `.pkt` (1 Switch 2 VLAN) + CLI `show vlan brief` |
| **4** | Routing Dasar & Inter-VLAN (Router-on-a-Stick) | 3 | [04-pertemuan-4-routing-dasar.md](mahasiswa/04-pertemuan-4-routing-dasar.md) | File `.pkt` + CLI `show ip route` + Laporan PDF |
| **5** | Layanan DHCP & Access Control List (ACL) Dasar | 3 | [05-pertemuan-5-dhcp-acl.md](mahasiswa/05-pertemuan-5-dhcp-acl.md) | File `.pkt` final + CLI `show access-lists` + Laporan PDF |

---

## Studi Kasus Praktikum

Modul pertemuan 2–5 memakai satu studi kasus ringan yang berkesinambungan: kantor kecil dengan dua divisi, **Staf** (kebutuhan 20 host) dan **Tamu** (kebutuhan 10 host), dialokasikan dari blok jaringan `192.168.10.0/24`. Parameter ini **sama untuk seluruh mahasiswa Kelas A** (tidak diturunkan dari NIM atau nomor urut).

| Divisi | Kebutuhan Host | Prefix | Alokasi Blok Subnet | Gateway | Rentang Host Usable |
|---|:---:|:---:|---|---|---|
| **Staf** | 20 | `/27` | `192.168.10.0/27` | `192.168.10.1` | `.2` – `.30` |
| **Tamu** | 10 | `/28` | `192.168.10.32/28` | `192.168.10.33` | `.34` – `.46` |

---

## Struktur Folder

```
mahasiswa/
├── 00-daftar-modul.md              — Indeks dan alur penggunaan modul praktikum
├── 01-pertemuan-1-konsep-dasar.md
├── 02-pertemuan-2-addressing-subnetting.md
├── 03-pertemuan-3-vlan-switching.md
├── 04-pertemuan-4-routing-dasar.md
├── 05-pertemuan-5-dhcp-acl.md
└── lampiran/
    ├── A-command-reference.md      — Referensi perintah Cisco IOS CLI yang dipakai
    └── gambar/                     — Aset gambar diagram & visualisasi modul
```

---

## Standar Pembagian Berkas (File Distribution)

| Kategori Berkas | Lokasi | Akses Mahasiswa |
|---|---|:---:|
| **Modul Praktikum Mahasiswa** | `mahasiswa/` | **Publik** |
| **Lampiran Command Reference** | `mahasiswa/lampiran/` | **Publik** |


---

## Alur Praktikum & Pengumpulan Tugas

1. **Pra-Praktikum:** Mahasiswa membaca bagian Materi Singkat pada modul pertemuan terkait sebelum sesi lab dimulai.
2. **Hands-On Lab:** Mahasiswa merangkai topologi dan mengeksekusi langkah konfigurasi pada Cisco Packet Tracer sesuai panduan.
3. **Verifikasi Teknis:** Mahasiswa menguji fungsionalitas jaringan menggunakan perintah verifikasi CLI serta pengujian ping.
4. **Pengumpulan Tugas Mingguan (Submission):**
   Mahasiswa mengumpulkan berkas `.zip` yang berisi berkas simulasi `.pkt`, screenshot pembuktian, dan laporan ringkas `.pdf` mengikuti :  
   **[Template Laporan Praktikum](https://docs.google.com/document/d/1ChvPwSa-9h_i8z8RE195jK_iNLz7sTdK/edit?usp=drivesdk&ouid=101845457565241443935&rtpof=true&sd=true)**

### Format Penamaan Berkas

Seluruh pengumpulan tugas praktikum wajib mematuhi standar format penamaan:

| Jenis Berkas | Ekstensi | Format Penamaan | Contoh |
|---|---|---|---|
| **Compress ZIP** | `.zip` | `DMJK_A_P<NomorPekan>_<NIM>_<NamaLengkap>.zip` | `DMJK_A_P02_10241066_ShabrinaPutriAz-Zahra.zip` |
| **File Simulasi Packet Tracer** | `.pkt` | `DMJK_A_P<NomorPekan>_<NIM>_<NamaLengkap>.pkt` | `DMJK_A_P02_10241066_ShabrinaPutriAz-Zahra.pkt` |
| **Laporan Mingguan** | `.pdf` | `DMJK_A_P<NomorPekan>_<NIM>_<NamaLengkap>.pdf` | `DMJK_A_P02_10241066_ShabrinaPutriAz-Zahra.pdf` |

---

## Kebijakan Penggunaan Tools (AI)

1. **AI sebagai Asisten Belajar (Dianjurkan):** Mahasiswa diperkenankan memanfaatkan model AI (ChatGPT, Claude, Gemini, Copilot) untuk memahami konsep teoritis dan menganalisis pesan kesalahan (*error debugging*).
2. **Batasan & Integritas:** Dilarang keras menyalin utuh laporan tanpa pemahaman konsep.
3. **Pertanggungjawaban:** Mahasiswa harus dapat menjelaskan setiap baris perintah CLI dan topologi yang diajukan pada Laporan maupun Ujian.

---

## Informasi Asisten Dosen & Narahubung

- **Asisten Pengampu:** Muhammad Ikhsyan dan Shabrina Putri Az-Zahra
- **Program Studi:** Sistem Informasi — Institut Teknologi Kalimantan (ITK)
- **Repo Utama:** [Asisten-Dosen-ITK](../README.md)
- **Komunikasi:** WA Asisten

---
