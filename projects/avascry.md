---
title: "AvaScry MTG Apex"
type: "system"
tags: [systems-architecture, zero-cloud, bare-metal, low-latency, caching, distributed-systems, python, fastapi, mongodb, celery]
status: "stable"
last_updated: 2026-10-06
repo: "avascry.com"
---

# AvaScry MTG Apex

> **Autonomous Full-Stack Search & Archival Engine — 808k+ Printings, In-Memory Sub-Millisecond Runtime, \$0.00/mo Cloud Footprint.**  
> High-performance discovery engine indexing 808,000+ Magic: The Gathering printings and 340,000+ card art scans. Inverts the traditional discovery pyramid by championing the long tail of forgotten printings, premodern variants, and global releases on bare-metal hardware.

```
                      [ Incoming Edge Traffic ]
                                 │
                                 ▼
                 ┌───────────────────────────────┐
                 │      Cloudflare Edge WAF      │
                 │ - Smart Tiered Cache (Edge CDN)│
                 │ - ASN Datacenter Filtering    │
                 └───────────────┬───────────────┘
                                 │ Encrypted Cloudflare Tunnel (cloudflared)
                                 ▼
                 ┌───────────────────────────────┐
                 │     FastAPI Ingress (:8004)   │
                 │ 🛡️ BotShieldMiddleware (rDNS)  │
                 │ 🔀 NetworkDispatch (Multi-Host)│
                 └───────────────┬───────────────┘
                                 │
         ┌───────────────────────┼───────────────────────┐
         ▼                       ▼                       ▼
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│ PAGE_CACHE (RAM)│     │IMAGE_PREFIX_MAP │     │  Local MongoDB  │
│ - <1ms response │     │- 808k scan hash │     │ - System of     │
│ - Atomic NVMe   │     │- O(1) slug map  │     │   record        │
│   gzip snapshot │     │- Extensionless  │     │ - Oracle rules  │
└─────────────────┘     └─────────────────┘     └─────────────────┘
         ▲                                               ▲
         │                                               │
         └───────────── Distributed Workers ─────────────┘
                       - Celery (Local Mongo Broker)
                       - Vision-Language & Embeddings
                       - Prime-Jitter Precomputations
```

---

## 1. Core Architecture & Zero-Cloud Invariant

- **$0.00/mo Infrastructure Cost**: Entire production stack runs on bare-metal Linux Mint hardware with an active-standby topology and air-gapped NVMe redundancy. Zero AWS, GCP, or managed SaaS database bills.
- **Encrypted Edge Ingress**: Replaces public load balancers with an encrypted `cloudflared` tunnel feeding directly to local FastAPI listeners (`127.0.0.1:8004`).
- **Edge Cache Co-Optimization**: Cloudflare Smart Tiered Cache absorbs static image scans and immutable long-tail card assets across 300+ edge locations before touching local fiber.
- **Host-Header Dispatch Matrix**: Zero-overhead routing multiplexes distinct domains and subdomains from a single unified FastAPI instance based on incoming HTTP `Host` headers.

---

## 2. In-Process Memory Hierarchy ("The RAM Tier")

> *"The database is the system of record; RAM is the runtime reality."*

To serve dynamic card views without Redis overhead, AvaScry employs a custom two-tier in-process memory architecture:

- **`PAGE_CACHE` (HTML In-Memory)**:
  - Scaled dynamically via `psutil` (soft-capped at 75% physical RAM, capacity for millions of cached documents).
  - **<1ms Turnaround**: Serves cached responses with `X-Cache: HIT`, entirely bypassing MongoDB and Jinja2 rendering passes.
  - **Atomic NVMe Durability**: Eliminates cold-start penalties via atomic serialization using highest-protocol Python `pickle` + Gzip (`page_cache.gz`). Uses `.tmp` staging and `os.replace` to prevent snapshot corruption on unexpected terminations.
- **`IMAGE_PREFIX_MAP` (808k Image Hash)**:
  - Background daemon threads pre-warm an in-memory prefix hash table across 808,602 local scan files in ~1.2s on startup.
  - Resolves multi-face cards, art series, language variants, and extensionless slugs to absolute NVMe paths in **$O(1)$ sub-microsecond time**.

---

## 3. Mathematical Systems: Prime + Jitter Doctrine

To eliminate lockstep collisions, harmonic resonance, and cache stampedes across warming routines and workers, binary power-of-two algorithms were replaced with number-theoretic prime dispersion:

- **Prime Ladder Backoff**: Replaced standard binary backoff ($2, 4, 8, 16\dots$) with prime sequences:
  $$\text{Ladder} = [1.123, 2.317, 3.141, 5.303, 7.129, 11.311, 13.147, 17.321, 19.183, 23.327, 29.173, 31.379]$$
  Prevents harmonic lockstep across parallel worker retries.
- **Continuous Stochastic Dispersion (`prime_jitter`)**: Applies prime jitter bounds ($\pm 17\%$, $\pm 13\%$) to shatter retry synchronization:
  $$\text{delay} = \text{base} \times (1.0 + \text{Uniform}(-0.17, +0.17))$$
