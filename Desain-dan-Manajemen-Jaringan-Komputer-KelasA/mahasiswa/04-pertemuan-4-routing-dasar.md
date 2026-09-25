# PERTEMUAN 4 — Inter-VLAN Routing & DHCP Server

**SI2514011 | Sub-CPMK-3 (C3) | Cisco Packet Tracer**

> Target akhir pertemuan: Anda memahami konsep Inter-VLAN Routing metode Router-on-a-Stick (RoaS) dan mampu mengonfigurasi layanan DHCP Server pada router agar PC client mendapatkan IP secara otomatis serta dapat saling berkomunikasi antar-VLAN.

---

## Tujuan

1. Mahasiswa memahami konsep dasar Inter-VLAN Routing menggunakan metode Router-on-a-Stick (RoaS) dengan sub-interface dan enkapsulasi 802.1Q.
2. Mahasiswa mampu mengkonfigurasi port Trunk pada switch dan sub-interface pada router.
3. Mahasiswa memahami dan mampu mengkonfigurasi layanan DHCP Server pada Cisco Router agar PC client mendapatkan IP address secara otomatis sesuai VLAN masing-masing.

---

## Perlengkapan Praktikum

- Cisco Packet Tracer

---

## Materi Singkat Terkait

### 1. Inter-VLAN Routing (Router-on-a-Stick)

Di Pertemuan 3, VLAN berfungsi memisahkan dan mengisolasi jaringan. Namun dalam dunia nyata, antar-divisi sering kali tetap membutuhkan komunikasi yang dikontrol. Karena VLAN bekerja pada Layer 2 (*Data Link*), dibutuhkan perangkat Layer 3 (*Router*) untuk menghubungkan antar-VLAN.

Metode paling efisien adalah **Router-on-a-Stick (RoaS)**, di mana satu link fisik (kabel) antara Switch dan Router dikonfigurasi sebagai jalur **Trunk**. Interface fisik router dipecah secara logis menjadi beberapa **sub-interface** (misalnya `Gi0/0.10` dan `Gi0/0.20`), di mana masing-masing sub-interface bertindak sebagai *Default Gateway* untuk VLAN yang bersangkutan.

```
Switch (port Fa0/24 Trunk) ──── Gi0/0 (fisik Router)
                                  ├── Gi0/0.10  →  Gateway VLAN 10 (Staf: 192.168.10.1)
                                  └── Gi0/0.20  →  Gateway VLAN 20 (Tamu: 192.168.10.33)
```

### 2. Enkapsulasi IEEE 802.1Q

Agar router dapat mengenali paket data berasal dari VLAN mana, setiap frame data yang melintasi jalur Trunk diberi label (*tagging*) nomor VLAN ID menggunakan standar **IEEE 802.1Q**. Sub-interface router dikonfigurasi dengan perintah `encapsulation dot1Q <vlan-id>` sesuai dengan ID VLAN masing-masing.

### 3. Dynamic Host Configuration Protocol (DHCP)

Mengisi alamat IP secara manual (statis) pada ratusan PC sangat tidak efisien. DHCP memungkinkan router/server membagikan IP Address, Subnet Mask, Default Gateway, dan DNS Server secara otomatis (*dynamic*) kepada *client*.

Di Cisco Router, alokasi IP dibuat dalam bentuk **DHCP Pool** untuk tiap subnet/VLAN:
- **DHCP Pool:** Rentang alokasi IP otomatis untuk suatu network/VLAN.
- **Excluded Address:** Alamat IP yang dikecualikan (seperti IP Gateway) agar tidak disewakan ke PC client guna menghindari konflik IP.

| Perangkat | Peran dalam RoaS & DHCP |
|---|---|
| **Switch (Port Trunk)** | Membawa paket dari multiple VLAN melintasi satu jalur kabel ke router dengan tag 802.1Q. |
| **Router (Sub-Interface)** | Menjadi gateway logis untuk tiap VLAN dan meneruskan (*route*) paket antar-VLAN. |
| **DHCP Pool Router** | Menyediakan rentang alamat IP otomatis sesuai subnet VLAN masing-masing client. |

