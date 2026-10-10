# 9Router — Cara Masuk

Catatan ini untuk dipakai kalau kamu sudah keluar / mau masuk lagi ke 9Router.

**Terakhir diperbarui:** 11 Oktober 2026

---

## Ringkasan singkat

| Hal | Nilai |
|---|---|
| 9Router jalan di | VPS kita (IP `129.226.83.27`) |
| Port | `20128` |
| Alamat di browser | `http://localhost:20128` |
| Data tersimpan di | `/home/ubuntu/.9router/db/data.sqlite` |
| Status | Service otomatis, nyala sendiri kalau VPS reboot |

Port `20128` **tertutup dari internet**. Jadi harus lewat "terowongan" SSH dulu (langkah di bawah).

---

## Langkah masuk (2 langkah saja)

### 1. Buka Terminal di Mac, tempel perintah ini, tekan Enter

```
ssh -N -L 20128:127.0.0.1:20128 ubuntu@129.226.83.27
```

- Masukkan **password VPS** kamu lalu Enter.
- **Layar tidak menampilkan apa-apa saat mengetik password** — ini normal, bukan error.
- Setelah Enter, terminal akan **terlihat diam / nge-hang tanpa tulisan apa pun**. **Itu justru tanda berhasil.** Jangan tutup terminal ini.

> Perintah ini disebut SSH tunnel. Artinya: port 20128 di Mac kamu disambungkan ke port 20128 di VPS, lewat koneksi SSH yang aman.

### 2. Buka browser di Mac

```
http://localhost:20128
```

Login, dan kamu bisa atur provider AI di dashboard-nya.

---

## Kalau muncul peringatan "HOST IDENTIFICATION HAS CHANGED"

Ini muncul kalau catatan kunci SSH di Mac sudah basi (biasanya karena VPS/IP pernah berubah). Cara bersihkan:

```
ssh-keygen -R 129.226.83.27
```

Lalu jalankan ulang perintah tunnel di atas. Saat muncul pertanyaan `Are you sure you want to continue connecting (yes/no)?`, **cek dulu** baris fingerprint-nya harus persis begini:

```
SHA256:u60x8vmeNfZ7S/16VaCDlSS+iXtoe3sPqIEk3Z8HzB8
```

Kalau **cocok** → ketik `yes` lalu Enter.
Kalau **beda** → jangan ketik `yes`, tutup terminal, hubungi Hermes dulu.

---

## Hal-hal yang perlu diingat

- Tunnel hanya hidup **selama Terminal itu terbuka**. Tutup Terminal, laptop tidur, atau WiFi putus → tunnel mati.
- Mau masuk lagi? Tinggal jalankan ulang perintah `ssh -N -L ...` yang sama, lalu buka `http://localhost:20128` lagi.
- Tidak perlu install ulang apa pun. 9Router di VPS sudah otomatis nyala.
- Kalau muncul `bind: Address already in use` → berarti tunnel lama di Mac masih jalan. Tutup terminal lama, atau pakai port kiri yang berbeda, misalnya `-L 20129:127.0.0.1:20128`, lalu buka `http://localhost:20129`.

---

## Cek status 9Router (opsional, via SSH biasa ke VPS)

Kalau kamu penasaran 9Router masih jalan atau tidak, SSH biasa (tanpa `-N`):

```
ssh ubuntu@129.226.83.27
```

Lalu jalankan:

```bash
systemctl status 9router                                    # lihat status singkat
sudo journalctl -u 9router -n 50 --no-pager                 # lihat 50 baris log terakhir
curl -s -o /dev/null -w "%{http_code}\n" -L http://127.0.0.1:20128/dashboard   # harus balas 200
sudo systemctl restart 9router                              # restart kalau perlu
```

---

## Info teknis (untuk arsip)

- Terinstall: `9router v0.5.99` (npm global, `/usr/bin/9router`)
- Service systemd: `/etc/systemd/system/9router.service` (enabled, `Restart=always`)
- Perintah jalan: `9router --no-browser --skip-update --host 127.0.0.1 --port 20128`
- Bind hanya ke `127.0.0.1` — sengaja, supaya tidak bisa diakses dari internet
- Dashboard punya login sendiri; API tanpa login balas `401`
- VPS: Ubuntu 24.04, Node v22.23.2, npm 10.9.8
