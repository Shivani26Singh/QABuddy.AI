# QABuddy.AI — Runbook

> Multi-source Hybrid RAG for QA Engineers. Ask questions in plain English, get cited answers grounded in your actual QA assets — test frameworks, test cases, JIRA tickets, meeting notes, PRDs, and more.

---

## Table of Contents

- [1. Why This Exists](#1-why-this-exists)
- [2. What Was Built](#2-what-was-built)
- [3. How It Works](#3-how-it-works)
- [4. Local Development](#4-local-development)
- [5. Production Deployment (Oracle Cloud Free Tier)](#5-production-deployment-oracle-cloud-free-tier)
- [6. Vercel (Docs Only)](#6-vercel-docs-only)
- [7. FAQ & Troubleshooting](#7-faq--troubleshooting)

---

## 1. Why This Exists

### The Problem

QA engineers waste hours context-switching between tools to answer simple questions:

- "What caused that checkout 500 error in production?" — check Jenkins logs, search JIRA, skim meeting notes, grep the test framework
- "Which test cases cover the coupon feature?" — open the test case spreadsheet, filter by module
- "Is this bug already reported?" — search JIRA, check Slack, look at the bug triage notes

Each answer requires jumping between 5–10 different tools and repositories. Knowledge lives in silos.

### The Solution

QABuddy.AI ingests **all 10 knowledge sources** into a single searchable knowledge base, then uses a **Hybrid RAG** (Retrieval-Augmented Generation) pipeline to answer questions with **cited sources** — every claim points back to the exact file, ticket, or log line it came from.

### Key Design Decisions

| Decision | Why |
|---|---|
| **BGE-M3 for embeddings** | Single model produces both dense (semantic) + sparse (keyword) vectors — half the latency of running two separate models. Essential for matching both concepts ("login failure") and exact terms ("KAN-1002", "NullPointerException"). |
| **Qdrant as vector store** | Named vectors let us store dense + sparse in one point. Built-in payload filtering for source selection. Low memory usage (Rust). Dual-mode: embedded (dev) or server (prod) with zero code changes. |
| **BGE-Reranker cross-encoder** | Vector search ranks by similarity, not relevance. The reranker scores (query, chunk) pairs directly — catches mismatches that embedding distance misses. |
| **0.22 relevance threshold** | When the best chunk scores below 0.22, the system says "not in my knowledge base" instead of hallucinating. |
| **SSE streaming everywhere** | Both chat responses and ingestion progress stream via Server-Sent Events — no HTTP timeouts on 10-30s LLM generations. |
| **Glossary in system prompt** | Company terms (VWO, RTM, SDET, POM) are injected into the prompt, not embedded. Keeps the vector space clean. |

---

## 2. What Was Built

### The Application

A complete **Hybrid RAG web application** with:

- **10 data source ingestors** — Selenium (Java), Playwright (TypeScript), test cases (CSV), JIRA tickets (JSON), company docs (PDF/MD), Figma designs (MD), meeting notes (MD), Lucid charts (TXT), PRD docs (PDF), Jenkins logs (TXT/LOG)
- **Per-source chunking** — brace-aware code splitting, heading-aware prose chunking, one-row-per-chunk for CSV, speaker-turn windowing for transcripts, failure-block extraction for logs
- **Idempotent incremental ingestion** — SHA-256 manifest tracks file changes; only modified files get re-embedded
- **Hybrid search** — dense ANN (cosine) + sparse BM25 merged via RRF fusion
- **Cross-encoder reranking** — bge-reranker-v2-m3 scores top-12 candidates, keeps top-6
- **LLM generation** — Groq API (openai/gpt-oss-120b) with mode-specific prompts (answer, generate tests, review coverage, root cause analysis)
- **Source-filtered chat** — check/uncheck knowledge sources to scope answers
- **Citation cards** — every [n] reference is clickable, shows the source file, snippet, and relevance score
- **Sidebar detail counts** — shows test counts, ticket counts, and file counts per source on the left panel
- **Architecture documentation** — interactive Mermaid.js diagrams at `/architecture`

### File Structure

```
QABuddy/
├── app/
│   ├── server/
│   │   ├── app.py              # Flask server: routes, SSE, API
│   │   ├── templates/
│   │   │   └── index.html      # Chat UI
│   │   └── static/
│   │       ├── app.js          # SSE client, markdown rendering
│   │       └── style.css       # App styling
│   ├── core/
│   │   ├── chunking.py         # Per-source chunking strategies
│   │   ├── embedder.py         # BGE-M3 embedding model wrapper
│   │   ├── store.py            # Qdrant store (embedded/server)
│   │   ├── reranker.py         # BGE-reranker-v2-m3 cross-encoder
│   │   └── fusion.py           # RRF fusion logic
│   ├── ingestion/
│   │   ├── pipeline.py         # Ingestion orchestration + source config
│   │   ├── loaders.py          # Per-source document loaders
│   │   ├── cli.py              # CLI entry for ingestion
│   │   └── base.py             # Base loader class
│   ├── config.py               # Environment + YAML config
│   ├── llm.py                  # LLM client (Groq OpenAI-compatible)
│   ├── prompts.py              # Mode-specific system prompts + glossary
│   └── retrieval.py            # Full retrieval pipeline (condense → rewrite → search → fuse → rerank → prompt → LLM)
├── data/                        # 10 knowledge source folders
│   ├── 01_selenium_framework/   # Cloned from GitHub
│   ├── 02_playwright_framework/ # Cloned from GitHub
│   ├── 03_test_cases/           # CSV test cases
│   ├── 04_jira_tickets/         # JSON ticket exports
│   ├── 05_company_docs/         # MD/PDF guides
│   ├── 06_figma_designs/        # Design descriptions
│   ├── 07_meeting_notes/        # Meeting transcripts
│   ├── 08_lucid_charts/         # Architecture flow exports
│   ├── 09_prd_srs_brd_frd/      # Product requirement documents
│   └── 10_jenkins_logs/         # Build console logs
├── docs/
│   ├── architecture.html        # Full interactive architecture page
│   ├── diagram-architecture.jpg # System architecture diagram
│   ├── diagram-data-flow.jpg    # End-to-end data flow diagram
│   ├── deploy-droplet.md        # VPS deployment guide
│   └── QABuddy-runbook.md       # This file
├── scripts/
│   └── fetch_repos.sh           # Clone/update framework repos
├── tests/                       # Unit tests
├── config.yaml                  # Application config
├── glossary.yaml                # Company term definitions
├── requirements.txt             # Python dependencies
├── Dockerfile                   # Production container
├── docker-compose.yml           # Qdrant + App + Caddy
└── Caddyfile                    # Reverse proxy config
```

---

## 3. How It Works

### Architecture Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                    10 Knowledge Sources                         │
│  (Selenium, Playwright, Test Cases, JIRA, Docs, Figma,         │
│   Meeting Notes, Lucid Charts, PRDs, Jenkins Logs)             │
└─────────────────────┬───────────────────────────────────────────┘
                      │ parse → chunk
                      ▼
┌─────────────────────────────────────────────────────────────────┐
│                  Ingestion Pipeline                             │
│  ┌──────────┐   ┌──────────┐   ┌──────────────────────────┐   │
│  │  Loader  │ → │ Manifest │ → │ BGE-M3 Embedder          │   │
│  │ (per src)│   │ (SHA-256)│   │ (dense 1024d + sparse)   │   │
│  └──────────┘   └──────────┘   └─────────────┬────────────┘   │
└───────────────────────────────────────────────┼─────────────────┘
                                                │ upsert
                                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                    Qdrant Vector Store                          │
│              qabuddy_kb (named vectors)                         │
└─────────────────────────────────────────────────────────────────┘
                                                ▲
                                                │ dense + sparse search
┌───────────────────────────────────────────────┼─────────────────┐
│               Retrieval Pipeline               │                │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────┐ │
│  │Condense  │→│ Rewrite  │→│ Hybrid   │→│ RRF      │→│Rerank│ │
│  │(pronouns)│ │(3× query)│ │(ANN+BM25)│ │(k=60)    │ │(12→6)│ │
│  └──────────┘ └──────────┘ └──────────┘ └──────────┘ └──┬───┘ │
└─────────────────────────────────────────────────────────┼───────┘
                                                          │ top-6 chunks
                                                          ▼
┌─────────────────────────────────────────────────────────────────┐
│  Groq LLM (openai/gpt-oss-120b)                                 │
│  ┌─ System prompt: mode + glossary ──────────────────────┐     │
│  │  Answer using ONLY the 6 chunks above. Cite [n].      │     │
│  └───────────────────────────────────────────────────────┘     │
│         │ stream tokens via SSE                                │
└─────────┼───────────────────────────────────────────────────────┘
          ▼
┌─────────────────────────────────────────────────────────────────┐
│  Flask Server → Chat UI                                         │
│  Rendered answer with clickable [n] citation cards              │
└─────────────────────────────────────────────────────────────────┘
```

### Retrieval Pipeline (Step by Step)

1. **Condense** — LLM rewrites follow-up questions into standalone queries ("what about the fix?" → "what is the fix for KAN-1002?")
2. **Rewrite** — LLM generates 3 alternate phrasings for recall coverage
3. **Hybrid Search** — Dense ANN (semantic) + sparse BM25 (keyword) searches Qdrant in parallel, 20 candidates each
4. **RRF Fusion** — Reciprocal Rank Fusion (k=60) merges dense + sparse rankings into top-12
5. **Rerank** — bge-reranker-v2-m3 cross-encoder scores the 12 candidates, keeps top-6
6. **Threshold Gate** — if max score < 0.22, responds "not in knowledge base" (prevents hallucination)
7. **Prompt Build** — 6 chunks + glossary + mode-specific system prompt + last 3 chat turns
8. **Generate** — Groq streams tokens via SSE, rendered as markdown with citation cards

---

## 4. Local Development

### Prerequisites

- Python 3.12+
- Git

### Setup

```bash
# Clone
git clone https://github.com/Shivani26Singh/QABuddy.AI.git
cd QABuddy.AI/QABuddy

# Virtual environment
python -m venv .venv
.venv\Scripts\activate      # Windows
source .venv/bin/activate   # Linux/Mac

# Install dependencies
pip install -r requirements.txt

# Environment config
copy .env.example .env      # Windows
cp .env.example .env        # Linux/Mac
# Edit .env — set your LLM_API_KEY (Groq key)

# Fetch framework repos
bash scripts/fetch_repos.sh

# Ingest data sources
python -m app.ingestion.cli ingest --all

# Run the server
python -m app.server.app
# → http://127.0.0.1:5080
```

### Environment Variables (`.env`)

| Variable | Required | Default | Description |
|---|---|---|---|
| `LLM_API_KEY` | **Yes** | — | Groq API key (or any OpenAI-compatible API) |
| `LLM_BASE_URL` | No | `https://api.groq.com/openai/v1` | API base URL |
| `LLM_MODEL` | No | `openai/gpt-oss-120b` | Model name |
| `EMBED_MODEL` | No | `BAAI/bge-m3` | Embedding model |
| `QDRANT_URL` | No | — | Qdrant server URL (blank = embedded mode) |
| `QDRANT_PATH` | No | `./qdrant_data` | Path for embedded Qdrant storage |

### Commands

```bash
# Ingest a specific source
python -m app.ingestion.cli ingest --source 01

# Ingest all sources (incremental — skips unchanged files)
python -m app.ingestion.cli ingest --all

# Force re-ingest all sources
python -m app.ingestion.cli ingest --all --force

# Run tests
python -m pytest tests/

# Generate sample data (if test data is missing)
python scripts/generate_all_data.py
python scripts/generate_testcases.py
```

---

## 5. Production Deployment (Oracle Cloud Free Tier)

Oracle Cloud's **Always Free Tier** gives you enough resources to run this app indefinitely at no cost.

### Step 1: Sign Up

1. Go to [cloud.oracle.com](https://cloud.oracle.com)
2. Click **Start for free**
3. Enter details (requires a credit card for identity verification — no charges for free tier)
4. After signup, you get access to the OCI Console

### Step 2: Create a VM Instance

1. In OCI Console, go to **Compute → Instances**
2. Click **Create instance**
3. Name: `qabuddy`
4. Placement: keep defaults
5. **Image**: Canonical Ubuntu 24.04 (or 22.04)
6. **Shape**: Change shape → Select **Specialty and legacy** → **VM.Standard.A1.Flex**
   - Select **Always Free eligible** checkbox
   - Set OCPUs to **4**, Memory to **24 GB**
7. **Networking**: Create a new VCN or use default
   - Add an **ingress rule** for ports **80** and **443** from `0.0.0.0/0`
8. **SSH keys**: Download the private key (you'll need it to connect)
9. Click **Create**

### Step 3: Connect & Install Docker

```bash
# From your terminal (replace IP and key path)
ssh -i ~/.ssh/oracle_key ubuntu@YOUR_VM_IP

# Install Docker
sudo apt update && sudo apt install -y docker.io docker-compose-v2
sudo systemctl enable --now docker
sudo usermod -aG docker ubuntu
# Log out and back in for group change to take effect
exit
ssh -i ~/.ssh/oracle_key ubuntu@YOUR_VM_IP

# Verify
docker --version && docker compose version
```

### Step 4: Clone & Deploy

```bash
# Clone the repo
git clone https://github.com/Shivani26Singh/QABuddy.AI.git
cd QABuddy.AI/QABuddy

# Create .env with your API key
cat > .env << 'EOF'
LLM_API_KEY=your_groq_api_key_here
LLM_BASE_URL=https://api.groq.com/openai/v1
LLM_MODEL=openai/gpt-oss-120b
QDRANT_URL=http://qdrant:6333
EOF

# Fetch framework repos
bash scripts/fetch_repos.sh

# Ingest data (takes a few minutes on first run)
docker compose run --rm app python -m app.ingestion.cli ingest --all

# Start the full stack
docker compose up -d
```

### Step 5: Access

```
http://YOUR_VM_IP
```

For a proper domain with HTTPS, add a domain in OCI DNS or use a free service like `nip.io`:

```
http://YOUR_VM_IP.nip.io
```

### Optional: Set Up HTTPS with Caddy

The `Caddyfile` and `docker-compose.yml` already include Caddy. To use it:

1. Point a domain to your VM's IP
2. Edit `Caddyfile` to use your domain
3. Update `docker-compose.yml` to expose ports 80/443
4. Run `docker compose up -d`

---

## 6. Vercel (Docs Only)

The architecture documentation is deployed on Vercel for public access:

- **URL:** `https://docs-xi-ivory.vercel.app/architecture`
- **What's deployed:** `QABuddy/docs/` — static HTML with Mermaid.js diagrams

### Why the full app can't run on Vercel

Vercel runs **serverless functions** — they're stateless, short-lived (10s timeout), memory-limited (1GB), and have no persistent disk. The QABuddy app requires:

| Requirement | Vercel Limit | Blocking? |
|---|---|---|
| BGE-M3 model (2.2GB RAM) | 1GB max | ❌ |
| Qdrant database (persistent process) | No background processes | ❌ |
| Ingestion (minutes-long) | 10s timeout | ❌ |
| File system (10 data source folders) | Ephemeral /tmp only | ❌ |
| SSE streaming (10-30s) | 10s timeout | ❌ |

**Alternatives that work:** Oracle Cloud Free Tier (see above), any VPS with Docker.

---

## 7. FAQ & Troubleshooting

### "Why does the reranker return 0 for everything?"

The first time you ask a question after ingestion, the reranker model loads into memory — this takes 30-60s on CPU. Subsequent queries will be fast. Run a dummy query (`python -m app.ingestion.cli warmup`) or just ask a question and wait.

### "How do I add a new knowledge source?"

1. Create a new folder `data/11_your_source/`
2. Add a loader in `app/ingestion/loaders.py`
3. Register it in `app/ingestion/pipeline.py` SOURCES dict
4. Add a chunking strategy in `app/core/chunking.py`
5. Run `python -m app.ingestion.cli ingest --source 11`

### "The model downloads every time I restart"

Model cache is at `~/.cache/huggingface/`. In Docker, mount a volume to persist it. In the Docker Compose setup, it's already configured via the `hf_cache` volume.

### "How do I reset everything?"

```bash
# Delete the vector store
rm -rf qdrant_data/

# Delete the manifest (forces re-ingest)
rm data/.ingest_manifest.json

# Re-ingest everything
python -m app.ingestion.cli ingest --all --force
```

### "Ingestion is slow"

First-time ingestion takes 5-15 minutes depending on your CPU and the size of the framework repos. Subsequent runs are fast — only changed files get re-embedded. You can ingest individual sources to parallelize:

```bash
python -m app.ingestion.cli ingest --source 01 &
python -m app.ingestion.cli ingest --source 02 &
wait
```

### "Port 5080 is already in use"

```bash
# Find the process
netstat -ano | findstr :5080
# Kill it
taskkill /F /PID <PID>
```

---

*Last updated: 2026-07-19*
