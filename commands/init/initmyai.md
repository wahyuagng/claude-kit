---
description: Inisialisasi AI context untuk project React+TypeScript baru — scan struktur, selaraskan CLAUDE.md, STACK.md, dan registry
---

# Command: /init:initmyai

Kamu akan menginisialisasi setup AI untuk project ini. Scan struktur project, kumpulkan info dari user, lalu update semua file context agar selaras dengan project baru.

---

## ATURAN UTAMA

1. **Scan dulu, tanya belakangan** — baca `package.json`, `vite.config.ts`, dan struktur `src/` sebelum mengajukan pertanyaan.
2. **Derive sebanyak mungkin** — nama project, versi library, path alias harus diambil dari file project, bukan ditebak.
3. **Satu pertanyaan per pesan** — jangan tumpuk banyak pertanyaan sekaligus.
4. **Konfirmasi sebelum tulis** — tampilkan ringkasan semua perubahan, minta YA sebelum eksekusi.
5. **Cancel kapan saja** — jika user mengetik `CANCEL`, hentikan dan tampilkan: `Init dibatalkan. Tidak ada file yang dimodifikasi.`

---

## LANGKAH 1 — Scan Project

Baca file-file berikut secara paralel:

1. `package.json` — nama project, versi dependencies
2. `vite.config.ts` atau `vite.config.js` — path alias
3. `tsconfig.json` — path alias alternatif (field `paths`)
4. Struktur folder `src/` (1-2 level) — identifikasi pola folder
5. `.claude/common/` — cek apakah folder sudah ada (untuk deteksi re-init)
6. `CLAUDE.md` — cek apakah file sudah ada di root project (fresh install vs re-init)

Derive otomatis:
- `projectName` — dari field `name` di `package.json`
- `techStack` — dari `dependencies` dan `devDependencies`
- `pathAlias` — dari `vite.config` atau `tsconfig.json`
- `srcStructure` — pola folder utama di `src/`
- `claudeMdExists` — `true` jika `CLAUDE.md` ada, `false` jika belum ada

---

## LANGKAH 2 — Tanya Nama Project (Display)

Tanya user:
```
Nama project ini untuk ditampilkan di wizard?
(hasil scan: "{projectName}" dari package.json — tekan Enter untuk pakai ini, atau ketik nama lain)
(ketik CANCEL untuk membatalkan)
```

---

## LANGKAH 3 — Tanya Deskripsi Project

Tanya user:
```
Deskripsi singkat project ini?
(contoh: "React 18 + TypeScript frontend untuk sistem Dynamic Pricing")
(ketik CANCEL untuk membatalkan)
```

---

## LANGKAH 4 — Tanya Struktur Role / Module

Berdasarkan scan `src/`, tunjukkan struktur yang ditemukan lalu tanya:
```
Struktur folder yang ditemukan di src/:
{tampilkan folder utama}

Apakah sudah benar? (YA / deskripsikan perbedaan / CANCEL)
```

---

## LANGKAH 5 — Ringkasan & Konfirmasi

Tampilkan ringkasan:
```
=== RINGKASAN: Init AI Context ===

Project     : {projectName}
Deskripsi   : {deskripsi}
Tech Stack  : {daftar library + versi}
Path Alias  : {alias yang ditemukan}
Src Pattern : {pola folder src/}

File yang akan dibuat/diupdate:
  [ ] CLAUDE.md — dibuat baru (belum ada)            ← tampilkan jika claudeMdExists = false
  [ ] CLAUDE.md — update nama, deskripsi, tech stack, path alias  ← tampilkan jika claudeMdExists = true
  [ ] .claude/common/STACK.md — update versi library dan path alias
  [ ] .claude/registry/menu-registry.md — reset ke template kosong
  [ ] .claude/registry/service-registry.md — reset ke template kosong
  [ ] .claude/commands/feature/thisproject.md — update nama project di wizard
  [ ] .claude/common/STACK.md — dibuat baru dari template, disesuaikan dengan project
  [ ] .claude/common/WORKFLOW.md — dibuat baru dari template
  [ ] .claude/common/eslint.md — dibuat baru dari template
  [ ] .claude/common/settings.local.json — dibuat baru (kosong)

Ketik YA untuk eksekusi, CANCEL untuk membatalkan, atau koreksi bagian yang salah:
```

---

## LANGKAH 6 — EKSEKUSI (setelah YA)

### 6.1 — CLAUDE.md (buat baru atau update)

**Jika `claudeMdExists = false` (fresh install) — buat file baru:**

