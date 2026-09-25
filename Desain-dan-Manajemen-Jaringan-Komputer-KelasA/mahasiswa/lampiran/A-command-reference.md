# LAMPIRAN A — Command Reference

**Cisco IOS pada Packet Tracer — router dan switch**

Kumpulan perintah yang dipakai sepanjang 5 pertemuan, disusun per topik. Perintah bertanda **(verifikasi)** tidak mengubah apa pun dan aman dijalankan kapan saja.

---

## Dasar dan Navigasi

```
enable                                  masuk mode privileged
configure terminal                      masuk mode konfigurasi global
exit                                    turun satu tingkat
end                                     langsung ke privileged
write memory                            simpan konfigurasi berjalan ke startup
```

## Interface (Pertemuan 1–2)

```
interface <tipe><nomor>
 ip address <ip-address> <mask>
 no shutdown
```

Verifikasi:

```
show ip interface brief                 (verifikasi) ringkas semua interface
```

## VLAN dan Trunk — switch (Pertemuan 3–4)

```
vlan <id>
 name <nama>

interface <tipe><nomor>
 switchport mode access
 switchport access vlan <id>

interface <tipe><nomor>
 switchport mode trunk
 switchport trunk allowed vlan <daftar>
```

Verifikasi:

```
show vlan brief                         (verifikasi) daftar VLAN dan port anggotanya
show interfaces trunk                   (verifikasi) trunk aktif dan VLAN yang diizinkan
```

## Router-on-a-Stick (Pertemuan 4)

```
interface <tipe><nomor>
 no shutdown
interface <tipe><nomor>.<sub>
 encapsulation dot1Q <vlan-id>
 ip address <ip-address> <mask>
```

Verifikasi:

```
show ip route                           (verifikasi) tabel routing
```

## DHCP (Pertemuan 4)

```
ip dhcp excluded-address <awal> <akhir opsional>
ip dhcp pool <nama>
 network <jaringan> <mask>
 default-router <ip-address>
```

Verifikasi:

```
show ip dhcp binding                    (verifikasi) alamat yang sudah disewakan
show ip dhcp pool                       (verifikasi) pemakaian dan pengecualian
```

Di sisi klien (Command Prompt PC):

```
ipconfig
ipconfig /all
ipconfig /release
ipconfig /renew
```

## ACL (Access Control List) (Pertemuan 5)

### Standard ACL (1–99)
```
access-list <1-99> permit|deny <sumber-ip> <wildcard>
interface <tipe><nomor>
 ip access-group <nomor> in|out
```

### Extended ACL (100–199)
```
access-list <100-199> permit|deny ip <sumber-ip> <wildcard-sumber> <tujuan-ip> <wildcard-tujuan>
interface <tipe><nomor>
 ip access-group <nomor> in|out
```

Verifikasi:

```
show access-lists                       (verifikasi) daftar ACL & match counter
show ip access-lists                    (verifikasi) termasuk penghitung per baris
```

### Wildcard mask

Kebalikan dari subnet mask: bit `0` berarti harus cocok, bit `1` berarti diabaikan.

| Cakupan | Subnet mask | Wildcard |
|---|---|---|
| Satu host | 255.255.255.255 | 0.0.0.0 |
| /28 | 255.255.255.240 | 0.0.0.15 |
| /27 | 255.255.255.224 | 0.0.0.31 |
| /24 | 255.255.255.0 | 0.0.0.255 |
| Semua | — | 255.255.255.255 (`any`) |

## Diagnosis dari Sisi Klien

```
ipconfig /all                           alamat, gateway, DNS yang diterima
ping <ip-address>
tracert <ip-address>
```
