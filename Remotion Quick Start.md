# Remotion Quick Start

## Step 1: Masuk ke VPS (Terminal 1)
Di Mac terminal, ketik:
```bash
ssh ubuntu@129.226.83.27
```
Masukkan password.

---

## Step 2: Jalankan Remotion (di Terminal 1 yang sama, VPS)
```bash
cd ~/my-animation
npm run dev
```
Tunggu sampai keluar:
```
Server ready - Local: http://localhost:3000
```
⚠️ **Jangan tutup terminal ini.**

---

## Step 3: Buat Terowongan (Terminal 2 - Tab/Window Baru di Mac)
Buka terminal BARU (tab baru atau window baru di Mac):
```bash
ssh -L 3000:localhost:3000 ubuntu@129.226.83.27
```
Masukkan password.  
⚠️ **Jangan tutup terminal ini.**

Ini menghubungkan Mac port 3000 → VPS port 3000 (terowongan).

---

## Step 4: Buka Chrome (Mac)
Buka Chrome, ketik:
```
http://localhost:3000
```

Remotion Studio siap.

---

## Quick Recap
1. SSH ke VPS (`ssh ubuntu@129.226.83.27`)
2. Run Remotion (`cd ~/my-animation && npm run dev`)
3. Terminal BARU: tunnel (`ssh -L 3000:localhost:3000 ubuntu@129.226.83.27`)
4. Chrome: `http://localhost:3000`
