---
description: Scan route config dan paths.ts di project ini, lalu generate .claude/registry/menu-registry.md dengan daftar lengkap menu, path URL, lokasi page, dan service
---

# Command: /init:initmenuregistry

Kamu akan men-scan konfigurasi routing project ini, mengekstrak semua menu/route yang terdaftar, lalu menulis ulang `.claude/registry/menu-registry.md`.

---

## ATURAN UTAMA

1. **Scan dulu, tulis belakangan** — baca `paths.ts` dan nav config sebelum menulis apapun.
2. **Derive dari source, bukan tebak** — path URL harus diambil dari `paths.ts`, bukan dikarang.
3. **Satu konfirmasi sebelum tulis** — tampilkan ringkasan, minta YA sebelum eksekusi.
4. **Cancel kapan saja** — jika user mengetik `CANCEL`, hentikan dan tampilkan: `Init dibatalkan. Tidak ada file yang dimodifikasi.`
5. **Regenerate penuh** — timpa file lama sepenuhnya, tidak merge dengan isi sebelumnya.

---

## LANGKAH 1 — Temukan File Route Config

Cari file-file berikut secara paralel (tidak semua harus ada — cukup yang ditemukan):

1. `src/routes/paths.ts` atau `src/routes/paths.js` — konstanta URL
2. `src/layouts/config-nav-dashboard.tsx` atau file serupa dengan nama `config-nav*` — nav items per role
3. `src/routes/components/Routes.tsx` atau file router utama — untuk validasi route yang terdaftar

Jika `paths.ts` tidak ditemukan:
- Coba glob `**/{paths,routes,router}.ts` dari `src/`
- Jika masih tidak ditemukan → tampilkan pesan error dan stop

---

## LANGKAH 2 — Ekstrak Data dari paths.ts

Baca `paths.ts` dan bangun map dari semua leaf path (nilai string, bukan function, bukan `*` wildcard):

```
pathKey (misal: master.industry.product.hlp) → URL string (/master/prod/hlp/industry)
```

Abaikan:
- Path yang mengandung `*` (wildcard, dipakai router internal)
- Path yang merupakan function (`(id: string) => ...`)
- Key `root` dan `index` yang hanya sebagai parent grouping

---

## LANGKAH 3 — Ekstrak Data dari Nav Config

Baca file nav config (misal `config-nav-dashboard.tsx`). Untuk tiap nav item, ekstrak:
- `title` — label menu yang ditampilkan ke user
- `path` — URL path (cocokkan ke map dari Langkah 2)
- Role/group — dari nama array (`navDataIndustry`, `navDataRetail`, `navDataKam`, dll)
- `children` — jika ada nested items

Tujuan: cocokkan title nav item ke path URL dari `paths.ts`.

---

## LANGKAH 4 — Inferensi Lokasi Page

Untuk setiap route, inferensikan lokasi folder page dari konvensi project:

```
ROLE_MAP = {
  industry: 'production',
  retail: 'retail',
  kam: 'cam',
  account: 'shared' (jika shared antar role)
}

Page folder = src/app/page/{role}/{path-segments}/
```

Verifikasi: cek apakah folder tersebut memang ada di `src/app/page/` — jika tidak ada, tandai dengan `(belum dibuat)`.

---

## LANGKAH 5 — Cocokkan Service

Baca `.claude/registry/service-registry.md` (jika ada). Cocokkan setiap route ke service berdasarkan:
1. Nama yang mirip antara path segment dan class name service
2. Modul service yang sesuai dengan role route

Jika tidak ada match yang jelas → gunakan `—`.

---

## LANGKAH 6 — Tampilkan Ringkasan & Minta Konfirmasi

Tampilkan ringkasan hasil scan:

```
=== SCAN SELESAI: Menu Registry ===

Ditemukan {N} route di {M} grup/role:

  industry   ({n})  : Customer, Customer Segment, Product, ... (+N lainnya)
  retail     ({n})  : Customer, Outlet, Product, ... (+N lainnya)
  kam        ({n})  : Customer KAM, Product KAM, ... (+N lainnya)
  shared     ({n})  : User, Role, Approval Scheme
  ...

Catatan:
  - {X} route tanpa page folder yang terdeteksi (akan ditandai "(belum dibuat)")
  - {Y} route tanpa service match (akan ditandai "—")

Target file: .claude/registry/menu-registry.md
(akan ditimpa — versi lama akan hilang)   ← jika sudah ada
(akan dibuat baru)                         ← jika belum ada

Lanjutkan? (YA / CANCEL)
```

Truncate ke 4 nama pertama per grup. Urutkan grup alphabetical.

---

## LANGKAH 7 — Proses Konfirmasi

- `CANCEL` → `Init dibatalkan. Tidak ada file yang dimodifikasi.` → stop
- `YA` → lanjut ke Langkah 8
- Jawaban lain → tanya ulang: `Ketik YA untuk melanjutkan atau CANCEL untuk membatalkan.`

---

## LANGKAH 8 — Tulis `.claude/registry/menu-registry.md`

Format output:

```markdown
# Menu Registry

> Cek file ini sebelum modifikasi fitur yang sudah ada. Berisi daftar semua menu beserta path URL, lokasi page, dan service yang digunakan.
> Digenerate oleh `/init:initmenuregistry` — jalankan ulang setelah menambah route baru.

---

## Struktur Role

| Role | Folder Page | Module Service |
|---|---|---|
| Industry / Production | `src/app/page/production/` | `master-production/` |
| Retail | `src/app/page/retail/` | `master-retail/` |
| CAM / KAM | `src/app/page/cam/` | `master-kam/` |

---

## {Grup/Role} — {Nama Section}

### {Sub-group jika ada}

| Menu | Path URL | Page | Service |
|---|---|---|---|
| Customer | `/master/cust/customer/industry` | `production/master/cust/customer/` | `CustomerService` |
```

Aturan format:
- Gunakan H2 (`##`) untuk grup role utama (Industry, Retail, KAM, Shared, dll)
- Gunakan H3 (`###`) untuk sub-group jika nav config punya grouping (Customer Group, Product Group, dll)
- Tiap grup dipisah `---`
- Path URL: gunakan nilai exact dari `paths.ts`
- Page: path relatif dari `src/app/page/`, tanpa `src/app/page/` prefix
- Service: class name dari `service-registry.md`; jika tidak ada match → `—`
- Page yang folder-nya tidak ada → tandai dengan `(belum dibuat)`

---

## LANGKAH 9 — Konfirmasi Selesai

```
✓ Menu registry diupdate: .claude/registry/menu-registry.md
  {N} route | {M} grup

Langkah selanjutnya:
  • /git:prime — load konteks sebelum coding
  • /feature:thisproject — buat fitur baru
  • /init:initskillcomponent — generate skill komponen dari source (jika belum)
```
