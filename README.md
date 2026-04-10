# 🦞 Super GitHub — AI-Native GitHub Assistant

> Powered by the same Embedder + Qdrant + LLM architecture as elite memory systems.
> Index repos, search semantically, monitor proactively — all with natural language.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Platform: OpenClaw](https://img.shields.io/badge/Platform-OpenClaw-ff6b35.svg)](https://github.com/openclaw/openclaw)
[![Stars](https://img.shields.io/github/stars/Ultracold-molecule/super-github)]()

---

## ✨ What It Does

Super GitHub brings **AI-native capabilities** to GitHub operations. Instead of manually searching issues, filtering PRs, and checking CI status — just ask in natural language.

```
You: "Find all open issues about memory leaks in the last 30 days"
AI : → Semantic search across all indexed issues, returns ranked results
```

It follows the same **three-layer architecture** proven in production memory systems:

```
User Query → [LLM: understand intent] → [Embedder: vectorize] → [Qdrant: semantic search] → [gh CLI: act]
```

---

## 🏗 Architecture

### Three-Layer System (same as production memory pipelines)

| Layer | Component | Role |
|-------|-----------|------|
| **Embedder** | Ollama `nomic-embed-text` | Converts text → 768-dim vectors |
| **Vector Store** | Qdrant (local) | Stores & searches vectors by similarity |
| **Action Layer** | `gh` CLI | Executes GitHub operations |

### Data Flow

```
Index:  gh api → JSON → text chunk → embed → Qdrant (768-dim cosine)
Search: query → embed → Qdrant similarity search → ranked results
Monitor: gh api → diff detection → keyword match → Feishu alert
```

---

## 📦 What's Included

### Scripts

| Script | Purpose |
|--------|---------|
| `github_indexer.py` | Index repos (issues, PRs, metadata) into Qdrant |
| `github_search.py` | Natural language semantic search across indexed data |
| `github_monitor.py` | Proactive monitoring with keyword alerts to Feishu |

### Features

- **🔍 Semantic Search** — Search issues/PRs/code with natural language, not just keywords
- **📊 Vector Indexing** — All GitHub data stored as semantic vectors in local Qdrant
- **🔔 Proactive Monitoring** — Track repo activity, alert on keywords (bug, broken, urgent)
- **🤖 LLM-Powered** — Understands intent, not just pattern matching
- **📱 Feishu Alerts** — Push notifications when monitored keywords are detected
- **💾 Local-First** — All vector data stays on your machine (Qdrant + Ollama, no external API)
- **⚡ Fast Setup** — Zero configuration, works out of the box

---

## 🚀 Quick Start

### Prerequisites

- `gh` CLI → [GitHub CLI installation](https://cli.github.com/)
- Ollama with `nomic-embed-text:latest` model
- Qdrant running locally (`localhost:6333`)

### 1. Authenticate `gh`

```bash
gh auth login
```

### 2. Install the Skill

```bash
openclaw skills install super-github
# or manually: copy to your skills directory
```

### 3. Initialize Vector Database

```bash
python scripts/github_indexer.py init
```

### 4. Index a Repository

```bash
# Index everything (issues + PRs + repo metadata)
python scripts/github_indexer.py add owner/repo --all

# Index issues only
python scripts/github_indexer.py add openclaw/openclaw --issues --limit 100

# Index PRs only
python scripts/github_indexer.py add openclaw/openclaw --prs
```

### 5. Search

```bash
# Natural language search
python scripts/github_search.py "memory search failing in agent"

# Filter by repo
python scripts/github_search.py "CI broken" --repo openclaw/openclaw

# Filter by type
python scripts/github_search.py "security vulnerability" --type issue

# Show recent CI runs
python scripts/github_search.py "" --repo openclaw/openclaw --ci
```

### 6. Monitor (proactive alerts)

```bash
# Watch a repo — alerts on new issues, PRs, CI failures with keywords
python scripts/github_monitor.py watch openclaw/openclaw \
  --events issues,prs,ci \
  --keywords bug,broken,urgent,critical,security

# Check all watched repos now
python scripts/github_monitor.py check

# View watched repos
python scripts/github_monitor.py status
```

---

## 🧠 Memory System Analogy

If you've used Mem0 or a vector memory system, this works the same way:

| Memory System | GitHub Super Skill |
|---------------|-------------------|
| Text memories | Issues, PRs, code |
| Ollama embedder | `nomic-embed-text` (same!) |
| Qdrant vector store | Qdrant (same!) |
| `mem0 add` | `github_indexer.py add` |
| `mem0 search` | `github_search.py` |

The difference: instead of storing conversation memories, it stores your GitHub project's entire activity history as semantic vectors.

---

## 📈 Use Cases

### For Open Source Maintainers
- Track issues about specific features without keyword search
- Monitor PR activity across multiple repositories
- Get alerts when "bug" or "critical" appears in new issues

### For Developers
- Find related past issues when debugging
- Search code context across repositories
- Monitor CI failures proactively

### For DevRel / Community Managers
- Track feature requests semantically
- Monitor community activity across repos
- Get notified about security-related reports

---

## 🔧 Configuration

### Ollama Setup

```bash
# Install Ollama → https://ollama.com/
ollama pull nomic-embed-text:latest
```

### Qdrant Setup

```bash
# Download Qdrant → https://qdrant.tech/
# Start locally (data at ./qdrant-data)
qdrant --storage-path ./qdrant-data

# Or with Docker
docker run -p 6333:6333 -p 6334:6334 \
    -v $(pwd)/qdrant-data:/qdrant/storage \
    qdrant/qdrant
```

### gh CLI Authentication

```bash
gh auth login
# or with token:
echo $GITHUB_TOKEN | gh auth login --with-token
```

---

## 📋 Command Reference

### github_indexer.py

```bash
python github_indexer.py init                              # Create Qdrant collection
python github_indexer.py add owner/repo --all               # Index everything
python github_indexer.py add owner/repo --issues            # Issues only
python github_indexer.py add owner/repo --prs               # PRs only
python github_indexer.py add owner/repo --repo              # Repo metadata only
python github_indexer.py add owner/repo --issues --limit 50 # Limit to 50
python github_indexer.py status                              # Show indexed data
python github_indexer.py rm owner/repo                      # Remove repo from index
```

### github_search.py

```bash
python github_search.py "query"                              # Search everything
python github_search.py "query" --repo owner/repo           # Search in specific repo
python github_search.py "query" --type issue                # Search issues only
python github_search.py "query" --type PR                   # Search PRs only
python github_search.py "query" --limit 20                  # Return 20 results
python github_search.py "query" --repo owner/repo --ci      # Show CI runs too
```

### github_monitor.py

```bash
python github_monitor.py watch owner/repo                   # Start watching
python github_monitor.py watch owner/repo --events issues,ci
python github_monitor.py status                              # Show all watches
python github_monitor.py check                               # Run checks now
python github_monitor.py unwatch owner/repo                 # Stop watching
```

---

## 🏆 Why This Works (Technical Deep-Dive)

### Vector Search vs Keyword Search

Traditional GitHub search is **keyword-based** — it matches exact strings. Vector search is **semantic** — it understands meaning.

| Query | Keyword Search | Vector Search |
|-------|---------------|---------------|
| "memory problems" | matches "memory" only | matches "memory leak", "OOM error", "out of memory" |
| "CI broken" | matches "CI" + "broken" | matches "build failed", "tests failing", "pipeline error" |

### Why Ollama Embedder?

Using the **same `nomic-embed-text`** model that powers your local memory system ensures:
- Consistent vector space across all your data (memories + GitHub)
- Zero API cost — fully local
- Privacy-first — no data leaves your machine

### Qdrant Collection Design

```
Collection: github_repos (768-dim, cosine distance)

Payload indexes:
  - repo (keyword)     → filter by specific repo
  - type (keyword)     → filter by issue/PR/repo/code
  - number (int)       → deduplication
  - state (keyword)    → open/closed filtering
  - labels (array)     → label-based filtering
```

---

## 🤝 Contributing

Contributions welcome! Areas to improve:

- [ ] Code search via `gh search code`
- [ ] PR review monitoring
- [ ] Release tracking
- [ ] Multi-user support
- [ ] GitHub Actions workflow definitions

---

## 📄 License

MIT — use freely, modify, redistribute.

---

## Related

- [OpenClaw](https://github.com/openclaw/openclaw) — The agent platform this skill runs on
- [Qdrant](https://qdrant.tech/) — Vector database powering semantic search
- [Ollama](https://ollama.com/) — Local LLM & embedder runner
- [Mem0](https://github.com/mem0ai/mem0) — Memory system that inspired this architecture
