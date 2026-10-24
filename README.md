# ApexSEO — AI-Native SEO & Search Intelligence Suite

> Enterprise-grade, open-source alternative to Semrush and Ahrefs with native Model Context Protocol (MCP) support for AI coding assistants.

---

## 1. Overview & Purpose
**ApexSEO** is a self-hostable, pay-as-you-go SEO analytics platform and AI search visibility engine. Unlike costly seat-based subscriptions ($130–$500/month per user), ApexSEO connects directly to wholesale data providers (**DataForSEO**) with **zero artificial markups** and **100% unlocked features**.

### Key Capabilities
* **Keyword Research & Discovery:** Global search volumes, keyword difficulty (KD), search intent, CPC, and live SERP analysis across 140+ countries.
* **Rank Tracking & Serps:** Scheduled position tracking, historical rank fluctuations, and SERP feature monitoring.
* **Competitor & Domain Intelligence:** Organic search traffic estimations, competitor keyword gaps, top pages, and market overlap.
* **Backlink Profiler:** Referring domains, anchor text analysis, backlink health, and new/lost link alerts.
* **Technical Site Audits:** Page-level crawl audits, broken link detection, meta tag validation, and site health metrics.
* **AI Visibility & Prompt Explorer:** Track your brand citations, AI answers, and search visibility across OpenAI ChatGPT, Google Gemini, and Perplexity.
* **In-App AI Agent ("SAM"):** Autonomous SEO assistant to answer queries and recommend action plans directly in the dashboard.
* **Model Context Protocol (MCP) Server:** Built-in MCP endpoints allowing AI agents (Claude Code, Cursor, OpenClaw, Codex) to pull live SEO metrics during development sessions.

---

## 2. Architecture & Directory Layout

```
apps/ApexSEO/
├── src/                        # Full-Stack Application Source
│   ├── client/                 # React UI (TanStack Router, Tailwind CSS, Lucide, Radix)
│   │   ├── components/         # Reusable UI widgets and navigation
│   │   ├── features/           # Feature slices (audit, keywords, backlinks, sam, etc.)
│   │   └── styles/             # Application styles and design tokens
│   ├── server/                 # Server-side API & Handlers
│   │   ├── mcp/                # Model Context Protocol (MCP) server implementation
│   │   └── dataforseo/         # Direct DataForSEO API client integrations
│   ├── db/                     # Drizzle ORM schema & migrations (SQLite/D1 & Postgres)
│   └── routes/                 # TanStack Start file-based routing
├── deploy/                     # Deployment targets
│   ├── docker/                 # Container runtime & entrypoint scripts
│   └── alchemy/                # Cloudflare deploy orchestration
├── docs/                       # Architectural and technical documentation
│   ├── DATAFORSEO_API_KEY.md   # DataForSEO setup guide
│   ├── LOCAL_DEVELOPMENT.md    # Local pnpm/Node.js workflow
│   ├── SELF_HOSTING_DOCKER.md  # Docker compose guide
│   └── SELF_HOSTING_CLOUDFLARE.md # Cloudflare Workers guide
├── compose.yaml                # Production Docker Compose orchestration
├── package.json                # Project dependencies and script definitions
├── .env                        # Active environment configuration
└── .env.local                  # Local dev overrides
```

---

## 3. Environment Variables & Credentials (`appinfo11.txt`)

All master API keys, environment settings, and provider secrets are centrally tracked in **`appinfo11.txt`** under **SECTION 6: APEXSEO**.

### Key Variables in `.env` / `.env.local`:

| Variable | Description | Default / Source |
| :--- | :--- | :--- |
| `DATAFORSEO_API_KEY` | Base64-encoded `login:password` for DataForSEO API. | Required for live data |
| `AUTH_MODE` | Authentication mode (`local_noauth`, `cloudflare_access`, `hosted`). | `local_noauth` (Local testing) |
| `OPENROUTER_API_KEY` | Key for SAM AI Assistant & Prompt Explorer. | Loaded from `appinfo11.txt` |
| `PORT` | Local web application port. | `3001` |
| `GOOGLE_MAPS_API_KEY`| (Optional) Google Maps Tile API key for Local SEO map views. | Optional |
| `GOOGLE_CLIENT_ID`   | (Optional) For Google Search Console OAuth. | Optional |

#### Generating your DataForSEO API Key:
1. Create an account at [DataForSEO](https://dataforseo.com/).
2. Obtain your API login and password.
3. In PowerShell, generate the Base64 key:
   ```powershell
   [Convert]::ToBase64String([Text.Encoding]::UTF8.GetBytes("YOUR_EMAIL:YOUR_PASSWORD"))
   ```
4. Paste the resulting string into `DATAFORSEO_API_KEY=` in `.env` and `appinfo11.txt`.

---

## 4. Setup & Running the Application

### Option A: Quickstart via Docker (Recommended for instant setup)
Docker provides the fastest isolated run without needing manual database setup:

```powershell
# Navigate to the project directory
cd "c:\Users\tarik\Desktop\MY TOOLS\apps\ApexSEO"

# Start the container stack in detached mode
docker compose up -d

# Check container status
docker compose ps
```
The application will be accessible at: **`http://localhost:3001`**.

---

### Option B: Local Node.js / pnpm Development

```powershell
# 1. Navigate to the project directory
cd "c:\Users\tarik\Desktop\MY TOOLS\apps\ApexSEO"

# 2. Install dependencies using pnpm
pnpm install

# 3. Apply local database migrations (SQLite)
pnpm run db:migrate:local

# 4. Start local development server
pnpm run dev
```

---

### Option C: Production Deployment to Cloudflare Workers

ApexSEO is built natively for Cloudflare Workers (using D1 Database and KV storage):

```powershell
# 1. Login to Cloudflare Wrangler
pnpm exec wrangler login

# 2. Run automated self-hosting deployment
pnpm run deploy:selfhost
```
For detailed custom domain and Cloudflare Access Zero-Trust setup, refer to [`docs/SELF_HOSTING_CLOUDFLARE.md`](./docs/SELF_HOSTING_CLOUDFLARE.md).

---

## 5. Connecting AI Agents via MCP
To let AI coding tools (Claude Code, Cursor, Windsurf, OpenClaw) query your live SEO data:
* **MCP URL:** `http://localhost:3001/api/mcp`
* **Transport:** Server-Sent Events (SSE) or JSON-RPC.
