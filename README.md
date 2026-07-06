# 9Router v2

> A self-hosted AI gateway — one endpoint, many providers, auto-fallback.

9Router v2 is a decoupled rewrite of [9Router](https://github.com/decolua/9router) with a clean separation between a dedicated **Express backend** and a **Vite + React frontend**. It exposes an OpenAI-compatible REST API that proxies requests across dozens of AI providers with automatic load balancing, fallback, and key rotation.

---

## Fork Lineage and Differences

This repository is a downstream fork of [ahwanulm/9router-v2](https://github.com/ahwanulm/9router-v2). That project is itself a decoupled rewrite built from the ideas and provider integrations of the original [decolua/9router](https://github.com/decolua/9router).

Full credit for the v2 architecture and its core functionality belongs to those upstream projects. This fork primarily focuses on making the application easier and more reliable to run on Railway.

Compared with `ahwanulm/9router-v2`, this fork adds or changes:

| Area | Changes in this fork |
|---|---|
| Railway deployment | Multi-stage Docker build, production frontend serving, Railway configuration, health checks, and Docker/build ignore rules |
| Persistent data | Uses `/data` as `DATA_DIR`; persistence is provided by a Railway Volume mounted at `/data`, without an unsupported Docker `VOLUME` instruction |
| Environment setup | Railway-friendly `.env.example`, generated secret placeholders, and automatic use of `RAILWAY_PUBLIC_DOMAIN` for the public OIDC origin |
| Dashboard UI | Self-hosted Material Symbols so icons still render when external font services are unavailable |
| Login and donations | Login artwork/testimonials replaced with a PayPal donation QR code; the Donate button links directly to the maintainer's PayPal |
| API keys | Fixes API-key generation under Node ESM and adds visible loading/error feedback to the Create Key dialog |
| Repository hygiene | Generated browser profiles, caches, local databases, build output, and other runtime artifacts are excluded from Git |

This is a deployment-oriented fork rather than a claim of authorship over the original routing engine or provider integrations.

---

## Screenshots

| Login Page | Quota Tracker |
|:---:|:---:|
| ![Login Page](docs/screnshoot/login-page.png) | ![Quota Tracker](docs/screnshoot/quota-tracker.png) |

---

## Features

- **OpenAI-compatible API** — works with any client that supports `/v1/chat/completions`, `/v1/images/generations`, `/v1/audio/speech`, `/v1/embeddings`, etc.
- **Multi-provider routing** — Cloudflare Workers AI, OpenAI, Anthropic, Gemini, Groq, and many more
- **Cloudflare Workers AI Automation** — automates account registration and API key extraction with Playwright + 2Captcha + Ammail temp mail
- **Dashboard UI** — manage providers, connections, proxy pools, CLI tools, and automation from a modern dark-mode interface
- **OIDC / Password authentication** — single sign-on or local credentials
- **Agent Skills** — ready-to-use SKILL.md files for Claude, Gemini, Codex, and other AI coding agents
- **SQLite backend** — zero-dependency local database, no external services required

---

## Architecture

```
9router-v2/
├── backend/          # Express server (port 3001)
│   └── src/
│       ├── routes/   # Auto-routed endpoints (/v1, /api, /auth, ...)
│       ├── db/       # SQLite via better-sqlite3
│       └── automation/ # Playwright automation scripts
├── frontend/         # Vite + React SPA (port 5177)
│   └── src/
│       ├── pages/    # Dashboard pages
│       └── shared/   # Components, hooks, constants
└── skills/           # Agent SKILL.md files
```

---

## Quick Start

### Requirements

- Node.js 20+
- Python 3.10+ (for automation features)
- Chromium (for Playwright automation)

### Install

```bash
git clone https://github.com/codestorm-official/9router-v2.git
cd 9router-v2
npm install
```

### Development

```bash
npm run dev          # Start both backend + frontend concurrently
npm run backend      # Backend only (port 3001)
npm run frontend     # Frontend only (port 5177)
```

### Environment Variables

Copy and configure the backend environment:

```bash
cp backend/.env.example backend/.env
```

Key variables:

| Variable | Description |
|---|---|
| `PORT` | Backend server port |
| `JWT_SECRET` | Secret for JWT signing |
| `INITIAL_PASSWORD` | Initial dashboard password |
| `API_KEY_SECRET` | Secret used when generating API keys |
| `DATA_DIR` | Persistent data directory; use `/data` with a Railway Volume |

---

## Production Deployment

### Railway

1. Create a Railway service from this repository.
2. Add the variables from `backend/.env.example` in the service's **Variables** tab.
3. Attach a Railway Volume with mount path `/data`.
4. Generate a public domain under **Settings → Networking**.

Railway builds the included `Dockerfile`; no separate frontend service or Docker `VOLUME` instruction is required.

### Manual Deployment

#### 1. Build Frontend

```bash
cd frontend && npm run build
```

#### 2. Start Backend

```bash
cd backend && npm start
```

#### 3. Nginx Reverse Proxy

Use the included `nginx.conf` to proxy `/api` and `/v1` to the backend while serving the built frontend statically. See `docs/deployment-linux.md` for a full Linux deployment guide.

---

## Cloudflare Workers AI Automation

The automation module automatically registers Cloudflare Workers AI accounts:

1. Configure **Ammail** temp mail credentials in Dashboard → Automation → Settings
2. Add a **2Captcha** API key for Turnstile solving
3. Add email accounts in the Automation tab and click **Run**
4. API keys are extracted and added to 9Router automatically

---

## Agent Skills

Skills are SKILL.md files for AI coding agents. Paste the entry skill URL into your AI:

```
Read this skill and use it:
https://raw.githubusercontent.com/codestorm-official/9router-v2/refs/heads/master/skills/9router/SKILL.md
```

Browse all skills in the Dashboard → Skills page or in the [`skills/`](./skills/) directory.

---

## API Reference

| Endpoint | Description |
|---|---|
| `GET /api/health` | Health check |
| `GET /v1/models` | List available chat/LLM models |
| `POST /v1/chat/completions` | Chat completions (streaming supported) |
| `POST /v1/images/generations` | Image generation |
| `POST /v1/audio/speech` | Text-to-speech |
| `POST /v1/audio/transcriptions` | Speech-to-text |
| `POST /v1/embeddings` | Text embeddings |
| `GET /v1/search` | Web search |

---

## License

MIT — see [LICENSE](./LICENSE)

---

## Acknowledgements

> 🙏 **Thanks to [ahwanulm/9router-v2](https://github.com/ahwanulm/9router-v2)** for the v2 rewrite and its Express + Vite architecture, and to the original [decolua/9router](https://github.com/decolua/9router) project for the routing logic, provider integrations, and foundation on which both downstream projects were built.