---

## Langkah Praktikum

### 1. Konfigurasi Port Trunk pada Switch

Hubungkan port `FastEthernet0/24` Switch ke interface `GigabitEthernet0/0` Router. Set port `Fa0/24` sebagai Trunk:

```ios
Switch> enable
Switch# configure terminal
Switch(config)# interface fa0/24
Switch(config-if)# switchport mode trunk
Switch(config-if)# exit
```

### 2. Konfigurasi Sub-Interface Router (Router-on-a-Stick)

Aktifkan interface utama router, lalu buat sub-interface untuk VLAN 10 dan VLAN 20:

```ios
Router> enable
Router# configure terminal
Router(config)# interface gigabitEthernet 0/0
Router(config-if)# no shutdown
Router(config-if)# exit

! Sub-interface VLAN 10 (Staf)
Router(config)# interface gigabitEthernet 0/0.10
Router(config-subif)# encapsulation dot1Q 10
Router(config-subif)# ip address 192.168.10.1 255.255.255.224
Router(config-subif)# exit

! Sub-interface VLAN 20 (Tamu)
Router(config)# interface gigabitEthernet 0/0.20
Router(config-subif)# encapsulation dot1Q 20
Router(config-subif)# ip address 192.168.10.33 255.255.255.240
Router(config-subif)# exit
```

### 3. Konfigurasi DHCP Server di Router

Konfigurasikan DHCP Pool untuk VLAN 10 dan VLAN 20:

```ios
! Exclude IP Gateway agar tidak disewakan ke client
Router(config)# ip dhcp excluded-address 192.168.10.1
Router(config)# ip dhcp excluded-address 192.168.10.33

! Pool VLAN 10
Router(config)# ip dhcp pool POOL_STAF
Router(dhcp-config)# network 192.168.10.0 255.255.255.224
Router(dhcp-config)# default-router 192.168.10.1
Router(dhcp-config)# exit

! Pool VLAN 20
Router(config)# ip dhcp pool POOL_TAMU
Router(dhcp-config)# network 192.168.10.32 255.255.255.240
Router(dhcp-config)# default-router 192.168.10.33
Router(dhcp-config)# exit
```

### 4. Uji Konfigurasi DHCP Client & Ping

- Di PC Staf & PC Tamu, ubah pengaturan IP dari **Static** ke **DHCP**. Pastikan IP Address dan Gateway didapatkan secara otomatis sesuai subnet VLAN masing-masing.
- Uji konektivitas `ping` dari PC Staf ke PC Tamu. Hasil harus **Reply** (berhasil terhubung antar-VLAN melalui router).

---

## Tugas 4

Konfigurasikan Inter-VLAN Routing (RoaS) dan DHCP Server di Cisco Packet Tracer sesuai subnetting dari Studi Kasus Pertemuan 2.

**Ketentuan:**
1. Semua PC harus mendapatkan IP secara otomatis via DHCP.
2. PC di VLAN 10 dan VLAN 20 harus sukses melakukan ping satu sama lain.

**Format Pengumpulan Tugas:**
Mahasiswa mengumpulkan arsip file `.zip` dengan format nama `DMJK_A_P04_<NIM>_<NamaLengkap>.zip` yang berisi:
1. File simulasi `.pkt` (nama file: `DMJK_A_P04_<NIM>_<NamaLengkap>.pkt`)
2. Screenshot IP DHCP yang didapat PC dan hasil ping antar-VLAN yang berhasil.
3. Laporan ringkas `.pdf` (nama file: `DMJK_A_P04_<NIM>_<NamaLengkap>.pdf`) disusun mengacu pada [Template Laporan Praktikum](https://docs.google.com/document/d/1ChvPwSa-9h_i8z8RE195jK_iNLz7sTdK/edit?usp=drivesdk&ouid=101845457565241443935&rtpof=true&sd=true).

---

**Pertemuan selanjutnya:** [05 — Access Control List (ACL) & Network Security](05-pertemuan-5-dhcp-acl.md)
