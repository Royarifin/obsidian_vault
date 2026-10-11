# Motion Studio — Cara Pakai

**Dibuat:** 11 Oktober 2026 · VPS `129.226.83.27` · folder `~/motion-studio`
(Beda dari `~/my-animation` yang lama — ini pabrik template parametrik, satu template jadi banyak varian.)

---

## 1. Perintah utama (di VPS)

```bash
cd ~/motion-studio

npm run dev                    # buka Remotion Studio (lihat/uji template)
npm run gen -- --count 50      # bikin daftar varian (out/manifest.json)
npm run render                 # render semua varian (4K)
npm run qa                     # cek otomatis → laporan PASS/FAIL
npm run sheet                  # bikin contact sheet untuk dilihat cepat
```

Uji cepat sebelum render banyak (1080p, ~4x lebih ngefek):

```bash
npm run render -- --limit 3 --resolution hd
```

---

## 2. Alur kerja

1. `npm run gen` → hasilkan varian + metadata otomatis (title, keyword, nama file).
2. `npm run render` → mp4 masuk `out/videos/`. Kalau putus, jalankan lagi: yang sudah ada otomatis di-skip.
3. `npm run qa` → periksa codec, resolusi, fps, durasi, tidak ada audio, frame beku, loop mulus.
4. `npm run sheet` → lihat `out/sheets/_batch.jpg` (1 gambar isi semua klip).
5. Upload: `out/videos/*.mp4` + CSV `out/adobe-upload.csv` ke `contributor.stock.adobe.com`.

⚠️ Jangan buka Studio saat render jalan (rebutan port 3000).

---

## 3. Aturan yang sudah otomatis

- Nama file ≤ 30 karakter, title ≤ 70 karakter **tanpa koma**, keyword 25-40, kategori 8 (Graphic Resources).
- Anti-duplikat: 2 varian dari template sama wajib beda ≥3 dimensi (palet, waktu, gaya, kamera, dll). Maksimal 20% satu kategori per batch.
- Template berisi teks (kinetic typography) ditandai **risky** dan TIDAK ikut batch, kecuali pakai `--include-risky`. Adobe sering tolak klip teks.
- Alpha (ProRes 4444 .mov): untuk overlay. Filenya besar (15 detik 1080p ≈ 170 MB) → render alpha di 1080p saja.

---

## 4. Ukuran & kecepatan (VPS 2 core, 2 GB)

| Klip | Waktu render |
|---|---|
| 8 detik H.264 1080p | 1 menit |
| 20 detik H.264 1080p | 2,5 menit |
| 15 detik alfa 1080p | 2 menit |

4K = 4x lebih lama (≈10 menit per klip 20 detik). Render malam, lihat pagi.

---

## 5. Nambah template baru

Baca `~/motion-studio/README.md` bagian **"Adding a template (10-step checklist)"** — 10 langkah, dari bikin file di `src/compositions/<kategori>/` sampai daftar di `src/registry.ts`. Semua otomatis kebaca Studio, generator, renderer, dan QA.

Isi `src/generator/trends.json` untuk atur bobot pasar (kategori yang lagi laku, musim).

---

## 6. Kalau ada masalah

- **Render gagal / kehabisan memori** → pastikan `--concurrency 1`, tutup aplikasi lain.
- **QA bilang loop seam** → gerak harus periodik (template sudah pakai helper di `src/lib/useLoop.ts`).
- **Studio tidak mau buka** → cek `ss -ltn | grep :3000` (mungkin render masih jalan).
