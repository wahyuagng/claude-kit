---
description: Generate command operasional (.claude/commands/) berdasarkan pola project ini — add-menu, update-menu, validate, review, create-pr, prime, thisproject
---

# Command: /init:command

Kamu akan men-scan project ini dan meng-generate (atau memperbarui) command operasional di `.claude/commands/` agar selaras dengan struktur, pola kode, dan registry project ini.

---

## ATURAN UTAMA

1. **Scan dulu** — baca struktur project, skill files, dan registry sebelum menulis command apapun.
2. **Derive dari source** — nama role, group folder, path alias, service pattern harus diambil dari source, bukan ditebak.
3. **Jangan timpa command yang sudah ada tanpa konfirmasi** — jika command sudah ada, tanya user apakah ingin di-reset atau dilewati.
4. **Satu konfirmasi sebelum tulis** — tampilkan daftar command yang akan dibuat/diupdate, minta YA sebelum eksekusi.
5. **Cancel kapan saja** — jika user mengetik `CANCEL`, hentikan dan tampilkan: `Init dibatalkan. Tidak ada file yang dimodifikasi.`

---

## LANGKAH 1 — Scan Project

Baca file-file berikut secara paralel:

1. `CLAUDE.md` — aturan utama dan daftar command yang sudah didokumentasikan
2. `.claude/common/STACK.md` — tech stack dan konvensi
3. `.claude/registry/service-registry.md` — daftar service yang ada
4. `.claude/registry/menu-registry.md` — daftar menu dan role yang ada
5. `.claude/skills/` — daftar skill yang tersedia (glob `**/*.md`)
6. `src/routes/paths.ts` — path URL yang terdaftar
7. `src/layouts/config-nav-dashboard.tsx` — nav data per role

Derive otomatis:
- `roles` — dari nav data: nama array (`navDataIndustry` → `industry`, dst)
- `rolePageMap` — dari menu-registry: role → folder page (`production`, `retail`, `cam`)
- `moduleMap` — dari service-registry: role → module service (`master-production`, dst)
- `groupFolders` — dari paths.ts dan menu-registry: daftar group folder yang ada (`cust`, `prod`, `sh`, dst)
- `availableSkills` — dari glob `.claude/skills/*/SKILL.md`

---

## LANGKAH 2 — Cek Command yang Sudah Ada

Cek file berikut — apakah sudah ada atau belum:

```
.claude/commands/feature/add-menu.md
.claude/commands/feature/update-menu.md
.claude/commands/feature/thisproject.md
.claude/commands/quality/validate.md
.claude/commands/quality/review.md
.claude/commands/git/create-pr.md
.claude/commands/git/prime.md
```

Untuk setiap file yang **sudah ada**, tandai sebagai `(ada — akan dilewati)`.
Untuk setiap file yang **belum ada**, tandai sebagai `(belum ada — akan dibuat)`.

---

## LANGKAH 3 — Tampilkan Ringkasan & Minta Konfirmasi

Tampilkan ringkasan:

```
=== SCAN SELESAI: Init Command ===

Project info yang terdeteksi:
  Roles    : industry, retail, kam
  Groups   : cust, prod, sh, sp, plant, account
  Skills   : grid-table, form-formik, field-catalog, service, router, folder, ...

Command yang akan diproses:

  add-menu.md      (belum ada — akan dibuat)
  update-menu.md   (belum ada — akan dibuat)
  validate.md      (ada — akan dilewati)
  review.md        (belum ada — akan dibuat)
  create-pr.md     (ada — akan dilewati)
  prime.md         (ada — akan dilewati)
  thisproject.md   (belum ada — akan dibuat)

Ingin reset command yang sudah ada? (TIDAK / ketik nama command yang ingin di-reset, pisah koma)
```

Tunggu jawaban user. Jika `TIDAK` → lanjut hanya untuk yang `belum ada`. Jika user menyebut nama command → tambahkan ke daftar yang akan ditulis ulang.

---

## LANGKAH 4 — Proses Konfirmasi Akhir

Setelah user menjawab Langkah 3, tampilkan daftar final:

```
Command yang akan ditulis/diupdate:
  [ ] add-menu.md
  [ ] update-menu.md
  [ ] review.md
  [ ] thisproject.md

Command yang dilewati (sudah ada):
  - validate.md
  - create-pr.md
  - prime.md

Lanjutkan? (YA / CANCEL)
```

- `CANCEL` → stop
- `YA` → lanjut ke Langkah 5

---

## LANGKAH 5 — Generate Command Files

Generate setiap command yang perlu dibuat. Isi setiap command harus **spesifik ke project ini** — gunakan data yang di-derive di Langkah 1. Jangan gunakan placeholder generik atau hardcode pola dari project lain.

**Prinsip derive:**
- Role dan group folder → dari `rolePageMap` dan `groupFolders` hasil scan
- Service pattern → dari `service-registry.md` (class structure, method names, DTO convention)
- Component pattern → dari `availableSkills` (skill apa yang tersedia, apa nama komponen, interface yang dipakai)
- Path URL pattern → dari `paths.ts` hasil scan
- Validation/form library → dari `STACK.md` (Formik/Yup, React Hook Form, Zod, dst — derive, jangan asumsi)
- UI library → dari `STACK.md` (MUI, Ant Design, Tailwind, dst — derive, jangan asumsi)

