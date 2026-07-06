# 9Router V3

> Railway-ready AI gateway: satu endpoint, banyak provider, dengan automatic fallback.

**9Router V3** adalah downstream fork yang dikembangkan oleh **codestorm** dengan fokus pada deployment Railway, persistence, keamanan konfigurasi, dan pengalaman dashboard yang lebih stabil.

Fondasi proyek ini berasal dari:

1. [decolua/9router](https://github.com/decolua/9router) — proyek 9Router asli, routing engine, dan integrasi provider.
2. [ahwanulm/9router-v2](https://github.com/ahwanulm/9router-v2) — rewrite dengan arsitektur Express backend dan Vite + React frontend.
3. **9Router V3 by codestorm** — pengembangan lanjutan yang mengemas arsitektur V2 agar siap digunakan sebagai satu service production di Railway.

Nama repository GitHub masih `9router-v2` untuk menjaga URL fork dan riwayat Git, tetapi nama produk, tampilan, metadata, dan dokumentasinya adalah **9Router V3**.

---

## Perbandingan 9Router, V2, dan V3

| Kemampuan | 9Router asli | 9Router V2 | 9Router V3 |
|---|:---:|:---:|:---:|
| OpenAI-compatible API | ✅ | ✅ | ✅ |
| Multi-provider routing dan fallback | ✅ | ✅ | ✅ |
| Backend dan frontend terpisah | ❌ | ✅ | ✅ |
| Express backend + Vite/React dashboard | ❌ | ✅ | ✅ |
| Docker multi-stage production | ❌ | ❌ | ✅ |
| Satu service untuk API dan dashboard | ❌ | ❌ | ✅ |
| Konfigurasi khusus Railway | ❌ | ❌ | ✅ |
| Railway Volume pada `/data` | ❌ | ❌ | ✅ |
| Railway-generated secret placeholders | ❌ | ❌ | ✅ |
| OIDC otomatis memakai `RAILWAY_PUBLIC_DOMAIN` | ❌ | ❌ | ✅ |
| Material Symbols disimpan lokal | ❌ | ❌ | ✅ |
| Dialog API key dengan loading dan error nyata | ❌ | ❌ | ✅ |
| Branding, logo, dan login V3 | ❌ | ❌ | ✅ |
| QR donasi PayPal pada login | ❌ | ❌ | ✅ |
| Generated browser profiles dikecualikan dari Git | ❌ | ❌ | ✅ |

V3 tidak mengklaim menciptakan ulang routing engine atau integrasi provider. Bagian tersebut tetap merupakan hasil kerja proyek upstream; V3 berfokus pada packaging, deployment, reliability, dan UX.

---

## Fitur

- **OpenAI-compatible REST API** untuk chat, image generation, audio, embeddings, web search, dan endpoint terkait.
- **Multi-provider routing** dengan load balancing, fallback, dan key rotation.
- **Dashboard modern** untuk provider, koneksi, proxy pool, CLI tools, automation, usage, dan pengaturan keamanan.
- **Password dan OIDC authentication** untuk akses dashboard.
- **SQLite persistence** tanpa database service eksternal.
- **Cloudflare Workers AI automation** dengan Playwright, CAPTCHA provider, dan temporary mail.
- **Agent Skills** untuk Claude, Gemini, Codex, serta coding agent lain.
- **Railway-ready container** yang melayani backend dan frontend production dari satu service.

---

## Arsitektur

```text
9router-v3/
├── backend/
│   └── src/
│       ├── routes/       # API dan auto-routed endpoints
│       ├── lib/db/       # SQLite repositories
│       └── automation/   # Browser automation
├── frontend/
│   ├── public/branding/  # Logo dan favicon V3
│   └── src/              # Vite + React dashboard
├── skills/               # Agent SKILL.md files
├── Dockerfile
└── railway.toml
```

---

## Deploy ke Railway

### 1. Buat service

Deploy repository ini sebagai service GitHub:

```text
https://github.com/codestorm-official/9router-v2
```

Railway akan menggunakan `Dockerfile` multi-stage yang tersedia.

### 2. Tambahkan variables

Salin variables dari [`backend/.env.example`](./backend/.env.example) ke tab **Variables** Railway. Minimal:

| Variable | Nilai/keterangan |
|---|---|
| `JWT_SECRET` | `${{secret(32)}}` |
| `INITIAL_PASSWORD` | `${{secret(32)}}` |
| `API_KEY_SECRET` | `${{secret(32)}}` |
| `DATA_DIR` | `/data` |
| `NODE_ENV` | `production` |

`RAILWAY_PUBLIC_DOMAIN` dan `RAILWAY_PRIVATE_DOMAIN` disediakan otomatis oleh Railway dan tidak perlu dibuat manual.

### 3. Pasang persistent volume

Tambahkan Railway Volume melalui dashboard dengan mount path:

```text
/data
```

Dockerfile sengaja tidak memakai instruksi `VOLUME` karena Railway tidak mendukungnya.

### 4. Aktifkan public domain

Buka **Settings → Networking → Generate Domain**. Aplikasi otomatis menggunakan domain tersebut untuk public OIDC origin.

---

## Development Lokal

### Requirements

- Node.js 20+
- Python 3.10+ untuk fitur automation
- Chromium/Camoufox untuk browser automation

### Instalasi

```bash
git clone https://github.com/codestorm-official/9router-v2.git 9router-v3
cd 9router-v3
npm install
cp backend/.env.example backend/.env
```

Ganti sintaks `${{secret(32)}}` dengan secret biasa ketika menjalankan aplikasi di luar Railway.

### Menjalankan aplikasi

```bash
npm run dev       # Backend dan frontend
npm run backend   # Backend saja
npm run frontend  # Frontend saja
npm run build     # Production build
```

Panduan deployment Linux tersedia di [`docs/deployment-linux.md`](./docs/deployment-linux.md).

---

## API Utama

| Endpoint | Fungsi |
|---|---|
| `GET /api/health` | Health check |
| `GET /v1/models` | Daftar model |
| `POST /v1/chat/completions` | Chat completions |
| `POST /v1/images/generations` | Image generation |
| `POST /v1/audio/speech` | Text-to-speech |
| `POST /v1/audio/transcriptions` | Speech-to-text |
| `POST /v1/embeddings` | Embeddings |
| `GET /v1/search` | Web search |

---

## Agent Skills

Mulai dari entry skill berikut:

```text
https://raw.githubusercontent.com/codestorm-official/9router-v2/refs/heads/master/skills/9router/SKILL.md
```

Skill lain tersedia di direktori [`skills/`](./skills/).

---

## Donasi

Jika fork ini membantu pekerjaanmu, dukungan dapat dikirim melalui:

[PayPal — paypal.me/selaris](https://www.paypal.com/paypalme/selaris)

---

## Lisensi dan Atribusi

Proyek ini menggunakan lisensi MIT. Lihat [`LICENSE`](./LICENSE).

Terima kasih kepada:

- [decolua/9router](https://github.com/decolua/9router) atas proyek asli, routing logic, dan integrasi provider.
- [ahwanulm/9router-v2](https://github.com/ahwanulm/9router-v2) atas rewrite V2 dan pemisahan Express + Vite/React.
- **codestorm** sebagai pembuat dan maintainer pengembangan **9Router V3**.

Copyright upstream tetap dipertahankan sebagai bentuk penghargaan dan kepatuhan terhadap lisensi.
