---
description: Generate/update CLAUDE.md (root) dan .claude/README.md berdasarkan kondisi project saat ini — dijalankan di akhir setelah semua init selesai
---

# Command: /init:initrecap

Kamu akan membaca kondisi project saat ini lalu menulis ulang dua file dokumentasi utama: `CLAUDE.md` di root project dan `.claude/README.md`.

---

## ATURAN UTAMA

1. **Baca dulu, tulis belakangan** — scan semua file yang ada sebelum menulis apapun.
2. **Derive dari kondisi nyata** — isi file harus mencerminkan apa yang benar-benar ada di project, bukan template generik.
3. **Satu konfirmasi sebelum tulis** — tampilkan preview ringkas, minta YA sebelum eksekusi.
4. **Cancel kapan saja** — jika user mengetik `CANCEL`, hentikan dan tampilkan: `Init dibatalkan. Tidak ada file yang dimodifikasi.`
5. **Timpa penuh** — jangan merge dengan isi lama, tulis ulang dari awal.

---

## LANGKAH 1 — Scan Kondisi Project

Baca file-file berikut secara paralel:

1. `package.json` — nama project, versi, deskripsi
2. `.claude/common/STACK.md` — tech stack dan konvensi
3. `.claude/common/WORKFLOW.md` — alur kerja
4. `.claude/registry/service-registry.md` — jumlah service dan modul
5. `.claude/registry/menu-registry.md` — jumlah menu dan role
6. `.claude/skills/` — glob `**/*.md` untuk daftar skill yang tersedia
7. `.claude/commands/` — glob `**/*.md` untuk daftar command yang tersedia
8. `CLAUDE.md` — cek apakah sudah ada (fresh install vs re-init)

Derive otomatis:
- `projectName` — dari `package.json`
- `projectDescription` — dari `package.json` atau tanya user jika kosong
- `techStack` — dari `STACK.md`
- `roles` — dari `menu-registry.md`
- `serviceCount` — jumlah total service di `service-registry.md`
- `availableSkills` — daftar nama skill dari file yang ditemukan
- `availableCommands` — daftar command per grup dari folder `commands/`

---

## LANGKAH 2 — Tanya Deskripsi (jika perlu)

Jika `package.json` tidak punya `description` yang informatif, tanya:

```
Deskripsi singkat project ini?
(contoh: "React 18 + TypeScript frontend untuk sistem Dynamic Pricing industri pelumas")
(ketik CANCEL untuk membatalkan)
```

---

## LANGKAH 3 — Tampilkan Preview & Konfirmasi

Tampilkan ringkasan:

```
=== PREVIEW: Init Recap ===

Project     : {projectName}
Deskripsi   : {projectDescription}
Tech Stack  : {ringkasan tech stack}
Roles       : {daftar role}
Services    : {serviceCount} service
Commands    : {N} command ({daftar grup})
Skills      : {daftar skill}

File yang akan ditulis:
  CLAUDE.md               (root project)
  .claude/README.md

Lanjutkan? (YA / CANCEL)
```

---

## LANGKAH 4 — Proses Konfirmasi

- `CANCEL` → stop
- `YA` → lanjut ke Langkah 5
- Jawaban lain → tanya ulang

---

## LANGKAH 5 — Tulis `CLAUDE.md` (root project)

Format output:

```markdown
# CLAUDE.md — {projectName}

## Commands Tersedia

### Inisialisasi (bootstrap kit)

| Command | Fungsi |
|---|---|
| `/init:initmyai` | Inisialisasi AI context untuk project baru |
| `/init:initserviceregistry` | Scan service files → generate service-registry.md |
| `/init:initmenuregistry` | Scan route/nav → generate menu-registry.md |
| `/init:initskillcomponent` | Scan base component → update skill table/form/fields |
| `/init:initcommand` | Generate command operasional sesuai pola project |
| `/init:initrecap` | Generate/update CLAUDE.md + README.md |

### Fitur

| Command | Fungsi |
|---|---|
| `/feature:add-menu` | Wizard interaktif buat menu CRUD master baru |
| `/feature:update-menu` | Wizard interaktif tambah/update fitur di menu yang sudah ada |
| `/feature:thisproject` | Wizard untuk jalur A–G |

### Quality

| Command | Fungsi |
|---|---|
| `/quality:validate` | Validasi hasil implementasi |
| `/quality:review` | Review menyeluruh sebelum PR |

### Git

| Command | Fungsi |
|---|---|
| `/git:prime` | Load konteks project sebelum mulai coding |
| `/git:create-pr` | Buat Pull Request |

---

## Cara Memulai Wizard

```
/feature:thisproject
```
atau
```
Jalankan wizard di .claude/commands/feature/thisproject.md
```

> Untuk membuat menu CRUD master baru, gunakan `/feature:add-menu` langsung.

---

## Wajib Dilakukan Sebelum Mengerjakan Task Apapun

1. Baca `.claude/common/WORKFLOW.md`
2. Tentukan tipe task dan baca skill yang relevan
3. Baca file referensi yang disebutkan di skill
4. Gunakan skill `eslint-rules` — semua kode harus lolos aturan ESLint & Prettier project ini

---

## Struktur `.claude/`

{diagram folder aktual berdasarkan scan — tampilkan struktur nyata yang ditemukan}

---

## Project Overview

{projectDescription}

**Tech Stack:** {ringkasan dari STACK.md}

Detail lengkap ada di `.claude/common/STACK.md`.

---

## Path Alias

{derive dari vite.config atau tsconfig — jika tidak ada, tampilkan "Lihat .claude/common/STACK.md"}
```

