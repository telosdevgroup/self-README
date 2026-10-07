---
title: "avathings-web"
type: "system"
tags: [web-platform, ai-native-development, fastapi, bare-metal, llms-txt, geo, cloudflare-tunnel, aiosqlite, systems-documentation]
status: "stable"
last_updated: 2026-10-06
local_source: "/home/dev/Code/tdg/avathings-web"
live_url: "https://avathings.com"
---

# avathings-web

> **AI-Built Web Platform & Generative Engine Optimization (GEO) Hub for AvaThings.**  
> Bare-metal FastAPI service built with autonomous AI pairing, running behind Cloudflare Tunnel. Engineered intentionally for machine ingestion via `/llms.txt` standards and privacy-preserving SHA-256 telemetry.

```
                  [ Web Visitors & Autonomous AI Bots ]
                                │
                                ▼
                ┌───────────────────────────────┐
                │      Cloudflare Edge CDN      │
                │  - Encrypted Tunnel Ingress   │
                │  - Unblocked AI Crawler Paths │
                └───────────────┬───────────────┘
                                │ :8085 Ingress
                                ▼
                ┌───────────────────────────────┐
                │     FastAPI / Uvicorn (:8085) │
                │  - Privacy SHA-256 Telemetry  │
                │  - Async SQLite (aiosqlite)   │
                │  - Systemd Service Daemon     │
                └───────────────┬───────────────┘
                                │
        ┌───────────────────────┴───────────────────────┐
        ▼                                               ▼
┌────────────────────────────────┐     ┌────────────────────────────────┐
│   Agent-First Surfaces (GEO)   │     │       Human Documentation      │
│ - /llms.txt (llmstxt.org spec) │     │ - Jinja2 Dynamic Render        │
│ - /llms-full.txt (Prompt ready)│     │ - Real-time command cheat-sheet│
│ - JSON-LD Schema (SoftwareApp) │     │ - Bare-metal deployment guides │
└────────────────────────────────┘     └────────────────────────────────┘
```

---

## 1. Context: Built With AI, Optimized For AI

`avathings-web` was built from zero to production as an AI-native pair programming project. Rather than treating an AI assistant as an occasional autocomplete, the architecture, schema design, and deployment pipelines were co-engineered end-to-end to serve both human engineers and downstream AI crawlers.

### Key AI-Native Design Principles
1. **First-Class Machine Readability**: Built around the emerging [llmstxt.org](https://llmstxt.org) standard (`/llms.txt` and `/llms-full.txt`), giving LLMs (ChatGPT, Claude, Perplexity) instant, token-efficient, cheat-sheet context to accurately cite and explain the toolsuite without parsing messy client-side DOMs.
2. **Generative Engine Optimization (GEO)**: Explicit schema embedding (`SoftwareApplication` JSON-LD) and bot-welcoming `robots.txt` ensuring the Linux tools appear accurately in AI search queries.
3. **Zero-Cloud Bare-Metal Operating Cost**: Runs directly on bare-metal Linux hardware behind Cloudflare Tunnel (`cloudflared`) on port `8085`. **$0.00/mo** hosting bill.

---

## 2. Technical Architecture & Code Profile

### Backend Stack
- **Framework**: FastAPI (Python 3.12) with async lifespan handlers.
- **Asynchronous Telemetry**: Uses `aiosqlite` to log incoming route requests, user-agent profiles, and one-way salted `SHA-256` client IP hashes (`ip_hash`) without collecting intrusive PII:
  ```python
  async def track(request: Request):
      user_agent = request.headers.get("user-agent", "")
      client_ip = request.client.host if request.client else ""
      ip_hash = hashlib.sha256(client_ip.encode()).hexdigest()[:16] if client_ip else ""
      await record_visit(request.url.path, user_agent, ip_hash)
  ```
- **Documentation Service**: Dynamic markdown ingestion via `docs_service.py`, serving real-time updates directly from tool catalogs.

### Deployment & Daemon Management
- **Process Supervision**: Managed via Linux `systemd` (`avathings-web.service`) with auto-restart on failure.
- **Encrypted Edge Routing**: Cloudflare Tunnel routes public traffic (`avathings.com` and `www.avathings.com`) straight into localhost `127.0.0.1:8085` with zero open firewall ports.

---

## 3. Engineering Metrics & Specifications

| Dimension | Implementation |
| :--- | :--- |
| **Local Source** | `/home/dev/Code/tdg/avathings-web` |
| **Ingress Invariant** | Encrypted `cloudflared` tunnel $\to$ `127.0.0.1:8085` |
| **Database** | Embedded asynchronous SQLite (`avathings.db` via `aiosqlite`) |
| **Analytics Footprint** | Pure local SHA-256 telemetry; zero third-party Google Analytics trackers |
| **Agent Endpoints** | `/llms.txt`, `/llms-full.txt`, semantic JSON-LD |
| **Hosting Overhead** | **$0.00 / month** |
