# DMJK Kelas A — Daftar Modul dan Cara Memakainya

**Desain dan Manajemen Jaringan Komputer | SI2514011 | 4 SKS**
**Perangkat: Cisco Packet Tracer**

---

## Bacalah berurutan

| File | Pertemuan | Topik | Sub-CPMK |
|---|:-:|---|:-:|
| [01-pertemuan-1-konsep-dasar.md](01-pertemuan-1-konsep-dasar.md) | 1 | Konsep dasar jaringan & pengenalan Packet Tracer | 1 |
| [02-pertemuan-2-addressing-subnetting.md](02-pertemuan-2-addressing-subnetting.md) | 2 | Pengalamatan IP & subnetting dasar | 2 |
| [03-pertemuan-3-vlan-switching.md](03-pertemuan-3-vlan-switching.md) | 3 | VLAN & switching | 3 |
| [04-pertemuan-4-routing-dasar.md](04-pertemuan-4-routing-dasar.md) | 4 | Inter-VLAN Routing & DHCP Server | 3 |
| [05-pertemuan-5-dhcp-acl.md](05-pertemuan-5-dhcp-acl.md) | 5 | Access Control List (ACL) & Network Security | 3 |

Baca modul sebelum sesi praktikum. Bagian **Materi Singkat** dirancang untuk dibaca lebih dulu; bagian **Langkah Praktikum** dan **Tugas** dikerjakan di laboratorium.

## Lampiran

| File | Dipakai untuk |
|---|---|
| [lampiran/A-command-reference.md](lampiran/A-command-reference.md) | Kumpulan perintah Cisco IOS CLI yang dipakai sepanjang 5 pertemuan |
| [Template Laporan Praktikum](https://docs.google.com/document/d/1ChvPwSa-9h_i8z8RE195jK_iNLz7sTdK/edit?usp=drivesdk&ouid=101845457565241443935&rtpof=true&sd=true) | Format laporan resmi (.pdf) yang dikumpulkan setiap tugas praktikum |

---

## Yang Perlu Diketahui Sebelum Pertemuan 1

**1. Parameter jaringan sama untuk semua mahasiswa.** Tidak ada nomor urut atau NIM yang mengubah alamat IP/VLAN Anda. Studi kasus (kantor kecil, divisi Staf dan Tamu, blok `192.168.10.0/24`) sama untuk seluruh kelas mulai pertemuan 2.

**2. Setiap pertemuan berdiri sendiri, tetapi nyambung.** Pertemuan 2 sampai 5 memakai topologi dan IP plan yang sama, dikembangkan bertahap: subnetting (P2) → dipisah VLAN (P3) → disambung ulang lewat router & diberi DHCP (P4) → dibatasi keamanan dengan Extended ACL (P5).

**3. Setiap pertemuan ada tugas.** Dikumpulkan berupa file `.pkt` (Packet Tracer), screenshot verifikasi, dan laporan ringkas `.pdf` mengacu pada [Template Laporan Praktikum](https://docs.google.com/document/d/1ChvPwSa-9h_i8z8RE195jK_iNLz7sTdK/edit?usp=drivesdk&ouid=101845457565241443935&rtpof=true&sd=true).

**4. Setelah pertemuan 5 ada tugas besar.** Materinya akan diberikan langsung oleh dosen — modul ini adalah fondasi sebelum itu.

## Yang Dikumpulkan Setiap Pertemuan

Mahasiswa mengumpulkan arsip `.zip` (`DMJK_A_P<NomorPekan>_<NIM>_<NamaLengkap>.zip`) berisi:
1. File `.pkt` hasil praktikum/tugas
2. Screenshot bukti verifikasi (ping, `show` command yang diminta)
3. Laporan ringkas `.pdf` disusun mengacu pada [Template Laporan Praktikum](https://docs.google.com/document/d/1ChvPwSa-9h_i8z8RE195jK_iNLz7sTdK/edit?usp=drivesdk&ouid=101845457565241443935&rtpof=true&sd=true)

