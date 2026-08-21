---
description: Scan route config dan paths.ts di project ini, lalu generate .claude/registry/menu-registry.md dengan daftar lengkap menu, path URL, lokasi page, dan service
---

# Command: /init:menu-registry

Kamu akan men-scan konfigurasi routing project ini, mengekstrak semua menu/route yang terdaftar, lalu menulis ulang `.claude/registry/menu-registry.md`.

---

## ATURAN UTAMA

1. **Tanya referensi dulu** — tanya user lokasi file route config dan naming convention sebelum scan.
2. **Scan dulu, tulis belakangan** — baca `paths.ts` dan nav config sebelum menulis apapun.
3. **Derive dari source, bukan tebak** — path URL harus diambil dari file paths, bukan dikarang.
4. **Satu konfirmasi sebelum tulis** — tampilkan ringkasan, minta YA sebelum eksekusi.
5. **Cancel kapan saja** — jika user mengetik `CANCEL`, hentikan dan tampilkan: `Init dibatalkan. Tidak ada file yang dimodifikasi.`
6. **Regenerate penuh** — timpa file lama sepenuhnya, tidak merge dengan isi sebelumnya.

---

## LANGKAH 0 — Tanya Referensi Path & Naming Convention

Sebelum scan, tanya user:

```
Di mana lokasi file route/path constants di project ini?
(contoh: #src/routes/paths.ts, #src/router/routes.ts, #src/config/routes.ts)
(atau tekan Enter untuk scan otomatis dari src/)
(ketik CANCEL untuk membatalkan)
```

Lalu tanya:

```
Di mana lokasi file nav/menu config di project ini?
(contoh: #src/layouts/config-nav-dashboard.tsx, #src/config/navigation.ts)
(atau tekan Enter untuk scan otomatis dari src/)
```

Dari jawaban user, derive:
- `pathsFile` — path ke file konstanta URL (jika tidak diberikan → coba glob otomatis)
- `navFile` — path ke file nav/menu config (jika tidak diberikan → coba glob otomatis)
- `routerFile` — path ke file router utama (selalu coba temukan otomatis)

---

## LANGKAH 1 — Temukan File Route Config

Gunakan referensi dari Langkah 0. Jika user tidak berikan path spesifik, cari secara paralel:

1. Glob `**/{paths,routes,router,navigation}.ts` dari `src/` — konstanta URL
2. Glob `**/config-nav*.tsx` atau `**/navigation*.ts` dari `src/` — nav items
3. Glob `**/Routes.tsx` atau `**/Router.tsx` dari `src/` — router utama

Jika tidak ditemukan sama sekali → tampilkan pesan error dan stop.

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
> Digenerate oleh `/init:menu-registry` — jalankan ulang setelah menambah route baru.

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

## LANGKAH 8 — Konfirmasi Selesai

```
✓ Menu registry diupdate: .claude/registry/menu-registry.md
  {N} route | {M} grup

Langkah selanjutnya:
  • /git:start — load konteks sebelum coding
  • /feature:thisproject — buat fitur baru
  • /init:skill-component — generate skill komponen dari source (jika belum)
```
