# PERTEMUAN 4 — Routing Dasar & Inter-VLAN Routing

**SI2514011 | Sub-CPMK-3 (C3) | Cisco Packet Tracer**

> Target akhir pertemuan: VLAN Staf dan Tamu dari Pertemuan 3 tersambung kembali lewat satu router (router-on-a-stick), dan Anda dapat membaca tabel routing sederhana.

---

## Tujuan

Mahasiswa bisa menyambungkan kembali VLAN Staf dan Tamu memakai router-on-a-stick, dan memahami dasar static/directly-connected routing.

## Materi : Routing Dasar & Inter-VLAN Routing

### 1. Apa Itu Routing dan Kenapa Dibutuhkan

Di Pertemuan 3, VLAN 10 (Staf) dan VLAN 20 (Tamu) berhasil terisolasi — artinya keduanya tidak bisa saling berkomunikasi. Tapi di dunia nyata, ada kalanya antar-divisi **tetap harus bisa berkomunikasi** (misal: PC Staf perlu mengakses printer di jaringan Tamu).

Di sinilah **routing** berperan. Routing adalah proses memilih jalur terbaik untuk meneruskan paket data **dari satu jaringan ke jaringan lain yang berbeda** — persis seperti GPS yang memilih rute antarkota. Perangkat yang menjalankan routing disebut **router**.

> **Analogi:** Jika switch adalah pengelola lalu lintas *di dalam* satu kota, maka router adalah pengelola lalu lintas *antarkota*.

---

### 2. Tiga Jenis Routing (Konteks Modul Ini)

| Jenis | Cara Router Tahu Jalurnya | Contoh |
|---|---|---|
| **Directly Connected** | Otomatis diketahui saat interface aktif dan diberi IP | Router mengetahui jaringan `192.168.10.0/27` karena salah satu interface-nya ada di jaringan itu |
| **Static Route** | Dimasukkan manual oleh admin jaringan | `ip route 10.0.0.0 255.0.0.0 192.168.1.1` |
| **Dynamic Route** | Dipelajari otomatis lewat protokol routing (OSPF, EIGRP, RIP) | Tidak dibahas di modul ini |

Di pertemuan ini, kita hanya berurusan dengan **Directly Connected** — router otomatis mengetahui kedua jaringan VLAN karena sub-interface-nya masing-masing terhubung langsung.

---

### 3. Router-on-a-Stick: Satu Kabel, Banyak VLAN

**Masalahnya:** Kita punya 2 VLAN (Staf dan Tamu). Solusi naif adalah memakai 2 interface fisik router — satu per VLAN. Tapi bagaimana jika VLAN-nya ada 10 atau 20? Tidak ada router yang punya 20 interface fisik.

**Solusinya: Router-on-a-Stick.** Hanya dibutuhkan **1 kabel fisik** dari switch ke router. Kabel tersebut dikonfigurasi sebagai **trunk** (membawa semua VLAN). Di sisi router, satu interface fisik dipecah menjadi beberapa **sub-interface** — masing-masing melayani satu VLAN.

```
Switch (trunk) ──── Gi0/0 (fisik)
                      ├── Gi0/0.10  →  melayani VLAN 10 (Staf)
                      └── Gi0/0.20  →  melayani VLAN 20 (Tamu)
```

---

### 4. Sub-Interface dan `encapsulation dot1Q`

Sub-interface adalah "interface virtual" yang dibuat di atas satu interface fisik. Penulisannya: `Gi0/0.10` artinya sub-interface nomor 10 di interface fisik `Gi0/0`.

> **Perhatian kritis:** Nomor sub-interface (`.10`) hanyalah **label untuk memudahkan manusia membaca**, bukan penentu VLAN. Yang benar-benar menentukan VLAN mana yang dilayani adalah perintah `encapsulation dot1Q <vlan-id>`.  
> Contoh: `Gi0/0.10` dengan `encapsulation dot1Q 20` akan melayani VLAN 20, bukan VLAN 10. Ini valid tapi sangat membingungkan — **selalu buat nomor sub-interface sama dengan VLAN ID-nya**.

---

### 5. Membaca Output `show ip route`

Saat menjalankan `show ip route` di router, ada beberapa kode prefix di setiap baris:

| Kode | Artinya |
|---|---|
| `C` | *Directly Connected* — jaringan ini terhubung langsung ke interface router |
| `L` | *Local* — alamat IP interface router itu sendiri |
| `S` | *Static* — rute yang dimasukkan manual oleh admin |
| `O` | *OSPF* — rute yang dipelajari dari protokol OSPF |

Di pertemuan ini, Anda akan melihat dua baris `C` (satu untuk subnet Staf, satu untuk subnet Tamu) — ini artinya router sudah otomatis mengetahui kedua jaringan tersebut karena sub-interface-nya aktif.

## Langkah Praktikum

1. Lanjutkan topologi VLAN Pertemuan 3, tambahkan 1 router, sambungkan ke switch dengan **satu** kabel
2. Ubah port switch yang tersambung ke router menjadi trunk:
   ```
   interface fastEthernet0/5
    switchport mode trunk
    switchport trunk allowed vlan 10,20
   ```
3. Konfigurasi sub-interface di router:
   ```
   interface gigabitEthernet0/0
    no shutdown
   interface gigabitEthernet0/0.10
    encapsulation dot1Q 10
    ip address 192.168.10.1 255.255.255.224
   interface gigabitEthernet0/0.20
    encapsulation dot1Q 20
    ip address 192.168.10.33 255.255.255.240
   ```
4. Set gateway di PC Staf ke `192.168.10.1` dan PC Tamu ke `192.168.10.33`
5. Uji `ping` PC Staf ↔ PC Tamu — sekarang **harus berhasil** lewat router (bandingkan dengan Pertemuan 3 yang gagal tanpa router)
6. Jalankan `show ip route` di router, amati dua baris `C` untuk kedua VLAN

## Tugas 4

Terapkan router-on-a-stick pada topologi Tugas 3 (VLAN Staf dan Tamu Anda sendiri).

**Buktikan:** PC Staf dan PC Tamu sekarang bisa saling `ping` lewat router.

**Format Pengumpulan Tugas:**
Mahasiswa mengumpulkan arsip file `.zip` dengan format nama `DMJK_A_P04_<NIM>_<NamaLengkap>.zip` yang berisi:
1. File simulasi `.pkt` (nama file: `DMJK_A_P04_<NIM>_<NamaLengkap>.pkt`)
2. Output command `show ip route`
3. Screenshot hasil ping berhasil antar VLAN (Staf ↔ Tamu)
4. Laporan ringkas `.pdf` (nama file: `DMJK_A_P04_<NIM>_<NamaLengkap>.pdf`) disusun mengacu pada [Template Laporan Praktikum](https://docs.google.com/document/d/1ChvPwSa-9h_i8z8RE195jK_iNLz7sTdK/edit?usp=drivesdk&ouid=101845457565241443935&rtpof=true&sd=true).

---

**Pertemuan selanjutnya:** [05 — Layanan DHCP & Access Control List Dasar](05-pertemuan-5-dhcp-acl.md)
