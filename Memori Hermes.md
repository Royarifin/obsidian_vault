---
judul: Memori Hermes
dibuat: 2026-09-23
diperbarui: 2026-09-23
---

# Memori Hermes

Catatan ini berisi ringkasan hal-hal yang diingat Hermes tentang Roy dan tentang setup server ini.
Dibuat otomatis oleh Hermes di server, lalu di-push ke GitHub.

## Tentang Roy

- Nama: **Roy Arifin** (username Mac: `roynurarifin`, GitHub: `Royarifin`)
- Email: roy.arifin1122@gmail.com
- Perangkat: **MacBook Air (macOS)**
- Bahasa: Indonesia — lebih suka balasan **bahasa Indonesia sederhana** (bukan campuran dialek/slang berat)
- Level: pengguna terminal **pemula** → butuh panduan langkah demi langkah, dan konfirmasi setelah tiap langkah
- Preferensi: **suka otomatis** daripada langkah manual berulang; hindari hal-hal yang boros token/LLM
- Pakai **Obsidian** untuk mencatat, vault di Mac: `~/Documents/Obsidian_Vault`

## Setup Sinkronisasi Vault

- Repo GitHub: `github.com/Royarifin/obsidian_vault`
- Di server ini di-clone ke: `~/vault`
- Autentikasi: **deploy key** SSH di `~/.ssh/obsidian_vault` (akses read-write)
- Skrip auto-sync: `~/vault_sync.sh`
  - Langkah 1: `git pull --ff-only origin main` (ambil perubahan dari Mac)
  - Langkah 2: kalau ada perubahan lokal, `git add -A` + commit sebagai `hermes` + `git push origin main`
- Jadwal: **crontab sistem, tiap 10 menit**, pakai `flock` supaya tidak tumpang tindih
- Log: `~/vault_sync.log`
- Sisi Mac: plugin **Obsidian Git** (Vinzent), auto-backup tiap 10 menit

## Cara Kerja Praktis

- Kalau Hermes bikin/ubah note di `~/vault`, perubahan sampai ke Obsidian di Mac **dalam ~10 menit**
- Kalau Roy bikin note di Mac, perubahan sampai ke server dalam ~10 menit (asalkan plugin Obsidian Git di Mac aktif)

## Status & Catatan Penting

- **23 Sep 2026:** Sinkron server → GitHub terverifikasi jalan (deploy key valid, repo up to date). Note ini berhasil di-push dari server.
- **23 Sep 2026:** Commit dari Mac masuk juga (`vault backup: 2026-09-23 00:13:44`) → **plugin Obsidian Git di Mac sudah jalan** dan bisa nge-push ke GitHub.
- **Sisa PR:** belum ada note dari Mac yang masuk selain `Welcome.md`. Perlu diuji dengan membuat note baru di Mac.
- Memori internal Hermes sendiri disimpan di `~/.hermes/` di server, **bukan** di vault ini. Catatan ini hanya ringkasan manual.

## Ide Berikutnya

- [ ] Bereskan push otomatis dari Mac (plugin Obsidian Git)
- [ ] Uji dua arah: bikin note di Mac → cek muncul di server
- [ ] (Opsional) Hermes menulis catatan harian/kegiatan ke vault secara otomatis