---
description: Setup konfigurasi API untuk testing endpoint langsung dari terminal — base URL, auth pattern, dan cara hit endpoint dengan payload form
---

# Command: /init:initapiconfig

Kamu akan men-scan project ini untuk menemukan konfigurasi API (base URL, auth pattern, env variables), lalu membuat `.claude/common/api-config.md` sebagai referensi saat AI perlu hit endpoint langsung dari terminal.

---

## ATURAN UTAMA

1. **Scan dulu** — baca `.env`, `vite.config`, dan interceptor file sebelum tanya apapun.
2. **Jangan simpan secret** — api-config.md hanya menyimpan pola dan key names, bukan nilai token/secret.
3. **Satu konfirmasi sebelum tulis** — tampilkan preview, minta YA sebelum eksekusi.
4. **Cancel kapan saja** — jika user mengetik `CANCEL`, hentikan dan tampilkan: `Init dibatalkan. Tidak ada file yang dimodifikasi.`

---

## LANGKAH 1 — Scan Konfigurasi API

Baca file-file berikut secara paralel:

1. `.env` atau `.env.local` atau `.env.development` — cari key yang mengandung `API`, `URL`, `BASE`, `ENDPOINT`
2. `vite.config.ts` — cari `proxy` atau env variable definition
3. `src/services/api/api.interceptor.ts` atau file interceptor serupa — cara inject token, header yang dikirim
4. `src/services/media/api.interceptor.ts` — jika ada interceptor kedua untuk media

Derive otomatis:
- `baseUrlKey` — nama env variable untuk base URL (misal: `VITE_API_URL`)
- `authPattern` — cara auth diinject (Bearer token dari localStorage, cookie, header custom, dst)
- `authStorageKey` — key di localStorage/sessionStorage yang menyimpan token
- `defaultHeaders` — header wajib selain Authorization (misal: `X-Client-Key`, `X-Client-Secret`)

---

## LANGKAH 2 — Tanya Base URL Dev

Tanya user:

```
Base URL API untuk environment development?
(contoh: https://api-dev.example.com/v1)
(ketik CANCEL untuk membatalkan)
```

---

## LANGKAH 3 — Tanya Cara Dapat Token

Berdasarkan hasil scan interceptor, tunjukkan pola yang ditemukan lalu tanya:

```
Cara mendapat auth token untuk testing?
1. Login manual lewat browser, copy dari localStorage key "{authStorageKey}"
2. Hit endpoint login dulu dengan curl, ambil token dari response
3. Lainnya (deskripsikan)
(ketik CANCEL untuk membatalkan)
```

Jika pilih opsi 2 — tanya endpoint login dan payload yang dibutuhkan.

---

## LANGKAH 4 — Tampilkan Preview & Konfirmasi

```
=== PREVIEW: API Config ===

Base URL    : {baseUrl}
Auth        : Bearer token dari localStorage["{authStorageKey}"]
Headers     : {defaultHeaders}
Token cara  : {cara dapat token}

File yang akan ditulis:
  .claude/common/api-config.md

Lanjutkan? (YA / CANCEL)
```

---

## LANGKAH 5 — Tulis `.claude/common/api-config.md`

Format output:

```markdown
# API Config

> Referensi untuk AI saat perlu hit endpoint langsung dari terminal.
> JANGAN simpan nilai token/secret di sini — hanya pola dan key names.

---

## Base URL

| Environment | URL |
|---|---|
| Development | {baseUrl} |
| Staging | (isi manual jika berbeda) |

---

## Autentikasi

**Pattern:** Bearer token di header `Authorization`

**Cara dapat token:**
{cara dapat token — langkah-langkah spesifik}

**Contoh ambil token dari localStorage (di browser console):**
```js
localStorage.getItem("{authStorageKey}")
```

---

## Default Headers

```
Authorization: Bearer {TOKEN}
{header lain yang ditemukan dari interceptor}
```

---

## Cara Hit Endpoint dari Terminal

### GET
```bash
curl -s -X GET "{baseUrl}/endpoint" \
  -H "Authorization: Bearer {TOKEN}" \
  -H "Content-Type: application/json" | jq .
```

### POST dengan payload
```bash
curl -s -X POST "{baseUrl}/endpoint" \
  -H "Authorization: Bearer {TOKEN}" \
  -H "Content-Type: application/json" \
  -d '{
    "field1": "value1",
    "field2": "value2"
  }' | jq .
```

### Test payload dari DTO form
Untuk test payload yang sama dengan yang dikirim form, AI akan:
1. Baca DTO file (`xxx.dto.ts`) untuk tahu field yang wajib
2. Baca service file untuk tahu endpoint URL yang dipanggil
3. Generate perintah curl dengan payload sesuai DTO
4. Jalankan dari terminal dan tampilkan response

---

## Catatan

- Token biasanya expire — ambil ulang jika dapat response 401
- Gunakan `| jq .` untuk format response JSON agar mudah dibaca
- Untuk endpoint yang butuh multipart/form-data (upload file), gunakan `-F` bukan `-d`
```

---

## LANGKAH 6 — Konfirmasi Selesai

```
✓ API config tersimpan: .claude/common/api-config.md

Sekarang AI bisa hit endpoint langsung dari terminal.
Contoh penggunaan: "coba hit endpoint list customer dengan token saya"

Langkah selanjutnya:
  • /init:initrecap — update CLAUDE.md + README.md
```
