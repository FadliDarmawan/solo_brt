# Peta Interaktif Batik Solo Trans & Trans Jateng

Fork frontend-only dari [brtsmg](https://github.com/FadliDarmawan/brtsmg) (yang
ternyata sendiri di-fork dari project Trans Jogja) — versi ini murni statis,
tanpa database/backend, cocok buat dibuka langsung atau di-hosting di Vercel/
Netlify/GitHub Pages.

## Cara jalanin

Cukup serve foldernya sebagai static site (harus lewat HTTP server, bukan
`file://`, karena pakai `fetch()`):

```bash
npx serve .
# atau
python3 -m http.server 8000
```

Buat deploy ke Vercel: drag folder ini ke vercel.com/new, atau `vercel deploy`
dari dalam folder ini. Nggak butuh env var apa pun (semua data statis).

## Struktur

- `index.html` — peta + rail rute + search + panel detail rute (MapLibre GL JS + Fuse.js)
- `stops/stops.json` — 747 halte hasil kalibrasi manual kamu
- `routes/<id>.json` — geometri + urutan halte per koridor
- `src/` — ikon panah arah & logo

## Status data per koridor

**Sudah ada geometri + halte ke-snap otomatis ke garis rute** (7 koridor):

| Kode | Rute | Halte ke-snap |
|------|------|----------------|
| 1  | Terminal Palur — Bandara Adi Soemarmo | 122 |
| 2  | Terminal Palur — Stasiun Purwosari | 81 |
| 4  | Terminal Kartasura — Terminal Palur | 111 |
| F7 | Ngipang — Pasar Klewer | 70 |
| F8 | Sub Terminal Pelangi — Pasar Legi (loop) | 40 |
| F9 | Sub Terminal Pelangi — Sub Terminal Semanggi | 84 |
| F10 | Terminal Palur — Pasar Klewer (loop) | 56 |

Snapping pakai proyeksi titik-ke-garis dengan threshold 60 meter — kalau ada
halte yang "hilang" dari suatu koridor padahal seharusnya masuk, kemungkinan
jaraknya >60m dari garis rute (garis kurang presisi atau halte-nya memang
agak jauh dari jalan utama).

**Ada di daftar rute tapi BELUM ada file geometri** (5 koridor — rail/list
tetap muncul, tapi klik rutenya belum nggambar garis):

- `3` — Terminal Kartasura — Taman Lansia
- `5` — Terminal Kartasura — Simpang Sidan
- `F12` — Pasar Klewer — Lapangan Gentan
- `S1` — Terminal Tirtonadi — Sumberlawang (Trans Jateng)
- `S2` — Terminal Tirtonadi — Wonogiri (Trans Jateng)

Halte yang termasuk 6 koridor ini masih pakai data keanggotaan rute dari tag
OSM lama (belum di-snap ke garis), jadi kemungkinan kurang lengkap/akurat.
Begitu kamu punya file polyline buat koridor-koridor ini, kirim ke Claude —
proses snapping-nya sama persis kayak yang 6 di atas.

## Yang sengaja dikosongkan (warisan dari versi Jogja, TIDAK ditebak)

- `ROUTE_DIRECTIONS` — titik start/wayback per koridor buat tombol "Show
  final destination". Kosong = tombol itu nonaktif sampai diisi manual per
  koridor (lihat komentar di `index.html` sekitar baris 1640 soal kenapa ini
  gak boleh ditebak otomatis).
- `DISPLAY_NAMES` — tabel pemendekan nama tampilan (mis. "Terminal Palur" →
  "Palur"). Nama halte kamu udah dikalibrasi manual, jadi ini dibiarkan
  kosong dulu; isi kalau mau ada versi nama yang lebih ringkas di peta.

## Yang TIDAK ikut di-fork (frontend-only, sesuai pilihan kamu)

Backend `node-api/` (Postgres/Neon, endpoint upload Excel, admin panel review/
merge halte) sengaja nggak diikutkan. Kalau nanti mau upgrade ke situ, tinggal
bilang aja.
