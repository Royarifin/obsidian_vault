# Remotion → Adobe Stock (Cara Pakai)

Project: VPS `~/my-animation` (Remotion 4.0.534). 22 klip siap jual.

## Render
```bash
cd ~/my-animation
python3 scripts/render_stock.py            # render semua (≈1–3 menit/klip)
python3 scripts/render_stock.py SB-Aurora  # satu klip saja
python3 scripts/render_stock.py --list     # lihat daftar job
python3 scripts/render_stock.py --csv      # bikin ulang CSV saja
```

Hasil di `output/stock/`:
- `<nama>.mp4` — H.264, 1920x1080 (atau 1080x1920 vertical), 30 fps, tanpa audio
- `adobe-stock-metadata.csv` — **upload ini ke portal** (format resmi Adobe)
- `stock-qc.csv` — cek teknis (durasi, resolusi) — buat sendiri, jangan diupload

## Upload ke Adobe Stock
1. contributor.stock.adobe.com → Upload → drag semua MP4
2. Klik upload CSV → pilih `output/stock/adobe-stock-metadata.csv`
3. Submit untuk moderasi
Kalau volume besar, Adobe sarankan SFTP (kredensial ada di halaman Upload).

## Batasan Adobe yang WAJIB dipatuhi (sudah dicek otomatis oleh script)
| Item | Batas |
|---|---|
| Nama file | **max 30 karakter** termasuk `.mp4` |
| Title | max 70 karakter, tanpa koma |
| Keywords | max 50 (urut dari yang paling penting) |
| Durasi | 5–60 detik |
| Format | MP4 H.264, min 1080 di sisi terpendek |
| Category | angka: **8 = Graphic Resources** (background, tekstur, elemen desain) |
| Header CSV | `Filename,Title,Keywords,Category,Releases` — jangan diubah |

Script menolak jalan kalau ada nama file >30 karakter, title >70, atau keywords >50.

## Tambah aset baru
1. Bikin komponen di `src/stock/`
2. Daftarkan di `src/Root.tsx` dalam `<Folder name="Stock-...">`
3. Tambah baris job di `scripts/render_stock.py` (comp, name, title, keywords)
4. `npx tsc --noEmit -p tsconfig.json && npx eslint src`
5. `python3 scripts/render_stock.py <CompId>`

## Catatan kecepatan (VPS 2 vCPU / 2GB)
- CSS `blur()` besar = pembunuh kecepatan (85s/60 frame → jadi 19s tanpa blur + grain PNG)
- Grain pakai `public/noise.png`, bukan SVG feTurbulence
- `--concurrency=2` paling optimal di VPS ini

## Peringatan moderasi
Jangan submit 22 klip sekaligus kalau banyak yang mirip (8 background gradasi).
Adobe bisa menolak karena "similar content / spam". Submit bertahap 4–6 klip,
campur kategori (background, particles, equalizer, transitions, lower third).
