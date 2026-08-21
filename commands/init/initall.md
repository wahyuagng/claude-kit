---
description: Jalankan semua init command berurutan dalam satu sesi — setup lengkap AI context dari nol
---

# Command: /init:initall

Kamu akan menjalankan semua init command secara berurutan untuk menyiapkan AI context project secara lengkap.

> Gunakan command ini untuk project baru atau saat ingin reset semua context dari awal.

---

## ATURAN UTAMA

1. **Jalankan berurutan** — setiap langkah harus selesai sebelum lanjut ke langkah berikutnya.
2. **Skip jika sudah ada** — jika output file sudah ada, tanya user apakah ingin di-skip atau di-regenerate.
3. **Cancel kapan saja** — jika user mengetik `CANCEL`, hentikan dan tampilkan: `Init dibatalkan. Langkah yang sudah selesai tetap tersimpan.`
4. **Tampilkan progress** — tampilkan status langkah ke-N dari 7 sebelum setiap langkah.

---

## LANGKAH 0 — Cek Status File yang Sudah Ada

Sebelum mulai, cek keberadaan output file dari setiap langkah:

| File | Langkah |
|---|---|
| `CLAUDE.md` + `.claude/common/STACK.md` | /init:my-ai |
| `.claude/registry/service-registry.md` | /init:service-registry |
| `.claude/registry/menu-registry.md` | /init:menu-registry |
| `.claude/skills/component/table/SKILL.md` | /init:skill-component |
| `.claude/common/api-config.md` | /init:api-config |
| `.claude/commands/feature/add-menu.md` | /init:command |
| `.claude/README.md` | /init:recap |

Untuk setiap file yang sudah ada, tanya user:
```
[nama-file] sudah ada. Skip langkah ini? (YA / regenerate / CANCEL)
```

---

## LANGKAH 1 — My AI (1/7)

```
[1/7] Menjalankan: /init:my-ai
```

Jalankan semua langkah dari `.claude/commands/init/my-ai.md`.

Setelah selesai, tampilkan:
```
✓ [1/7] my-ai selesai → lanjut ke service-registry
```

---

## LANGKAH 2 — Service Registry (2/7)

```
[2/7] Menjalankan: /init:service-registry
```

Jalankan semua langkah dari `.claude/commands/init/service-registry.md`.

Setelah selesai, tampilkan:
```
✓ [2/7] service-registry selesai → lanjut ke menu-registry
```

---

## LANGKAH 3 — Menu Registry (3/7)

```
[3/7] Menjalankan: /init:menu-registry
```

Jalankan semua langkah dari `.claude/commands/init/menu-registry.md`.

Setelah selesai, tampilkan:
```
✓ [3/7] menu-registry selesai → lanjut ke skill-component
```

---

## LANGKAH 4 — Skill Component (4/7)

```
[4/7] Menjalankan: /init:skill-component
```

Jalankan semua langkah dari `.claude/commands/init/skill-component.md`.

Setelah selesai, tampilkan:
```
✓ [4/7] skill-component selesai → lanjut ke api-config
```

---

## LANGKAH 5 — API Config (5/7)

```
[5/7] Menjalankan: /init:api-config
```

Jalankan semua langkah dari `.claude/commands/init/api-config.md`.

Setelah selesai, tampilkan:
```
✓ [5/7] api-config selesai → lanjut ke command
```

---

## LANGKAH 6 — Command (6/7)

```
[6/7] Menjalankan: /init:command
```

Jalankan semua langkah dari `.claude/commands/init/command.md`.

Setelah selesai, tampilkan:
```
✓ [6/7] command selesai → lanjut ke recap
```

---

## LANGKAH 7 — Recap (7/7)

```
[7/7] Menjalankan: /init:recap
```

Jalankan semua langkah dari `.claude/commands/init/recap.md`.

Setelah selesai, tampilkan ringkasan akhir:

```
✓ Init selesai (7/7):
  ✓ [1/7] my-ai           → CLAUDE.md, STACK.md, WORKFLOW.md
  ✓ [2/7] service-registry → .claude/registry/service-registry.md
  ✓ [3/7] menu-registry   → .claude/registry/menu-registry.md
  ✓ [4/7] skill-component → .claude/skills/component/*/SKILL.md
  ✓ [5/7] api-config      → .claude/common/api-config.md
  ✓ [6/7] command         → .claude/commands/feature/*.md
  ✓ [7/7] recap           → CLAUDE.md (final), .claude/README.md

Project siap digunakan.

Langkah selanjutnya:
  • /git:start — load konteks sebelum mulai coding
  • /feature:add-menu — buat fitur pertama
```
