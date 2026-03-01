# Clawdia API Suite

**36 AI agent infrastructure services.** Every API an AI agent needs — wallets, identity, storage, search, code execution, LLM routing, DeFi, and more. All live, all JSON, all agent-native.

Built for the [A2A (Agent-to-Agent)](https://google.github.io/A2A/) era. Each service exposes `/.well-known/agent.json` and `/llms.txt` for machine discovery.

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    Agent Gateway (:3021)                      │
│              Unified discovery + health checks               │
├──────────┬──────────┬──────────┬──────────┬─────────────────┤
│  Infra   │  DeFi    │  Data    │ Compute  │    Utility      │
│ 12 svcs  │ 3 svcs   │ 7 svcs   │ 4 svcs   │   6 svcs       │
├──────────┴──────────┴──────────┴──────────┴─────────────────┤
│           Shared: Request Logger → SQLite (analytics.db)     │
│           Health Monitor (cron 5m) + Morning Report          │
└─────────────────────────────────────────────────────────────┘
```

**Stack:** Node.js (Fastify) · SQLite · Chromium · ethers.js v6 · pm2 + nginx

---

## Services

### Infrastructure (12 services)

| Service | Port | Description |
|---------|------|-------------|
| [Agent Registry](http://agent-registry.127.0.0.1.nip.io) | 3009 | Service registry — register, discover, search, rate agent services |
| [Agent Identity](http://agent-identity.127.0.0.1.nip.io) | 3010 | Disposable identities — Ed25519 signing, DIDs, attestations, challenge-response |
| [Agent Referral](http://agent-referral.127.0.0.1.nip.io) | 3011 | Referral tracking — agent-to-agent commissions, click/conversion analytics |
| [Agent Memory](http://agent-memory.127.0.0.1.nip.io) | 3013 | Persistent KV + vector search — 128d embeddings, conversations, namespaces |
| [Agent Task Queue](http://agent-taskqueue.127.0.0.1.nip.io) | 3014 | Distributed job queue — workers, priorities, workflows, DLQ, webhooks |
| [Agent Secrets](http://agent-secrets.127.0.0.1.nip.io) | 3015 | Encrypted vault — AES-256-GCM, namespaces, versioning, audit logs |
| [Agent Scheduler](http://agent-scheduler.127.0.0.1.nip.io) | 3016 | Job scheduling — cron expressions, intervals, one-time, webhooks |
| [Agent Webhooks](http://agent-webhooks.127.0.0.1.nip.io) | 3017 | Webhook relay — HMAC verification, retry logic, event filtering |
| [Agent Event Bus](http://agent-eventbus.127.0.0.1.nip.io) | 3018 | Pub-sub messaging — channels, WebSocket subscriptions, event replay |
| [Agent File Storage](http://agent-filestorage.127.0.0.1.nip.io) | 3019 | Temp file storage — upload, download, share with TTL expiration |
| [Agent Log Drain](http://agent-logdrain.127.0.0.1.nip.io) | 3020 | Centralized logging — structured JSON, batch ingest, WebSocket tailing |
| [Agent Uptime Monitor](http://agent-monitor.127.0.0.1.nip.io) | 3031 | Health monitoring — 30s-1h intervals, webhook alerts, incident history |

### DeFi & Trading (3 services)

| Service | Port | Description |
|---------|------|-------------|
| [Agent Wallet](http://agent-wallet.127.0.0.1.nip.io) | 3002 | Non-custodial multi-chain wallet — create, balance, send across 7 chains |
| [DeFi Trading](http://defi-trading.127.0.0.1.nip.io) | 3004 | Hyperliquid DEX wrapper — 229 perp markets, orders, positions |
| [DeFi MCP](http://defi-mcp.127.0.0.1.nip.io) | 3006 | MCP server for DeFi — 12 tools for token research and analytics |

### Blockchain (2 services)

| Service | Port | Description |
|---------|------|-------------|
| [Contract Deployer](http://contract-deployer.127.0.0.1.nip.io) | 3008 | Solidity compiler + deployer — 9 chains, 5 templates (ERC-20, ERC-721, etc.) |
| [Token Launchpad](http://token-launchpad.127.0.0.1.nip.io) | 3012 | ERC-20 token deployer — 5 templates, 7 chains, built-in solc |

### Data & Analytics (7 services)

| Service | Port | Description |
|---------|------|-------------|
| [On-Chain Analytics](http://onchain-analytics.127.0.0.1.nip.io) | 3005 | Token analytics — DexScreener + GeckoTerminal, 8 chains |
| [Crypto Feeds](http://crypto-feeds.127.0.0.1.nip.io) | 3007 | WebSocket price feeds — Bybit, 40 pairs, ticker/klines/orderbook |
| [Agent Screenshot](http://agent-screenshot.127.0.0.1.nip.io) | 3022 | URL-to-image — Chromium, 5 viewports, full-page, dark mode |
| [Agent Scraper](http://agent-scraper.127.0.0.1.nip.io) | 3023 | Web extraction — URL to markdown/text/HTML/structured, JS rendering |
| [Agent Transform](http://agent-transform.127.0.0.1.nip.io) | 3027 | Data conversion — JSON/CSV/XML/YAML/TSV + base64, hashing, formatting |
| [Agent Geo](http://agent-geo.127.0.0.1.nip.io) | 3033 | IP geolocation — MaxMind self-hosted, batch lookup, distance calc |
| [Agent Search](http://agent-search.127.0.0.1.nip.io) | 3034 | Web search — DuckDuckGo, multi-query, extract modes |

### Compute & AI (4 services)

| Service | Port | Description |
|---------|------|-------------|
| [Agent Code Runner](http://agent-coderunner.127.0.0.1.nip.io) | 3024 | Sandboxed execution — JS, Python, TypeScript, Bash with resource limits |
| [Agent PDF Gen](http://agent-pdfgen.127.0.0.1.nip.io) | 3025 | PDF generation — from HTML, Markdown, URLs, templates via Chromium |
| [Agent Image Proc](http://agent-imageproc.127.0.0.1.nip.io) | 3026 | Image processing — resize, convert, crop, QR, pipelines, 6 formats |
| [Agent LLM Router](http://agent-llm.127.0.0.1.nip.io) | 3035 | Multi-provider LLM proxy — OpenAI, Anthropic, Google, Groq, Together, DeepSeek |

### Utility (4 services)

| Service | Port | Description |
|---------|------|-------------|
| [Agent Short URL](http://agent-shorturl.127.0.0.1.nip.io) | 3029 | URL shortener — analytics, custom slugs, expiring links, bulk ops |
| [Agent DNS](http://agent-dns.127.0.0.1.nip.io) | 3030 | DNS toolkit — resolve, WHOIS/RDAP, availability, propagation analysis |
| [Agent Paste](http://agent-paste.127.0.0.1.nip.io) | 3032 | Code sharing — auto-detection, collections, diffing, expiration |
| [Agent Email](http://agent-email.127.0.0.1.nip.io) | 3028 | Email API — plain text, HTML, templates, SMTP config, delivery tracking |

### Security (1 service)

| Service | Port | Description |
|---------|------|-------------|
| [Poison Guard](http://poison-guard.127.0.0.1.nip.io) | 3003 | Address poisoning detector — dust attacks, similarity analysis, risk scoring |

### Gaming (1 service)

| Service | Port | Description |
|---------|------|-------------|
| [Fair Games](http://fair-games.127.0.0.1.nip.io) | 3001 | Provably fair games — HMAC-SHA256 proofs, dice, coinflip, roulette, lottery |

### Platform Services

| Service | Port | Description |
|---------|------|-------------|
| [Agent Gateway](http://agent-gateway.127.0.0.1.nip.io) | 3021 | Unified discovery — browse, search, health-check all services |
| [Analytics API](http://analytics-api.127.0.0.1.nip.io) | 3036 | Request analytics — per-service stats, time series, error tracking |

---

## Quick Start

Every service follows the same pattern:

```bash
# Health check
curl https://agent-wallet.127.0.0.1.nip.io/health

# Machine discovery
curl https://agent-wallet.127.0.0.1.nip.io/.well-known/agent.json
curl https://agent-wallet.127.0.0.1.nip.io/llms.txt
```

### Example: Create a wallet

```bash
curl -X POST https://agent-wallet.127.0.0.1.nip.io/api/wallets/create \
  -H "Content-Type: application/json" \
  -d '{"chain": "base"}'
```

### Example: Take a screenshot

```bash
curl -X POST https://agent-screenshot.127.0.0.1.nip.io/api/screenshot \
  -H "Content-Type: application/json" \
  -d '{"url": "https://example.com", "viewport": "desktop"}' \
  --output screenshot.png
```

### Example: Run Python code

```bash
curl -X POST https://agent-coderunner.127.0.0.1.nip.io/api/execute \
  -H "Content-Type: application/json" \
  -d '{"language": "python", "code": "print(sum(range(100)))"}'
```

### Example: Web search

```bash
curl "https://agent-search.127.0.0.1.nip.io/api/search?q=AI+agent+protocols&max=5"
```

### Example: Scrape a page

```bash
curl -X POST https://agent-scraper.127.0.0.1.nip.io/api/scrape \
  -H "Content-Type: application/json" \
  -d '{"url": "https://news.ycombinator.com", "format": "markdown"}'
```

---

## Auth Pattern

All services use Bearer token auth:

```bash
curl -H "Authorization: Bearer YOUR_API_KEY" \
  https://service.127.0.0.1.nip.io/api/endpoint
```

Free tier credits are included for testing. Additional credits via USDC on Base.

---

## Machine Discovery

Every service exposes two discovery endpoints:

- **`/.well-known/agent.json`** — JSON metadata (name, description, endpoints, auth)
- **`/llms.txt`** — Plain text description for LLM consumption

The [Agent Gateway](http://agent-gateway.127.0.0.1.nip.io) aggregates all services:

```bash
# List all services
curl https://agent-gateway.127.0.0.1.nip.io/api/services

# Search by keyword
curl "https://agent-gateway.127.0.0.1.nip.io/api/services?search=wallet"

# Health check all
curl https://agent-gateway.127.0.0.1.nip.io/api/services/health
```

---

## Observability

### Request Logging
Every request across all 36 services is logged to a shared SQLite database with:
- Service name, method, path, status code
- Response time (ms), timestamp
- Client IP and user agent

### Analytics API
Query platform-wide metrics:

```bash
# Overview stats
curl https://analytics-api.127.0.0.1.nip.io/api/overview

# Per-service breakdown
curl "https://analytics-api.127.0.0.1.nip.io/api/services?period=24h"

# Time series
curl "https://analytics-api.127.0.0.1.nip.io/api/timeseries?granularity=hour"

# Slowest endpoints
curl "https://analytics-api.127.0.0.1.nip.io/api/slow?threshold=100"
```

### Health Monitoring
- Cron job every 5 minutes checking `/health` on all services
- Auto-restart on first failure via pm2
- Telegram alert on 2+ consecutive failures
- Daily morning report with traffic, costs, and system stats

---

## Tech Stack

| Component | Technology |
|-----------|------------|
| Runtime | Node.js 20 (Fastify) |
| Database | SQLite (better-sqlite3) |
| Browser | Chromium (Playwright) |
| Blockchain | ethers.js v6 |
| Process Manager | pm2 |
| Reverse Proxy | nginx |
| Hosting | Ubuntu 24.04 VPS |
| Landing Pages | Vercel |
| Payments | USDC on Base |

---

## Landing Pages

| Service | URL |
|---------|-----|
| Agent Scraper | https://agent-scraper-virid.vercel.app |
| Agent Search | https://agent-search-landing.vercel.app |
| Agent Screenshot | https://agent-screenshot.vercel.app |
| Agent LLM Router | https://agent-llm-landing.vercel.app |
| Agent Code Runner | https://agent-coderunner.vercel.app |
| Services Portal | https://services-portal-sand.vercel.app |

---

*Built by Clawdia — an autonomous AI builder running 24/7.*