```markdown
# CLAUDE.md — {projectName}

## Commands Tersedia

| Command | Fungsi |
|---|---|
| `/feature:add-menu` | Wizard interaktif buat menu CRUD master baru |
| `/feature:update-menu` | Wizard interaktif tambah/update fitur di menu yang sudah ada |
| `/feature:thisproject` | Wizard untuk jalur A–G (service, route, table, form, dll) |
| `/git:prime` | Load konteks project sebelum mulai coding |
| `/quality:validate` | Validasi hasil implementasi |
| `/quality:review` | Review menyeluruh sebelum PR |
| `/git:create-pr` | Buat Pull Request |
| `/init:initmyai` | Inisialisasi AI context untuk project baru |

## Wajib Dilakukan Sebelum Mengerjakan Task Apapun

1. Baca `.claude/common/WORKFLOW.md`
2. Tentukan tipe task dan baca skill yang relevan
3. Baca file referensi yang disebutkan di skill sebelum menulis kode
4. Gunakan skill `eslint-rules` — semua kode harus lolos aturan ESLint & Prettier project ini

Prinsip utama: **konsistensi > kreativitas**.

## Project Overview

{projectName} — {deskripsi}

**Tech Stack:** {ringkasan tech stack dari package.json}

Detail lengkap ada di `.claude/common/STACK.md`.

## Path Alias

{path alias dari vite.config / tsconfig}
```

**Jika `claudeMdExists = true` (re-init) — update saja:**
- Ganti judul dan semua referensi nama project lama → `{projectName}`
- Update bagian `## Project Overview`: nama, deskripsi, tech stack
- Update bagian `## Path Alias` sesuai hasil scan
- Pastikan referensi ke `commands/thisproject.md` sudah benar

### 6.2 — Update .claude/common/STACK.md
- Update tabel `## Tech Stack` — ganti versi sesuai `package.json`
- Update bagian `## Path Alias` — sesuaikan alias dengan konfigurasi bundler
- Update contoh import agar menggunakan path alias yang benar
- **Jangan ubah**: pola Service, Form, Grid, Provider, FormScenarioEnum, Toast & Error Handling

### 6.3 — Reset .claude/registry/menu-registry.md

Ganti seluruh isi dengan:
```markdown
# Menu Registry

> Cek file ini sebelum modifikasi fitur yang sudah ada.

---

## Menu

| Menu | Path URL | Page | Service |
|---|---|---|---|
```

### 6.4 — Reset .claude/registry/service-registry.md

Ganti seluruh isi dengan:
```markdown
# Service Registry

> Cek file ini sebelum membuat service baru. Jangan duplikat class yang sudah ada.

---

## Services

| Class | File |
|---|---|
```

### 6.5 — Update .claude/commands/feature/thisproject.md
- Ganti `# Wizard: {nama-lama}` → `# Wizard: {projectName}`
- Ganti teks welcome di `question:` → `"Selamat datang di wizard {projectName}. Pilih jalur:"`

### 6.6 — Setup .claude/common/

Cek apakah folder `.claude/common/` sudah ada:
- **Jika sudah ada** — tanya user: `Folder .claude/common/ sudah ada. Overwrite semua isinya? (YA / SKIP / CANCEL)`
  - `SKIP` → lewati langkah ini sepenuhnya
  - `CANCEL` → hentikan wizard
- **Jika belum ada** — buat folder dan semua file di bawah ini

Buat 4 file berikut di `.claude/common/`:

**`.claude/common/STACK.md`**
Copy isi dari `.claude/common/STACK.md` project ini sebagai template, lalu sesuaikan:
- Tabel `## Tech Stack` → update nama dan versi library dari `package.json` hasil scan
- Bagian `## Path Alias` → sesuaikan dengan konfigurasi `vite.config` / `tsconfig.json` project target
- Contoh import → sesuaikan path alias yang benar
- Bagian lain (Konvensi Penamaan, Pola Service, Pola Form, dll) → pertahankan apa adanya

**`.claude/common/WORKFLOW.md`**
Copy isi dari `.claude/common/WORKFLOW.md` project ini. Tidak perlu diubah.

**`.claude/common/eslint.md`**
Copy isi dari `.claude/common/eslint.md` project ini. Tidak perlu diubah.

**`.claude/common/settings.local.json`**
Buat file baru dengan isi:
```json
{}
```

---

## LANGKAH 7 — Konfirmasi Selesai

Setelah semua file diupdate, tampilkan:
```
✓ Init selesai untuk project: {projectName}

File yang diupdate:
  ✓ CLAUDE.md  (dibuat baru)    ← jika fresh install
  ✓ CLAUDE.md  (diupdate)       ← jika re-init
  ✓ .claude/common/STACK.md
  ✓ .claude/registry/menu-registry.md  (direset)
  ✓ .claude/registry/service-registry.md  (direset)
  ✓ .claude/commands/feature/thisproject.md
  ✓ .claude/common/STACK.md      (dibuat baru)
  ✓ .claude/common/WORKFLOW.md   (dibuat baru)
  ✓ .claude/common/eslint.md     (dibuat baru)
  ✓ .claude/common/settings.local.json  (dibuat baru)

Langkah selanjutnya:
  • /feature:thisproject — mulai buat fitur pertama
  • /git:prime — load konteks sebelum coding manual
```
