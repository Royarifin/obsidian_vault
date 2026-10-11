# Stock Studio — Cara Pakai & Standar Adobe

**Dibuat:** 11 Oktober 2026 · VPS `129.226.83.27`

---

## 1. Buka webnya
**https://judgment-kits-faqs-grace.trycloudflare.com** (login: `roy` / lihat `~/stock-web/auth.txt`)
atau lewat tunnel: `ssh -L 8080:localhost:8080 ubuntu@129.226.83.27` → `http://localhost:8080`

Ada 5 tab: **Dasbor**, **Aset**, **Batch upload**, **Antrean render**, **Panduan**.

---

## 2. Alur kerja (5 langkah)

1. **Antrean render** → pilih komposisi → klik **Render**. Render jalan satu per satu di VPS.
2. Selesai render, **QC jalan otomatis**. Lihat hasilnya di tab **Aset**:
   - `QC lolos` = aman diupload
   - `QC lolos (catatan)` = ada catatan, masih boleh diupload
   - `QC gagal` = jangan diupload, perbaiki dulu
3. Perbaiki **title** dan **keyword** kalau perlu (tombol "Edit metadata" di kartu aset). Title maks 70 karakter, keyword maks 50.
4. **Batch upload** → **Buat batch baru** → download **zip** (klip) + **CSV metadata**.
5. Upload ke **contributor.stock.adobe.com** → drag MP4 → upload CSV → submit. Setelah ditinjau, tandai **Diterima/Ditolak** di tab Aset supaya tingkat penerimaan kelihatan.

---

## 3. Standar yang dipakai QC (dari aturan resmi Adobe)

### Wajib
| Item | Aturan |
|---|---|
| Format | MP4 H.264, pixel format yuv420p |
| Resolusi | 1920x1080 atau 1080x1920 (vertical), min 1080 di sisi terpendek |
| FPS | 23.98 / 24 / 25 / 29.97 / 30 / 50 / 59.94 / 60 |
| Durasi | 5-60 detik (Adobe sarankan 15 detik ke atas) |
| Ukuran | maks 3.9 GB |
| Nama file | maks **30 karakter** termasuk `.mp4` |
| Title | maks **70 karakter**, tanpa koma |
| Keywords | maks **50**, huruf kecil, tanpa duplikat, tanpa merek |
| Kategori | angka 8 = Graphic Resources |

### Dilarang (langsung ditolak)
- **Teks/angka overlay, lower third, intro** — Adobe: *"add text overlays, transitions, or introductions"*
- **Transisi** antar klip
- **Gerakan minim / frame beku** ("limited motion")
- Frame hitam atau putih
- Merek, logo, nama perusahaan, orang nyata
- Konten mirip yang diupload beruntun ("similar content" — penolakan nomor satu)

### Yang dicek QC per klip
- teknis: codec, resolusi, fps, durasi, ukuran, bitrate, ada/tidak audio
- visual: frame hitam/putih, jeda beku, tingkat gerakan, kemulusan loop, variasi warna (banding)
- metadata: panjang title/keyword, duplikat, merek, istilah pemicu
- portofolio: kemiripan keyword antar klip (ambang 45%)

---

## 4. Klip yang DIBLOKIR aturan (jangan disubmit)
7 klip lama sudah ditandai **jangan submit** dan tidak akan masuk batch:
- `countdown-10s`, `countdown-5s` — teks/angka overlay
- `lower-third-dark`, `lower-third-light` — lower third (kategori template undangan, bukan video)
- `transition-smooth`, `transition-slide`, `transition-blur` — transisi

Yang aman: 15 klip background/loop/particles/equalizer + 4 klip scene animasi isometrik
(gudang siang, gudang malam, kota siang, kota malam).

---

## 5. Klip scene animasi (bukan polosan)
Dibuat dengan engine isometrik sendiri di `src/stock/iso.tsx`:
- `SceneWarehouse.tsx` — gudang: rak, karton, 4 forklift jalan, conveyor dengan paket
- `SceneCity.tsx` — jalan kota: mobil, bus, truk, monorel, lampu jalan, jendela menyala
Semua loop mulus 15 detik, tanpa teks, tanpa merek, tanpa orang (tidak perlu model release).

Tambah scene baru: bikin `SceneXxx.tsx` pakai helper `iso.tsx` + `vehicles.ts`, daftarkan di
`src/Root.tsx` (folder `Stock-Scenes`), lalu tambah entri di `scripts/gen_catalog.py` →
jalankan `python3 scripts/gen_catalog.py`.

---

## 6. Perintah terminal (opsional)
```bash
cd ~/my-animation
python3 scripts/render_stock.py --list          # daftar aset yang boleh disubmit
python3 scripts/render_stock.py Wh-Day          # render satu klip
python3 scripts/qc.py                           # jalankan QC semua klip
python3 scripts/gen_catalog.py                  # regenerate katalog setelah menambah aset
```
Hasil: `output/stock/<nama>.mp4`, `output/stock/qc.json`, `output/stock/batches/`

---

## 7. Catatan jujur
- QC ini menutup sebab penolakan yang **bisa dicek mesin**. Penilaian estetika, "commercial value",
  dan kemiripan dengan isi portofolio orang lain tetap ditentukan moderator Adobe — jadi tidak ada
  jaminan 100% diterima.
- Delapan background gradasi masih mirip satu sama lain. Ambang kemiripan batch 45% sudah mencegah
  dua klip mirip masuk batch yang sama, tapi jangan submit semuanya dalam sehari.
- Bitrate klip lama 1-8 Mbps. QC menandai yang di bawah 2 Mbps. Kalau mau kualitas maksimal,
  render ulang dengan `--crf 14` (lebih lama).
