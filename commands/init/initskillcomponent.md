---
description: Scan base component source code (Table/Grid, Form, Field) dan generate/update skill files di .claude/skills/component/
---

# Command: /init:initskillcomponent

Kamu akan membaca source code base component yang ada di project ini, mengekstrak API dan pola penggunaannya, lalu menulis ulang skill files di `.claude/skills/component/`.

---

## ATURAN UTAMA

1. **Baca source dulu** — baca file component asli, jangan tebak props atau pola dari nama file.
2. **Baca contoh page juga** — baca minimal 1 Grid file dan 1 Form file dari halaman master yang ada untuk memahami pola penggunaan nyata.
3. **Satu konfirmasi sebelum tulis** — tampilkan ringkasan komponen yang ditemukan, minta YA sebelum menulis file.
4. **Cancel kapan saja** — jika user mengetik `CANCEL`, hentikan dan tampilkan: `Init dibatalkan. Tidak ada file yang dimodifikasi.`
5. **Timpa penuh** — jangan merge dengan isi skill lama, regenerate dari source yang dibaca.

---

## LANGKAH 1 — Temukan Base Component Files

Cari semua base component di project. Coba lokasi berikut secara paralel (tidak semua harus ada):

- `src/libs/base/components/` — rekursif
- `src/components/` — rekursif
- `src/app/component/` — rekursif
- `src/ui/` — rekursif

Untuk setiap folder yang ditemukan, identifikasi:
- **Grid/Table**: file yang namanya mengandung `grid`, `table`, `Grid`, `Table`, `DataGrid`
- **Form/Field (Formik-bound)**: file yang namanya mengandung `form`, `field`, `Field`, `Input` — khusus yang terikat ke Formik (`FormikProps` atau `useFormik`)
- **FieldBasic (standalone)**: file yang namanya mengandung `fieldbasic`, `FieldBasic`, atau berada di folder `formbasic/`
- **Modal/Dialog**: file yang namanya mengandung `modal`, `dialog`, `Modal`, `Dialog`
- **Button/Action**: file yang namanya mengandung `button`, `Button`, `action`, `Action`
- **Provider**: cari 1 contoh file `Provider.tsx` dari halaman master untuk memahami pola Context

Jika tidak ada satu pun folder yang ditemukan → tampilkan pesan error dan stop.

---

## LANGKAH 2 — Baca Source Component

Baca setiap file yang ditemukan di Langkah 1. Untuk tiap file, ekstrak:

**Untuk Grid/Table:**
- Nama komponen yang diekspor (namespace atau named)
- Interface/type yang diekspor: props utama, props opsional, tipe spesial (action, column, filter, sort, pagination)
- Konstanta atau enum yang relevan

**Untuk Form/Field:**
- Apakah Formik-bound atau standalone (cek apakah ada `FormikProps` atau `useFormik` di props)
- Nama namespace atau export (misal: `Field.*`, `FieldBasic.*`)
- Setiap member: nama + props utama (wajib dan opsional)
- Perbedaan antara variant (misal: `Field.Dropdown` vs `Field.DropdownApi`)

**Untuk Modal:**
- Props interface lengkap

**Untuk Button/Action:**
- Namespace/export structure

---

## LANGKAH 3 — Baca Contoh Halaman Master

Cari 1 halaman master yang sederhana (bukan transaksi, bukan proposal) dan baca:
1. File `XxxGrid.tsx` atau `XxxTable.tsx` — lihat pola penggunaan Grid lengkap
2. File `XxxForm.tsx` — lihat pola penggunaan Form + Field lengkap
3. File `Provider.tsx` dari halaman yang sama — lihat pola Context

Tujuan: pahami pola yang digunakan developer di halaman nyata, bukan hanya dari interface component.

---

## LANGKAH 4 — Tampilkan Ringkasan & Minta Konfirmasi

Tampilkan ringkasan:

```
=== SCAN SELESAI: Skill Component ===

Komponen yang ditemukan:

  Grid/Table:
    • Grid.Table, Grid.Column, Grid.Filter, Grid.Sort, Grid.Pagination
    • Grid.Toolbar, Grid.Layout, Grid.Refresh, Grid.ExportKam
    • (+ {N} lainnya)

  Field (Formik-bound):
    • Field.Text, Field.Number, Field.Dropdown, Field.DropdownApi
    • Field.Date, Field.Datetime, Field.Time, Field.Boolean
    • (+ {N} lainnya)

  FieldBasic (standalone):
    • FieldBasic.FieldText, FieldBasic.FieldNumber, FieldBasic.FieldDropdown
    • FieldBasic.FieldDropdownApi, FieldBasic.FieldDate, FieldBasic.FieldBoolean
    • (+ {N} lainnya)

  Supporting: CustomModal, FormDelete, Button, Action

File yang akan ditulis/diupdate:
  .claude/skills/component/table/SKILL.md   (akan ditimpa)
  .claude/skills/component/form/SKILL.md    (akan ditimpa)
  .claude/skills/component/fields/SKILL.md  (akan ditimpa)

Lanjutkan? (YA / CANCEL)
```

---

## LANGKAH 5 — Proses Konfirmasi

- `CANCEL` → `Init dibatalkan. Tidak ada file yang dimodifikasi.` → stop
- `YA` → lanjut ke Langkah 6
- Jawaban lain → tanya ulang: `Ketik YA untuk melanjutkan atau CANCEL untuk membatalkan.`

---

## LANGKAH 6 — Tulis Skill Files

Tulis 3 file skill. Format setiap file:

```markdown
---
name: {slug}
description: {satu kalimat}
---

# {Judul Skill}

> {kapan skill ini dibaca}

---

## Struktur Komponen

{penjelasan namespace dan export}

## Pola Penggunaan

{pola kanonik berdasarkan contoh page nyata yang dibaca}

## Props Reference

{tabel atau list props per komponen}

## Contoh Kode

{potongan kode representatif dari contoh page yang dibaca, bukan karangan}
```

### `.claude/skills/component/table/SKILL.md`
- `name: grid-table`
- Dokumentasikan: struktur `XxxGrid.tsx` kanonik, urutan toolbar (Refresh → Filter → Sort → Export), format column, filter/sort wiring, action column + FormDelete, modal wiring
- Sertakan: interface `TableProps`, `ActionColumnProps`, `TableColumnProps`, `FilterField`, `SortField`

### `.claude/skills/component/form/SKILL.md`
- `name: form-formik`
- Dokumentasikan: struktur `XxxForm.tsx` kanonik, `useCore()` destructure, `useFormik` + Yup pattern, `onSubmit` dengan `FormScenarioEnum`, `useEffect` untuk UPDATE populate, pola object-relation dropdown
- Sertakan: pola `initialValues` kosong, pola `setValues` untuk update

### `.claude/skills/component/fields/SKILL.md`
- `name: field-catalog`
- Dokumentasikan: dua kategori (`Field.*` = Formik-bound, `FieldBasic.*` = standalone), kapan pakai masing-masing, tabel semua member + props utama, pola `FieldDropdownApi` + `setFieldValue` untuk object relation

---

## LANGKAH 7 — Konfirmasi Selesai

```
✓ Skill component diupdate:
  .claude/skills/component/table/SKILL.md
  .claude/skills/component/form/SKILL.md
  .claude/skills/component/fields/SKILL.md

Langkah selanjutnya:
  • /git:prime — load konteks sebelum coding
  • /init:initcommand — generate command operasional (add-menu, validate, dll)
```