---

## LANGKAH 6 — Tulis `.claude/README.md`

Format output:

```markdown
# Panduan Penggunaan AI di Project ini

Folder `.claude/` berisi instruksi, skill, dan registry yang digunakan AI agent untuk memahami project ini secara konsisten.

---

## Cara Kerja Singkat

AI agent membaca file-file di folder ini sebelum menulis kode. Semakin lengkap file ini diisi, semakin konsisten hasil kode yang dihasilkan. **Jangan improvisasi** — AI mengikuti pola yang sudah ada di sini.

---

## Alur Kerja Utama

### Fitur Baru (CRUD master)
```
/git:prime → /feature:add-menu → /quality:validate → /quality:review → /git:create-pr
```

### Fitur Baru (non-CRUD)
```
/git:prime → /feature:thisproject (jalur D) → /quality:validate → /quality:review → /git:create-pr
```

### Update Fitur yang Sudah Ada
```
/git:prime → /feature:update-menu → /quality:validate → /quality:review → /git:create-pr
```

### Fix Bug / Modifikasi Kecil
```
/git:prime → [coding langsung] → /quality:validate
```

---

## Daftar Command

{generate section per command — nama, deskripsi singkat, kapan digunakan, contoh singkat}

---

## File Registry — Wajib Diupdate Setiap Menambah Fitur

| File | Isi | Update saat |
|---|---|---|
| `.claude/registry/service-registry.md` | Daftar semua class service | Buat service baru |
| `.claude/registry/menu-registry.md` | Daftar semua menu + path URL + service | Buat menu baru |
| `.claude/registry/feature/[role]/[menu].md` | Komponen/fitur per menu | Buat atau update fitur |

---

## Command Init (Bootstrap Kit)

| Command | Fungsi |
|---|---|
| `/init:initmyai` | Scan project → generate CLAUDE.md, STACK.md, WORKFLOW.md |
| `/init:initserviceregistry` | Scan service files → generate service-registry.md |
| `/init:initmenuregistry` | Scan routes/nav → generate menu-registry.md |
| `/init:initskillcomponent` | Scan base component → update skill files |
| `/init:initcommand` | Generate command operasional |
| `/init:initrecap` | Generate/update CLAUDE.md + README.md (jalankan terakhir) |

Urutan untuk project baru:
```
/init:initmyai → /init:initserviceregistry → /init:initmenuregistry → /init:initskillcomponent → /init:initcommand → /init:initrecap
```

---

## Struktur Folder `.claude/`

{diagram folder aktual berdasarkan scan}

---

## Tips

- **Selalu mulai dengan `/git:prime`** — AI perlu konteks sebelum menulis kode yang konsisten.
- **Jangan skip `/quality:validate`** — ESLint error lebih mudah diperbaiki sebelum PR.
- **Update registry setelah setiap fitur** — registry adalah "memory" AI untuk sesi berikutnya.
- **Konsistensi > kreativitas** — AI mengikuti pola yang sudah ada. Jika ada pattern baru yang lebih baik, update dulu skill yang relevan.
```

---

## LANGKAH 7 — Konfirmasi Selesai

```
✓ Recap selesai:
  ✓ CLAUDE.md  (root project)
  ✓ .claude/README.md

Init sequence selesai. Project siap digunakan.

Langkah selanjutnya:
  • /git:prime — mulai coding
  • /feature:add-menu — buat fitur pertama
```
