---
title: "AI-Context-OS / MEMM — глубокий разбор и план переноса в First-Agent"
compiled: "2026-04-26"
source:
  - https://github.com/alexdcd/AI-Context-OS
  - knowledge/research/llm-wiki-community-batch-1.md §3.3
chain_of_custody:
  - "Репозиторий alexdcd/AI-Context-OS клонирован 2026-04-24."
  - "Код прочитан целиком; цитаты — путь:строка. Звёзды/коммиты — на дату фетча."
status: research
claims_requiring_verification:
  - "MEMM claim о token-budget efficiency vs flat RAG — README только, no benchmark."
  - "Personalised PageRank performance on 1k-doc corpus — авторская оценка."
  - "L0/L1/L2 progressive loading — actual disk IO numbers from README, не наш measurement."
related:
  - knowledge/research/llm-wiki-community-batch-1.md
  - knowledge/research/agent-roles.md
  - knowledge/adr/ADR-3-memory-architecture-variant.md
superseded_by: "knowledge/adr/ADR-3-memory-architecture-variant.md"
---

# AI-Context-OS / MEMM — глубокий разбор

> **Status:** superseded by [`adr/ADR-3-memory-architecture-variant.md`](../adr/ADR-3-memory-architecture-variant.md) (archived 2026-05-08; body trimmed 2026-05-11 per PR-M).
>
> Excluded from `knowledge/llms.txt §BY-DEMAND-INDEX` for the OSS-agent routing surface. Deep-dive companion to batch-1's §3.3 (MEMM is the most engineered of all LLM-wiki community projects); cited as input to ADR-3.
>
> **Body trimmed in PR-M to TL;DR + key-patterns abstract; full pre-trim text in git history at commit `cf7db4d`** (`git show cf7db4d:knowledge/research/ai-context-os-memm-deep-dive.md`). Trim rationale: `repo-audit-2026-05-10-revised.md` §4.1.

## 1. TL;DR — why MEMM matters

MEMM (Memory-Enhanced Markdown Manager, by alexdcd / AI-Context-OS) is
the **most over-engineered** of all LLM-wiki community projects we
surveyed. ~115 stars, ~3.2k LoC Python, ~40 internal modules. Two-pass
retrieval (seeds → personalised PageRank), six-signal relevance scoring,
intent-aware scoring weights, conflict detection by technology pairs,
L0/L1/L2 progressive document loading. Production-quality engineering;
research-quality README claims (unverified benchmarks).

**Verdict for First-Agent v0.1:** **adopt patterns, reject runtime**.
The runtime is too heavy (40 modules, complex graph state) for the
deterministic-shell model we target. But three patterns are directly
useful:

1. **Two-pass retrieval** (FTS5 seeds → graph-walk expansion). Deferred
   to ADR-4 v0.2 (FTS5 already in v0.1; expansion later).
2. **Intent-aware scoring** (different scoring weights for «exploration»
   vs «debug» vs «build» queries). Pushed to ADR-8 prep.
3. **L0/L1/L2 progressive loading** (summary → full doc → adjacent
   docs). Already de facto adopted: `llms.txt` MUST READ FIRST = L0,
   `DIGEST.md` rows = L1, ADR bodies = L2.

## Key MEMM patterns at a glance

| # | Pattern | Source | First-Agent fate |
|---|---|---|---|
| 5.1 | Two-pass retrieval (seeds → PPR) | `memm/retrieval/two_pass.py` | Deferred — FTS5 only in v0.1 |
| 5.2 | Six-signal relevance scoring | `memm/scoring/signals.py` | Deferred — overkill for ~100 doc corpus |
| 5.3 | Intent-aware scoring weights | `memm/scoring/intent.py` | Future ADR-8 input |
| 5.4 | Conflict detection by tech pairs | `memm/conflicts/detector.py` | Rejected — no equivalent need in v0.1 |
| 5.5 | L0/L1/L2 progressive loading | `memm/loaders/progressive.py` | **Adopted** as `llms.txt` / DIGEST / ADR-body hierarchy |
| 5.6 | Atomic Markdown docs | `memm/storage/atomic.py` | **Adopted** — Variant A foundation |
| 5.7 | Frontmatter as canonical metadata | `memm/parsers/frontmatter.py` | **Adopted** — `knowledge/README.md` schema |

## Where the decisions live now

- **ADR-3 §Decision:** Variant A (Mechanical Wiki). Patterns 5.5-5.7
  cited as input.
- **ADR-4 §Backend:** SQLite + FTS5. Pattern 5.1 deferred.
- **Companion batch-1:** [`llm-wiki-community-batch-1.md`](./llm-wiki-community-batch-1.md) §3.3.
- **Skill dispatcher (ADR-8 open):** patterns 5.3 cited.

## Full pre-trim text

`git show cf7db4d:knowledge/research/ai-context-os-memm-deep-dive.md` —
801 lines, last full revision 2026-05-08. Contains: §2 full module
catalogue (~40 modules, role per module), §3 retrieval-pipeline deep-
dive with code citations, §4 scoring algebra (six signals + intent
weights), §5 conflict-detection algorithm, §6 progressive-loader
implementation, §7 storage layer, §8 commitment table for FA v0.1.
