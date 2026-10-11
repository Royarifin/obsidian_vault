# Remotion → Adobe Stock (Cara Pakai)

Project: VPS `~/my-animation` (Remotion 4.0.534). 22 klip siap jual.

## 🌐 Lihat & download hasil di browser (tanpa terminal ribet)
Dari Mac, buka terminal sekali:
```bash
ssh -L 8080:localhost:8080 ubuntu@129.226.83.27
```
Biarkan jendela itu terbuka, lalu buka **http://localhost:8080** di Chrome.

Yang ada di halaman itu:
- Preview tiap klip (klik ▶ buat diputar)
- Tombol **⬇ Download** per klip
- Tombol **⧉ Copy metadata** (title + keywords siap tempel ke Adobe)
- Tombol **↻ Render** kalau mau render ulang satu klip
- Tombol **⬇ Download semua (zip)** + **⬇ CSV metadata**

Web ini jalan otomatis (systemd `stockweb.service`), file-nya di `~/stock-web/`
(`server.py` + `index.html`). Port 8080 hanya localhost di VPS — tidak terbuka ke internet.

## Render (kalau mau dari terminal)
```bash
cd ~/my-animation
python3 scripts/render_stock.py            # render semua (≈3 menit/klip)
python3 scripts/render_stock.py SB-Aurora  # satu klip saja
python3 scripts/render_stock.py --list     # lihat daftar job
```

Hasil di `output/stock/`:
- `<nama>.mp4` — H.264, 1920x1080 (atau 1080x1920 vertical), 30 fps, tanpa audio
- `adobe-stock-metadata.csv` — **upload ini ke portal** (format resmi Adobe)
- `stock-qc.csv` — cek teknis internal (jangan diupload)

Hasil render **tidak masuk GitHub** — `output/` sudah di-ignore di repo Remotion.

## Upload ke Adobe Stock
1. contributor.stock.adobe.com → Upload → drag semua MP4
2. Klik upload CSV → pilih `adobe-stock-metadata.csv`
3. Submit untuk moderasi
Volume besar? Adobe sarankan SFTP (kredensial ada di halaman Upload).

## Batasan Adobe yang WAJIB dipatuhi (dicek otomatis oleh script)
| Item | Batas |
|---|---|
| Nama file | **max 30 karakter** termasuk `.mp4` |
| Title | max 70 karakter, tanpa koma |
| Keywords | max 50 (urut dari yang paling penting) |
| Durasi | 5–60 detik |
| Format | MP4 H.264, min 1080 di sisi terpendek |
| Category | angka: **8 = Graphic Resources** (background, tekstur, elemen desain) |
| Header CSV | `Filename,Title,Keywords,Category,Releases` — jangan diubah |

## Tambah aset baru
1. Bikin komponen di `src/stock/`
2. Daftarkan di `src/Root.tsx` dalam `<Folder name="Stock-...">`
3. Tambah baris job di `scripts/render_stock.py` (comp, name, title, keywords)
4. `npx tsc --noEmit -p tsconfig.json && npx eslint src`
5. `python3 scripts/render_stock.py <CompId>`

## Catatan kecepatan (VPS 2 vCPU / 2GB)
- CSS `blur()` besar = pembunuh kecepatan (85s/60 frame → 19s tanpa blur besar + grain PNG)
- Grain pakai `public/noise.png`, bukan SVG feTurbulence
- `--concurrency=2` paling optimal di VPS ini
- 1 klip 10 detik 1080p ≈ 3 menit

## Peringatan moderasi
Jangan submit 22 klip sekaligus kalau banyak yang mirip (8 background gradasi).
Adobe bisa menolak karena "similar content / spam". Submit bertahap 4–6 klip,
campur jenis (background, particles, equalizer, transitions, lower third).
