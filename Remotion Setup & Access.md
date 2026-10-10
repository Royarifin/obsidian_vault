# Remotion Setup

## Terminal 1: VPS
```bash
killall -9 node
ulimit -n 65536
cd ~/my-animation
npm run dev
```
Server jalan di port 3000. **Jangan tutup.**

## Terminal 2: Mac
```bash
ssh -L 3000:localhost:3000 ubuntu@129.226.83.27
```
**Jangan tutup.**

## Chrome: Mac
```
http://localhost:3000
```

---

## Troubleshoot
- Port busy: `killall -9 node` di VPS
- Connection refused: cek SSH tunnel masih running
- Edit animasi: `src/compositions/HelloWorld.tsx`
- Render: `npx remotion render`