- **Prime Sampling Batches ($N \ge 31$)**: Sampling batches anchored to prime thresholds ($N = 31, 67, 127, 257, 509, 1021$) to satisfy the Central Limit Theorem ($N \ge 30$) while preventing modulo-power-of-two cache bucket hot-spotting.

---

## 4. Edge Defense: The Scraper Firehose & WAF Engineering

AvaScry experienced a trial by fire immediately upon deployment: **served 3.5M requests directly from a personal laptop in the first 7–10 days**. Primary consumers hammering the endpoint were large AI scraping operations (Meta, Anthropic/Claude, ShapBot, OpenAI). This forced an aggressive, rapid hardening cycle:

- **Cloudflare WAF Custom Rules & Filtering Syntax**: Mastered Cloudflare WAF expression language to construct custom edge filter rules, dropping aggressive automated harvesting runs before requests could saturate local fiber or compute.
- **Real-Time Reverse-DNS Crawler Verification**: Malicious scrapers spoofing crawler user-agents (`Googlebot`, `Bingbot`) are challenged via automated two-way rDNS lookups (reverse lookup on IP, followed by forward lookup on verified domain hostname). Verified IPs are cached in memory; unverified scrapers receive an instant `403 Forbidden`.
- **Datacenter ASN Filtering**: Heuristic rules drop unauthenticated cloud datacenter IP ranges (AWS, DigitalOcean, Hetzner) while maintaining a zero-latency exception path for cryptographically signed webhooks.
- **Automated Edge WAF Sync**: Continuous synchronization of local reputation scores, suspicious subnets, and security policies to Cloudflare Edge firewall rules via Cloudflare API (`sync_cloudflare_waf.py`).

---

## 5. Distributed Worker Fleet (Celery + Mongo Broker)

Multi-stage Celery worker fleet handles compute-heavy pipelines asynchronously without dedicated Redis or RabbitMQ instances:

- **MongoDB as Broker**: Configured local MongoDB collections as both message broker and result backend (`celery_broker`, `celery_results`).
- **Specialized Work Queues**:
  - `gpu-tasks`: High-dimensional vector embeddings and visual feature extraction.
  - `ai-lures`: LLM-assisted lore generation, card hook summaries, and flavor analysis.
  - `builder-tasks`: Graph synergy calculation, card recommendations, and batch HTML pre-rendering.
  - `vl`: Vision-Language (Qwen-VL) pipelines for automated card scan visual verification.
- **Concurrency & Failure Isolation**: Late-acknowledgement (`task_acks_late=True`) with dedicated pools to isolate GPU workloads from low-overhead CPU tasks.

---

## 6. High-Availability Discord Slash Command Architecture

Multi-game Discord engine (`/mtg`, `/dom`, `/swu`, `/necro`, `/mc`, `/pkm`, `/op`, `/lor`) built for sub-second responses:

- **Decoupled Stateless Webhook Engine**: Slash commands route serverlessly via `POST /api/discord/interactions`, cryptographically validated via **Ed25519 signatures** (`X-Signature-Ed25519`). Executes with 100% availability even during full Discord Gateway WebSocket outages.
- **Gateway Presence Daemon**: Independent background process maintains Gateway v10 connectivity, heartbeat ACKs, and session resumptions (`RESUME` OP 6) purely for status broadcasting.
- **3-Second Law Compliance**: Instant `Type 5` (deferred channel response) dispatch in <50ms offloads image download, rendering, and payload synthesis to asynchronous background tasks.
- **Multipart Uploads**: Directly uploads multipart message file payloads (`card.jpg`/`card.png`) to eliminate cross-platform Discord mobile client bugs where embed-wrapped images fail to render.

---

## 7. Metrics & Engineering Spec

| Metric / Feature | Engineered Value | Architectural Rationale |
| :--- | :--- | :--- |
| **Catalog Scale** | **808,000+** Printings / **340k+** Art Scans | Inverting the discovery pyramid for long-tail MTG printings |
| **Initial Blast Test** | **3.5M requests in 7–10 days** | Served from a bare laptop; survived scraping waves (Meta, Claude, OpenAI) |
| **Cloud Hosting Cost** | **$0.00 / month** | Bare-metal Linux + Cloudflare Edge + Local MongoDB |
| **Cached Page Latency** | **< 1 ms** | In-memory `PAGE_CACHE` bypassing DB queries and Jinja templating |
| **Image URI Lookup** | **Sub-microsecond ($O(1)$)** | Pre-warmed `IMAGE_PREFIX_MAP` across 808k files |
| **Persistence Durability** | **Atomic .tmp $\to$ os.replace** | Zero corrupted cache snapshots; fast rehydration on boot |
| **Retry Coordination** | **Prime Ladder + 17% Jitter** | Mathematical elimination of harmonic retry stampedes |
| **Slash Command Engine** | **Ed25519 Webhooks + Deferred Type 5** | Decoupled from Discord WebSocket gateway state |
| **Scraper Defense** | **Real-time 2-Way rDNS Lookups** | Blocks spoofed crawlers and abusive datacenter IP ranges |
