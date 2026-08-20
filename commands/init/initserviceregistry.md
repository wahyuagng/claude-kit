---
description: Scan semua service files di project ini dan generate .claude/registry/service-registry.md dengan metadata lengkap (class, methods, DTO)
---

# Command: /init:initserviceregistry

Kamu akan men-scan semua service files di project ini, mengekstrak metadata (class name, methods, DTO), lalu menulis ulang `.claude/registry/service-registry.md`.

---

## ATURAN UTAMA

1. **Scan dulu, tulis belakangan** — jangan tulis apapun sebelum scan selesai dan user konfirmasi.
2. **Jangan hardcode path** — gunakan glob `**/*.service.ts` dari root project, bukan path spesifik.
3. **Derive sebanyak mungkin** — class name, methods, dan DTO harus diambil dari source file, bukan ditebak.
4. **Satu konfirmasi sebelum tulis** — tampilkan ringkasan hasil scan, minta YA sebelum eksekusi.
5. **Cancel kapan saja** — jika user mengetik `CANCEL`, hentikan dan tampilkan: `Init dibatalkan. Tidak ada file yang dimodifikasi.`

---

## LANGKAH 1 — Scan Service Files

Gunakan Glob dengan pattern `**/*.service.ts` dari root project.

Jika 0 file ditemukan:
- Coba fallback `**/*.service.js`
- Jika masih 0 → tampilkan: `Tidak ditemukan service files (*.service.ts / *.service.js) di project ini.` lalu stop.

Juga cek apakah `.claude/registry/service-registry.md` sudah ada — simpan status ini (`registryExists = true/false`) untuk dipakai di ringkasan nanti.

---

## LANGKAH 2 — Baca & Ekstrak Metadata

Baca setiap service file yang ditemukan. Untuk tiap file, ekstrak:

**Class name**
- Ambil identifier pertama setelah `export class` di baris yang tidak dikomentari.

**Methods**
- Cari semua baris dengan pattern `async <nama>(` di dalam class body.
- Skip baris yang dikomentari: prefixed `//`, atau berada di dalam blok `/* ... */`.
- Urutkan dengan prioritas: `list` → `detail` → `create` → `update` → `delete` → sisanya alphabetical.
- Konstanta urutan: `CRUD_ORDER = ['list', 'detail', 'create', 'update', 'delete']`

**DTO file**
- Cari import statements dengan pattern `from './xxx.dto'` atau `from './xxx.dto.ts'`.
- Jika tidak ada → `(none)`.
- Jika lebih dari satu DTO file → list semua, dipisah spasi.
- Simpan path import persis seperti di source (misal `./customer.dto.ts`), bukan resolved path.

---

## LANGKAH 3 — Inferensi Modul per File

Untuk setiap file, tentukan modul-nya dari path file menggunakan algoritma berikut:

```
NOISE_SEGMENTS = ['services', 'api', 'modules', 'lib', 'libs', 'shared', 'common', 'core']

1. Ambil segmen path setelah 'src/' (pisah dengan '/')
2. Strip semua segmen yang ada di NOISE_SEGMENTS
3. Strip segmen terakhir (nama file)
4. Segmen pertama yang tersisa = nama modul

Fallback:
- Jika tidak ada segmen tersisa → modul = 'misc'
- Jika hanya 1 segmen tersisa dan sama dengan filename stem → modul = segmen itu
```

Contoh:
- `src/services/api/modules/master-production/customer/customer.service.ts`
  → strip noise → `master-production/customer` → modul: `master-production`
- `src/services/api/modules/geographical/city.service.ts`
  → strip noise → `geographical` → modul: `geographical`
- `src/myservice.service.ts`
  → strip noise → kosong → modul: `misc`

Hasil akhir: map `modul → [{ className, filePath, methods[], dto }]`

---

## LANGKAH 4 — Tampilkan Ringkasan & Minta Konfirmasi

Tampilkan ringkasan hasil scan:

```
=== SCAN SELESAI: Service Registry ===

Ditemukan {N} service di {M} modul:

  geographical        (2)  : CityService, ProvincesService
  master-kam         (10)  : ActivityKamService, CustomerKamService, ... (+6 lainnya)
  master-production  (18)  : ActivityService, CustomerService, ... (+14 lainnya)
  ...

Target file: .claude/registry/service-registry.md
(akan ditimpa — versi lama akan hilang)   ← tampilkan ini jika registryExists = true
(akan dibuat baru)                         ← tampilkan ini jika registryExists = false

Lanjutkan? (YA / CANCEL)
```

Aturan truncate: tampilkan maksimal 4 nama class per modul. Jika lebih dari 4, tambahkan `(+N lainnya)`.
Urutkan modul secara alphabetical.

---

## LANGKAH 5 — Proses Konfirmasi

- Jika user menjawab `CANCEL` → tampilkan `Init dibatalkan. Tidak ada file yang dimodifikasi.` → stop.
- Jika user menjawab `YA` → lanjut ke Langkah 6.
- Jika jawaban lain → tanya ulang: `Ketik YA untuk melanjutkan atau CANCEL untuk membatalkan.`

---

## LANGKAH 6 — Tulis `.claude/registry/service-registry.md`

Tulis file dengan format berikut:

```markdown
# Service Registry

> Cek file ini sebelum membuat service baru. Jangan duplikat class yang sudah ada.
> Digenerate oleh `/init:initserviceregistry` — jalankan ulang setelah menambah service baru.
> Dibaca oleh skill `service` dan `thisproject`.

---

## geographical

| Class | File | Methods | DTO |
|---|---|---|---|
| `CityService` | `src/services/api/modules/geographical/city.service.ts` | `list` | (none) |
| `ProvincesService` | `src/services/api/modules/geographical/provinces.service.ts` | `list` | (none) |

---

## master-production

| Class | File | Methods | DTO |
|---|---|---|---|
| `CustomerService` | `src/services/api/modules/master-production/customer/customer.service.ts` | `list` `detail` `create` `update` `delete` | `./customer.dto.ts` |
```

Aturan format:
- Modul diurutkan alphabetical, masing-masing sebagai H2 (`## nama-modul`)
- Tiap modul dipisah `---`
- Methods: setiap method sebagai inline code dipisah spasi, urutan CRUD dulu lalu extras alphabetical
- DTO: path import persis seperti di source file; jika multiple → dipisah spasi; jika tidak ada → `(none)`
- File path: relatif dari root project (mulai dari `src/`)

---

## LANGKAH 7 — Konfirmasi Selesai

Setelah file berhasil ditulis, tampilkan:

```
✓ Service registry diupdate: .claude/registry/service-registry.md
  {N} service | {M} modul

Langkah selanjutnya:
  • /git:prime — load konteks sebelum coding
  • /feature:thisproject — buat fitur baru (registry akan dikonsultasi otomatis)
  • /init:initmenuregistry — scan dan generate menu registry (jika belum)
```
