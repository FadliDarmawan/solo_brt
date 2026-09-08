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

**Semua 12 koridor punya geometri di `routes/<id>.json`**, dengan daftar
halte-di-sepanjang-garis dihitung otomatis lewat snapping (proyeksi
titik-ke-garis, threshold 60m):

| Kode | Rute |
|------|------|
| 1  | Terminal Palur — Bandara Adi Soemarmo |
| 2  | Terminal Palur — Stasiun Purwosari |
| 3  | Terminal Kartasura — Tugu Cembengan |
| 4  | Terminal Kartasura — Terminal Palur |
| 5  | Terminal Kartasura — Simpang Sidan |
| F7 | Ngipang — Pasar Klewer |
| F8 | Sub Terminal Pelangi — Pasar Legi (loop) |
| F9 | Sub Terminal Pelangi — Sub Terminal Semanggi |
| F10 | Terminal Palur — Pasar Klewer (loop) |
| F12 | Pasar Klewer — Lapangan Gentan |
| S1 | Terminal Tirtonadi — Sumberlawang (Trans Jateng) |
| S2 | Terminal Tirtonadi — Wonogiri (Trans Jateng) |

**PENTING — `stops/stops.json` TIDAK pakai hasil snapping.** Awalnya
`services` per halte diisi otomatis dari hasil snapping di atas, tapi ini
menghasilkan beberapa false positive (halte yang posisinya kebetulan dekat
garis rute padahal bus itu nggak benar-benar berhenti di situ). Jadi
`stops.json` sekarang di-rollback ke data keanggotaan rute asli (dari tag
OSM, sebelum ada snapping sama sekali) — 743 dari 747 halte punya tag
`routes` asli, 4 sisanya kosong dari OSM.

Konsekuensinya: keanggotaan rute di `stops.json` (dipakai buat search &
info "halte ini dilewati rute apa") bisa saja **tidak 100% sinkron** dengan
daftar halte yang muncul saat rute digambar di `routes/<id>.json` (yang
masih pakai hasil snapping, karena itu untuk keperluan render garis +
posisi marker, bukan buat menentukan "beneran berhenti atau nggak"). Kalau
mau, sesuaikan manual `services` di `stops.json` per halte — formatnya:

```json
{ "point": "110.xxx -7.xxx", "stop_name": "Nama Halte",
  "services": [{ "route": "1", "is_departure_hub": false, "destinations": [] }] }
```

## Papan jadwal keberangkatan (terminus)

Klik halte-halte terminus berikut buat lihat papan keberangkatan langsung
di popup-nya — 1 baris "sudah berangkat" (abu-abu), sampai 4 jadwal
berikutnya (paling atas ditebalkan + label "Berikutnya"), bus terakhir
hari itu dapat badge merah "Bus terakhir", dan begitu lewat jam segitu
tampil "Tidak ada pemberangkatan lagi hari ini". Waktunya dihitung dari
jam di browser kamu (live), bukan simulasi seperti demo widget-nya.

| Halte terminus | Rute yang ditampilkan |
|---|---|
| Terminal Palur | 1, 2, F10 |
| Terminal Kartasura | 3, 4, 5 |
| Terminal Ngipang | F7 |
| Pelangi Mojosongo | F8, F9 |
| Pasar Klewer | F12 |

Data jadwalnya nempel di stop yang sesuai di dalam `routes/<id>.json`
masing-masing (field `departures`, array string `"HH:MM"`), bukan file
terpisah — tinggal edit array itu kalau ada revisi jam. Logic tampilannya
ada di `getStopDepartures` / `buildDepartureBoard` di `index.html`.

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
