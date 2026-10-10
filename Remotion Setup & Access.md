# Remotion Setup & Access

Panduan lengkap setup Remotion di VPS dan akses dari Mac.

## Prerequisites
- VPS: Ubuntu 129.226.83.27 (Node.js v22+)
- Mac: SSH access ke VPS
- Goal: Bikin stock animations buat Adobe Stock

## Installation (VPS)

### 1. Install Remotion CLI
```bash
sudo npm install -g @remotion/cli
```

### 2. Create Project
```bash
npx create-video my-animation
# Choose: "Hello World" template
# Choose: "Install all skills"
```

## Accessing Remotion Studio

### Terminal 1: VPS (Jalanin Server)

```bash
# Kill old processes
killall -9 node

# Raise file limit
ulimit -n 65536

# Go to project
cd ~/my-animation

# Start dev server
npm run dev
```

**Wait sampai keluar:**
```
Server ready - Local: http://localhost:3000, Network: http://10.3.0.7:3000
Built in XXXms
```

⚠️ **Jangan tutup terminal ini.**

---

### Terminal 2: Mac (SSH Tunnel)

Buka terminal **BARU** di Mac (tab baru atau window baru):

```bash
ssh -L 3000:localhost:3000 ubuntu@129.226.83.27
```

Enter password. Akan login ke VPS.

⚠️ **Jangan tutup terminal ini juga.**

---

### Chrome: Mac

Buka Chrome baru, ketik:
```
http://localhost:3000
```

Press Enter → Remotion Studio akan muncul.

---

## Troubleshooting

**"Port 3000 is busy"**
- Di VPS, jalanin: `killall -9 node` dan ulang

**"Failed to connect to localhost"**
- Pastikan SSH tunnel masih nyambung (terminal jangan ditutup)
- Di Mac, cek: `lsof -i :3000` harus show tunnel jalan

**"Connection refused"**
- Di VPS, cek: `curl http://localhost:3000` — harusnya okay
- Terus di Mac, coba buka SSH tunnel baru

---

## Next: Render Video

Setelah siap dengan animasi:

```bash
npx remotion render
```

Output video ada di `/out/` folder.

---

## File Struktur

```
my-animation/
├── src/
│   ├── compositions/
│   │   └── HelloWorld.tsx  (edit ini buat animasi)
│   └── index.ts
├── remotion.config.ts
└── package.json
```

Edit `HelloWorld.tsx` buat ganti animasi.
