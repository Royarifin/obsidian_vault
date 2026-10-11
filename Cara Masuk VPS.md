# Rangkuman Akses VPS, Gallery Aset & Remotion Studio

**Terakhir diperbarui:** 11 Oktober 2026 · VPS `129.226.83.27` (user: `ubuntu`)

---

## Peta cepat (yang paling sering dipakai)

| Mau apa | Perintah di Terminal Mac | Buka di Chrome |
|---|---|---|
| Masuk terminal VPS | `ssh ubuntu@129.226.83.27` | — |
| **Gallery aset** (lihat + download klip) | `ssh -L 8080:localhost:8080 ubuntu@129.226.83.27` | `http://localhost:8080` |
| **Remotion Studio** (edit/preview animasi) | `ssh -L 3000:localhost:3000 ubuntu@129.226.83.27` | `http://localhost:3000` |
| 9Router (dashboard AI) | `ssh -N -L 20128:127.0.0.1:20128 ubuntu@129.226.83.27` | `http://localhost:20128` |
| Semua sekaligus (gallery + studio + 9router) | `ssh -L 8080:127.0.0.1:8080 -L 3000:127.0.0.1:3000 -L 20128:127.0.0.1:20128 ubuntu@129.226.83.27` | ketiganya |

**Aturan main tunnel:** jendela Terminal itu harus **tetap terbuka** selama kamu pakai. Tutup terminal / laptop tidur → terputus, tinggal jalankan ulang perintah yang sama.

---

## 1. Masuk terminal VPS

```bash
ssh ubuntu@129.226.83.27
```
- Password = password VPS kamu. **Saat mengetik password tidak ada tulisan yang muncul** — normal, bukan error.
- Keluar: ketik `exit` lalu Enter.
- Terminal ini buat jalanin perintah di VPS (render, cek status, dll).

---

## 2. Gallery aset Adobe Stock (paling sering kamu pakai)

**Di VPS:** tidak perlu ngapa-ngapain. Gallery nyala otomatis sebagai service `stockweb`.

**Di Mac:**
```bash
ssh -L 8080:localhost:8080 ubuntu@129.226.83.27
```
Lalu Chrome → **http://localhost:8080**

Isi halaman:
- Preview tiap klip (klik ▶ untuk putar)
- **⬇ Download** per klip · **⬇ Download semua (zip)** · **⬇ CSV metadata**
- **⧉ Copy metadata** (title + keywords siap tempel ke Adobe Stock)
- **↻ Render** untuk render ulang satu klip, plus status `✓ siap upload` / `● rendering…`

Klip yang sudah dirender otomatis muncul di sini. Data diambil dari `~/my-animation/output/stock/`.

---

## 3. Remotion Studio (edit & preview animasi)

**Cara A — service (disarankan, sekali klik):**

Di Terminal Mac, masuk VPS dulu:
```bash
ssh ubuntu@129.226.83.27
sudo systemctl start remotion-studio     # nyalakan studio
```
Masih di VPS, tinggal `exit`. Lalu di Mac:
```bash
ssh -L 3000:localhost:3000 ubuntu@129.226.83.27
```
Chrome → **http://localhost:3000**

Matikan studio kalau tidak dipakai:
```bash
ssh ubuntu@129.226.83.27
sudo systemctl stop remotion-studio
```
> Service ini **tidak auto-nyala saat reboot** (disengaja, hemat RAM). Nyalakan hanya saat mau edit.

**Cara B — manual (kalau tidak mau pakai service):**
```bash
ssh ubuntu@129.226.83.27
cd ~/my-animation && npm run dev      # jangan tutup terminal ini
```
Terminal Mac kedua:
```bash
ssh -L 3000:localhost:3000 ubuntu@129.226.83.27
```
Chrome → **http://localhost:3000**

⚠️ **Jangan nyalakan Studio saat sedang render batch** — Studio dan proses render sama-sama pakai port 3000, jadi bentrok. Cek dulu:
```bash
ss -ltn | grep :3000        # kalau ada isinya = port sedang dipakai
```

Di Studio kamu bisa: lihat semua komposisi (`Stock-Backgrounds`, `Stock-Loops`, `Stock-Utility`, `Transitions`), ubah warna/teks lewat panel Props, dan render satu klip dengan tombol Render.

---

## 4. Kalau ada masalah

| Gejala | Solusi |
|---|---|
| `bind: Address already in use` (di Mac) | Tunnel lama masih jalan. Tutup terminal lama, atau ganti port kiri: `-L 8081:127.0.0.1:8080` lalu buka `http://localhost:8081` |
| Terminal diam tanpa tulisan setelah SSH | Itu **tanda berhasil** (khusus perintah dengan `-N`) |
| `HOST IDENTIFICATION HAS CHANGED` | `ssh-keygen -R 129.226.83.27`, ulangi. Fingerprint yang benar: `SHA256:u60x8vmeNfZ7S/16VaCDlSS+iXtoe3sPqIEk3Z8HzB8` (kalau beda → jangan ketik `yes`) |
| Tidak bisa SSH sama sekali | fail2ban mungkin memblokir IP rumah kamu (salah password 5× / 10 menit = blokir 1 jam). Minta Hermes buka blokir |
| Gallery kosong padahal klip sudah dirender | Minta Hermes jalankan `python3 scripts/finalize_stock.py` (perapi nama file) |
| Render batch berhenti | Masuk VPS → lihat sisa: `ls ~/my-animation/output/stock/` → jalanin ulang: `cd ~/my-animation && python3 scripts/render_stock.py` |

---

## 5. Status service di VPS

| Service | Fungsi | Auto-nyala saat reboot |
|---|---|---|
| `stockweb` | Gallery aset (port 8080) | ✅ ya |
| `9router` | AI router (port 20128) | ✅ ya |
| `remotion-studio` | Studio edit animasi (port 3000) | ❌ manual |

Cek dari dalam VPS:
```bash
systemctl status stockweb 9router remotion-studio --no-pager
```

**Keamanan:** port 8080/3000/20128 hanya bisa diakses lewat SSH tunnel — sudah dicek tertutup dari internet. SSH pakai password + fail2ban; daftar putih: `127.0.0.0/8`, `::1`, IP Mac lama `182.10.97.224`.

---

## File terkait
- `Remotion Adobe Stock.md` — cara render & upload aset ke Adobe Stock
- `9router - Cara Masuk.md` — detail dashboard 9Router
- `Remotion Setup & Access.md` — versi lama (isi sudah dipindah ke catatan ini)