### `/feature:add-menu` — Wizard CRUD Master Baru

Command wizard interaktif. Isi yang harus spesifik ke project:
- Daftar role yang tersedia (dari `rolePageMap`)
- Daftar group folder (dari `groupFolders`)
- Pattern path URL (dari `paths.ts`)
- Daftar skill yang dibaca saat eksekusi (dari `availableSkills`)
- Checklist file yang dibuat/dimodifikasi (sesuai struktur folder project)
- Nama method service yang lazim di project ini (dari `service-registry.md`)

Alur wajib:
1. Tanya nama fitur → derive `featureKebab`, `featureClass`, `serviceClass`
2. Tanya tipe (CRUD / View)
3. Tanya role → derive `moduleGroup`, folder page
4. Tanya group folder
5. Tanya endpoint API
6. Tanya DTO fields
7. Tanya kolom tabel
8. Tanya form fields (jika CRUD)
9. Tanya service dependency
10. Derive + konfirmasi path URL
11. Tampilkan ringkasan lengkap → minta YA
12. Baca skill → eksekusi → update registry

### `/feature:update-menu` — Wizard Update Fitur yang Ada

Command wizard untuk menambah/mengubah fitur di menu yang sudah ada. Alur:
1. Tanya nama menu yang ingin diupdate
2. Baca `.claude/registry/feature/[nama-menu].md` (jika ada) untuk context
3. Tanya perubahan apa yang ingin dilakukan
4. Tampilkan file yang akan terpengaruh
5. Minta konfirmasi → baca skill relevan → eksekusi

### `/quality:validate` — Validasi Implementasi

Command validasi sebelum PR. Konten yang harus spesifik ke project:
- Perintah lint yang sesuai (dari `package.json` — cek script `lint`, `eslint`, dst)
- Checklist pola kode yang mengikuti konvensi **project ini** — derive dari skill files yang tersedia:
  - Service: derive dari pola di `service-registry.md`
  - Component: derive dari skill `table`, `form`, `fields` yang ada
  - Routing: derive dari struktur `paths.ts` yang ditemukan
  - Registry: cek `registry/service-registry.md`, `registry/menu-registry.md`, `registry/feature/`

Jangan hardcode nama class, interface, atau library — semua harus derive dari scan project.

### `/quality:review` — Review Sebelum PR

Command review menyeluruh. Langkah:
1. Cek semua file yang berubah (`git diff --name-only`)
2. Baca setiap file yang berubah
3. Review konsistensi: naming, pola, import path
4. Review kelengkapan: semua file yang seharusnya ada sudah ada
5. Review registry: sudah diupdate sesuai perubahan
6. Tampilkan hasil review: ✓/✗ per kategori

### `/git:create-pr` — Buat Pull Request

Command buat PR ke GitHub. Langkah:
1. Cek `git status` dan `git log` — tampilkan ringkasan perubahan
2. Baca ticket/spec jika ada di `.claude/prd/`
3. Generate judul PR (max 70 karakter)
4. Generate body PR dengan format: Summary (bullet) + Test Plan (checklist)
5. Tampilkan preview → minta konfirmasi
6. Eksekusi `gh pr create`

### `/git:start` — Load Konteks Sebelum Coding

Command load konteks. File yang selalu dibaca:
1. `CLAUDE.md`
2. `.claude/common/WORKFLOW.md`
3. `.claude/common/STACK.md`
4. `.claude/registry/service-registry.md`
5. `.claude/registry/menu-registry.md`

Tambahan jika ada nama fitur/ticket:
- `.claude/registry/feature/[nama].md`
- `.claude/prd/` (glob, jika ada)

Tampilkan konfirmasi siap + rekomendasi langkah selanjutnya.

### `/feature:thisproject` — Wizard Multi-Jalur

Command wizard dengan beberapa jalur — sesuaikan jalur dengan tipe task yang relevan di project ini (derive dari skill yang tersedia):

```
Pilih jalur:
A — Buat service + DTO saja
B — Daftarkan route + nav saja
C — Buat fitur CRUD master baru lengkap  → redirect ke /feature:add-menu
D — Buat fitur non-CRUD (dashboard, report, monitoring, transaksi)
E — Update fitur yang sudah ada  → redirect ke /feature:update-menu
F — Buat multiple input (array of objects)  ← hanya jika skill multiple-input tersedia
G — Modifikasi komponen tabel atau form saja
```

Setiap jalur yang redirect ke command lain cukup redirect — jangan duplikasi logikanya.

---

## LANGKAH 6 — Update CLAUDE.md (jika perlu)

Setelah semua command ditulis, cek apakah tabel command di `CLAUDE.md` sudah mencakup semua command yang baru dibuat. Jika ada yang belum terdaftar, tambahkan entry-nya ke tabel.

---

## LANGKAH 7 — Konfirmasi Selesai

```
✓ Command diupdate: .claude/commands/

  ✓ add-menu.md
  ✓ update-menu.md
  ✓ review.md
  ✓ thisproject.md
  - validate.md    (dilewati)
  - create-pr.md   (dilewati)
  - prime.md       (dilewati)

Langkah selanjutnya:
  • /git:start — load konteks sebelum coding
  • /feature:add-menu — coba wizard buat fitur baru
  • /quality:validate — validasi implementasi yang ada
```
