# claude-kit

Bootstrap kit untuk setup AI context di project React+TypeScript baru menggunakan the assistant.

---

## Cara Menggunakan

### 1. Install ke project baru

```bash
npx degit wahyuagng/claude-kit/commands .claude/commands
```

Perintah ini akan meng-copy folder `commands/init/` ke `.claude/commands/` di project kamu — tanpa git history, tanpa file lain.

### 2. Jalankan init sequence

Buka the assistant di project baru, lalu jalankan command berikut **secara berurutan**:

```
/init:initmyai
```
Scan project → generate `CLAUDE.md` (root) + `.claude/common/STACK.md` + `.claude/common/WORKFLOW.md` + `.claude/common/eslint.md`

```
/init:initserviceregistry
```
Scan semua `*.service.ts` → generate `.claude/registry/service-registry.md`

```
/init:initmenuregistry
```
Scan `paths.ts` + nav config → generate `.claude/registry/menu-registry.md`

```
/init:initskillcomponent
```
Scan base component source (Table/Grid, Form, Field) → generate skill files di `.claude/skills/component/`

```
/init:initcommand
```
Generate command operasional sesuai pola project ini (`feature/`, `quality/`, `git/`)

```
/init:initrecap
```
Generate/update `CLAUDE.md` (root) + `.claude/README.md` — **jalankan terakhir**

---

## Struktur yang Dihasilkan

Setelah semua init selesai, `.claude/` project kamu akan punya struktur ini:

```
.claude/
├── common/
│   ├── WORKFLOW.md
│   ├── STACK.md
│   ├── eslint.md
│   └── settings.local.json
├── registry/
│   ├── service-registry.md
│   ├── menu-registry.md
│   └── feature/
├── commands/
│   ├── init/          ← dari claude-kit
│   ├── feature/       ← di-generate oleh /init:initcommand
│   ├── quality/       ← di-generate oleh /init:initcommand
│   └── git/           ← di-generate oleh /init:initcommand
├── skills/            ← di-generate oleh /init:initskillcomponent
└── README.md          ← di-generate oleh /init:initrecap
```

---

## Catatan

- `commands/init/` adalah satu-satunya folder yang di-copy dari repo ini — semua file lain di-generate dari source project kamu sendiri
- Setiap command init **scan dulu, derive dari source** — tidak ada yang hardcode atau di-copy dari project lain
- Jalankan ulang command init kapanpun struktur project berubah besar (misal: pindah ke library baru, tambah role baru)
