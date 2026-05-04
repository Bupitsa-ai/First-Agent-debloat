---
title: "SocratiCode vs First-Agent: code-intelligence lessons"
source:
  - "https://github.com/giancarloerra/SocratiCode"
  - "/home/ubuntu/research_sources/SocratiCode/README.md"
  - "/home/ubuntu/research_sources/SocratiCode/DEVELOPER.md"
  - "/home/ubuntu/research_sources/SocratiCode/src/index.ts"
  - "/home/ubuntu/research_sources/SocratiCode/src/services/indexer.ts"
  - "/home/ubuntu/research_sources/SocratiCode/src/services/qdrant.ts"
  - "/home/ubuntu/research_sources/SocratiCode/src/services/graph-impact.ts"
  - "/home/ubuntu/research_sources/SocratiCode/src/services/lock.ts"
  - "/home/ubuntu/research_sources/SocratiCode/src/services/context-artifacts.ts"
compiled: "2026-05-04"
chain_of_custody: >
  SocratiCode facts are taken from its README, DEVELOPER.md, package.json,
  AGENTS.md/CLAUDE.md, and targeted source files in src/services and src/tools
  from a local clone at /home/ubuntu/research_sources/SocratiCode. Mapping to
  First-Agent is inferred from current ADR-1..6, project-overview.md,
  knowledge/README.md, AGENTS.md, and the research-briefing template on
  main as of 2026-05-04. This note is research input only, not an accepted ADR.
goal_lens: >
  Identify what First-Agent can use, benefit from, or learn from SocratiCode
  without violating v0.1 scope or license constraints.
tier: stable
links:
  - "../project-overview.md"
  - "../adr/ADR-1-v01-use-case-scope.md"
  - "../adr/ADR-2-llm-tiering.md"
  - "../adr/ADR-3-memory-architecture-variant.md"
  - "../adr/ADR-4-storage-backend.md"
  - "../adr/ADR-5-chunker-tool.md"
  - "../adr/ADR-6-tool-sandbox-allow-list.md"
mentions:
  - "SocratiCode"
  - "giancarloerra/SocratiCode"
  - "MCP"
  - "Qdrant"
  - "Ollama"
  - "BM25"
  - "RRF"
  - "ast-grep"
  - "Context Artifacts"
  - "AGPL-3.0"
confidence: inferred
claims_requiring_verification:
  - >
    SocratiCode's benchmark claims (61.5% less data, 84% fewer tool calls,
    37.2x speedup on VS Code) are copied from README.md and were not
    independently reproduced.
  - >
    Static symbol-level impact analysis is treated as useful design evidence,
    not as a correctness guarantee; SocratiCode itself documents limits around
    dynamic dispatch, reflection, DI/framework magic, and unresolved edges.
  - >
    License analysis is conservative and non-legal: do not copy AGPL-licensed
    implementation code into First-Agent without separate legal review.
superseded_by: none
---

> **Status:** active. Note produced via
> [`knowledge/prompts/research-briefing.md`](../prompts/research-briefing.md).
> §0 is the Decision Briefing; later sections hold the source-derived analysis.

## 0. Decision Briefing

### R-1 — Import the "context artifacts" idea as a manifest, not as vectors

- **What:** Взять у SocratiCode не Qdrant-реализацию, а продуктовый паттерн:
  явный manifest для non-code knowledge, где у каждого артефакта есть `name`,
  `path`, `description`, и главное — описание "когда это читать". Для
  First-Agent это может быть лёгкий слой поверх `knowledge/llms.txt` /
  `notes/inbox/`, без embeddings.
- **Project-axis fit (stable across notes):**
  - (A) reduces session-start noise: YES (~300-700 tokens per future session;
    agent reads a small routing manifest instead of scanning knowledge tree)
  - (B) helps LLM find context when needed: YES (description field becomes a
    pointer-shape: "check this before doing X")
- **Goal-lens fit (per session, dynamic):**
  - (C) advances chosen goal_lens "Identify what First-Agent can use, benefit
    from, or learn from SocratiCode without violating v0.1 scope or license
    constraints.": YES (copies the idea, not AGPL code or vector infra)
- **Cost:** cheap (<1h)
- **Verdict:** TAKE
- **If UNCERTAIN-ASK:** n/a (TAKE)
- **Alternative-if-rejected:** Keep using only hand-written `llms.txt` rows and
  accept weaker "when to consult this" routing.
- **Concrete first step (if TAKE):** Add a short `context-artifact` subsection
  to `knowledge/llms.txt` or a new `knowledge/context-artifacts.yaml` listing
  ADRs, research notes, `notes/inbox/`, schemas/specs, and their use triggers.

### R-2 — Use RRF-style fusion inside the existing SQLite-only retrieval ladder

- **What:** Do not add Qdrant or embeddings in v0.1. Instead, borrow
  SocratiCode's ranking shape: run multiple cheap retrievers
  (filename/title/tag grep, exact symbol/path hints, SQLite FTS5 BM25), then
  combine ranked lists with simple Reciprocal Rank Fusion.
- **Project-axis fit (stable across notes):**
  - (A) reduces session-start noise: YES (~500-1000 tokens saved on broad
    retrieval tasks; fewer irrelevant chunks enter context)
  - (B) helps LLM find context when needed: YES (stable top-k score explains
    why a chunk was selected)
- **Goal-lens fit (per session, dynamic):**
  - (C) advances chosen goal_lens "Identify what First-Agent can use, benefit
    from, or learn from SocratiCode without violating v0.1 scope or license
    constraints.": YES (adapts a search idea within ADR-4 constraints)
- **Cost:** medium (1-4h)
- **Verdict:** TAKE
- **If UNCERTAIN-ASK:** n/a (TAKE)
- **Alternative-if-rejected:** Use raw FTS5 BM25 only; simpler, but worse when
  exact filenames/tags and semantic-ish prose queries disagree.
- **Concrete first step (if TAKE):** Implement a tiny
  `src/fa/search/rank.py::rrf_merge()` over lists of `(chunk_id, rank, source)`,
  with unit tests showing stable tie-breaking.

### R-3 — Copy the operational contract: background work must be stoppable, statused, and resumable

- **What:** SocratiCode treats indexing as a long-running operation with
  `index`, `status`, `stop`, hash checkpoints, and auto-resume. First-Agent's
  `fa ingest` / `fa reindex` should follow the same contract even while the
  backend is only SQLite FTS5.
- **Project-axis fit (stable across notes):**
  - (A) reduces session-start noise: YES (~300-600 tokens saved when agents
    restart after failed ingest; state is queryable instead of re-explained)
  - (B) helps LLM find context when needed: YES (`fa status` is a canonical
    source for "is the index fresh?")
- **Goal-lens fit (per session, dynamic):**
  - (C) advances chosen goal_lens "Identify what First-Agent can use, benefit
    from, or learn from SocratiCode without violating v0.1 scope or license
    constraints.": YES (a design lesson that strengthens v0.1 reliability)
- **Cost:** medium (1-4h)
- **Verdict:** TAKE
- **If UNCERTAIN-ASK:** n/a (TAKE)
- **Alternative-if-rejected:** Treat ingest as a blocking one-shot command and
  rebuild from scratch after interruption.
- **Concrete first step (if TAKE):** Add `meta(indexing_status,
  last_checkpoint_batch, chunker_version, last_error)` to the ADR-4 SQLite
  schema before implementing `fa reindex`.

### R-4 — Keep symbol-level impact/flow as a v0.2 design target, but reserve IDs now

- **What:** SocratiCode's file graph plus symbol graph (`impact`, `flow`,
  `symbol`, `symbols`) is the strongest feature for coding agents, but it is
  too heavy for First-Agent v0.1. The usable lesson now is to make chunk
  anchors and ctags-derived symbol IDs stable enough that a future graph can be
  attached without re-indexing the whole wiki canon.
- **Project-axis fit (stable across notes):**
  - (A) reduces session-start noise: PARTIAL (little immediate saving; prevents
    future migration noise)
  - (B) helps LLM find context when needed: YES (stable IDs let future graph
    edges point to exact chunks/symbols)
- **Goal-lens fit (per session, dynamic):**
  - (C) advances chosen goal_lens "Identify what First-Agent can use, benefit
    from, or learn from SocratiCode without violating v0.1 scope or license
    constraints.": YES (turns a big feature into a safe v0.1 compatibility
    constraint)
- **Cost:** cheap (<1h)
- **Verdict:** TAKE
- **If UNCERTAIN-ASK:** n/a (TAKE)
- **Alternative-if-rejected:** Let v0.2 graph invent IDs later and pay a
  migration/backfill cost.
- **Concrete first step (if TAKE):** In the chunker implementation, preserve
  `(path, anchor, line_start, line_end, ctags_kind, ctags_name)` even if only
  `(path, anchor, body)` is queried in v0.1.

### R-5 — Defer managed Docker/Qdrant/Ollama infrastructure

- **What:** SocratiCode's zero-config Docker story is polished, but it solves
  a different stage: vector search, cloud/local embedding providers, and
  multi-million-LOC indexes. First-Agent v0.1 explicitly chose SQLite FTS5 and
  no vector layer.
- **Project-axis fit (stable across notes):**
  - (A) reduces session-start noise: NO for v0.1 (adds Docker/provider setup to
    every new workstation)
  - (B) helps LLM find context when needed: PARTIAL (vectors help later, but
    not before the corpus justifies them)
- **Goal-lens fit (per session, dynamic):**
  - (C) advances chosen goal_lens "Identify what First-Agent can use, benefit
    from, or learn from SocratiCode without violating v0.1 scope or license
    constraints.": PARTIAL (good future evidence; bad immediate fit)
- **Cost:** expensive (>4h)
- **Verdict:** DEFER
- **If UNCERTAIN-ASK:** n/a (DEFER)
- **Alternative-if-rejected:** If the user wants immediate semantic search,
  prototype `sqlite-vec` in the same SQLite file before considering Qdrant.
- **Concrete first step (if TAKE):** n/a (DEFER)

### R-6 — Add a "search before reading" tool-use rule to First-Agent prompts

- **What:** SocratiCode's AGENTS/CLAUDE instructions are valuable even without
  its tooling: broad conceptual query first, exact grep only for exact strings,
  graph/impact before refactor, context artifacts before schema/API work.
  First-Agent should encode the analogous retrieval ladder in prompts and
  future tool docs.
- **Project-axis fit (stable across notes):**
  - (A) reduces session-start noise: YES (~400-900 tokens saved by avoiding
    speculative file reads)
  - (B) helps LLM find context when needed: YES (future agents learn which
    tool to use from the prompt itself)
- **Goal-lens fit (per session, dynamic):**
  - (C) advances chosen goal_lens "Identify what First-Agent can use, benefit
    from, or learn from SocratiCode without violating v0.1 scope or license
    constraints.": YES (process-level learning; no code copying)
- **Cost:** cheap (<1h)
- **Verdict:** TAKE
- **If UNCERTAIN-ASK:** n/a (TAKE)
- **Alternative-if-rejected:** Leave retrieval behaviour implicit in each
  agent's habits, which recreates the "read too much" failure mode.
- **Concrete first step (if TAKE):** Amend `knowledge/prompts/architect-fa-compact.md`
  or the future Coder prompt with a 5-row retrieval-routing table.

### R-7 — Treat SocratiCode as a license-firewalled reference

- **What:** SocratiCode is AGPL-3.0/commercial dual-licensed. First-Agent is
  proprietary in `pyproject.toml`, so direct code copying is the wrong move;
  use only observed behaviours, public API shapes, and independently written
  implementations.
- **Project-axis fit (stable across notes):**
  - (A) reduces session-start noise: YES (future agents get an explicit
    boundary instead of re-litigating whether code can be copied)
  - (B) helps LLM find context when needed: YES (the note is a reusable
    "allowed use" policy pointer)
- **Goal-lens fit (per session, dynamic):**
  - (C) advances chosen goal_lens "Identify what First-Agent can use, benefit
    from, or learn from SocratiCode without violating v0.1 scope or license
    constraints.": YES (directly enforces the license constraint)
- **Cost:** cheap (<1h)
- **Verdict:** TAKE
- **If UNCERTAIN-ASK:** n/a (TAKE)
- **Alternative-if-rejected:** Ask for legal review before any implementation
  that closely mirrors SocratiCode internals.
- **Concrete first step (if TAKE):** Keep this note's "Do / Do not copy" table
  in §5 and cite it in any PR that borrows a SocratiCode pattern.

### Summary

| R-N | Verdict | Project-fit (A / B) | Goal-fit (C) | Cost | Alternative-if-rejected | User decision needed? |
|-----|---------|---------------------|--------------|------|--------------------------|------------------------|
| R-1 | TAKE | YES / YES | YES (manifest) | cheap | Keep only `llms.txt` prose rows | No |
| R-2 | TAKE | YES / YES | YES (SQLite RRF) | medium | Raw FTS5 BM25 only | No |
| R-3 | TAKE | YES / YES | YES (resumable ops) | medium | Blocking one-shot ingest | No |
| R-4 | TAKE | PARTIAL / YES | YES (future graph IDs) | cheap | v0.2 backfill/migration | No |
| R-5 | DEFER | NO / PARTIAL | PARTIAL (future vectors) | expensive | Try `sqlite-vec` first | No |
| R-6 | TAKE | YES / YES | YES (tool discipline) | cheap | Retrieval behaviour stays implicit | No |
| R-7 | TAKE | YES / YES | YES (license firewall) | cheap | Legal review before close mirroring | No |

## 1. TL;DR

- SocratiCode is not a "drop-in library" for First-Agent. It is a TypeScript MCP
  code-intelligence engine with Qdrant, embeddings, ast-grep, graph analysis,
  Docker-managed infrastructure, and AGPL-3.0 licensing.
- The best immediate lesson is product/interface design, not implementation:
  context-artifact manifests, tool-status contracts, search-before-read
  instructions, and RRF-style result fusion.
- For v0.1, do **not** import Qdrant/Ollama/vector infra. ADR-3 and ADR-4
  explicitly choose Mechanical Wiki + SQLite FTS5, with vectors deferred.
- For v0.1, do reserve stable IDs and metadata that make v0.2 graph/impact
  analysis possible later: file path, anchor, symbol name/kind, line range,
  content hash, chunker version.
- SocratiCode's strongest coding-agent feature is symbol-level impact/flow:
  "what breaks if I change X?" and "what does this entry point do?". First-Agent
  should treat this as a v0.2 target, not as v0.1 scope creep.
- SocratiCode's benchmark claims are useful motivation, not proof for
  First-Agent. Reproduce any numbers locally before citing them as FA metrics.
- Because SocratiCode is AGPL-3.0/commercial dual-licensed and First-Agent is
  currently proprietary, copy ideas and API shapes only; do not copy source code.

## 2. Scope, метод

Goal-lens (verbatim):

> Identify what First-Agent can use, benefit from, or learn from SocratiCode
> without violating v0.1 scope or license constraints.

Method:

1. Read SocratiCode primary repo docs: `README.md`, `DEVELOPER.md`,
   `AGENTS.md`, `CLAUDE.md`, `package.json`.
2. Read targeted implementation files for the claims most relevant to
   First-Agent: MCP tool registration, indexing, Qdrant search, context
   artifacts, file locks, startup/resume, watcher, and graph impact.
3. Cross-map each SocratiCode pattern to First-Agent ADR-1..6:
   v0.1 scope, static LLM routing, Mechanical Wiki, SQLite FTS5, chunker,
   and sandbox allow-list.
4. Separate three buckets:
   - **TAKE now:** pattern fits v0.1 with no heavy infra.
   - **DEFER:** valuable, but belongs to v0.2+.
   - **SKIP / firewall:** implementation or licensing does not fit.

Deliberately excluded:

- No runtime benchmark reproduction.
- No legal opinion beyond conservative license hygiene.
- No attempt to run SocratiCode's MCP server, Docker stack, Qdrant, or Ollama.
- No implementation changes to First-Agent in this PR beyond this research note
  and `knowledge/llms.txt`.

## 3. Core facts known before reasoning

### 3.1 SocratiCode facts

| Fact | Evidence |
|------|----------|
| Project type | `package.json` names `socraticode`, version `1.8.4`, Node.js `>=18`, main `dist/index.js`, bin `socraticode`. |
| License | README says dual license: AGPL-3.0 open-source, commercial alternative. Source files carry `SPDX-License-Identifier: AGPL-3.0-only`. |
| Main interface | `src/index.ts` registers MCP tools over stdio via `@modelcontextprotocol/sdk`. |
| Tool count | README lists 21 tools grouped as indexing, search, graph, impact, management, context artifacts. |
| Storage/search | Qdrant collection stores dense vectors and BM25 sparse vectors; `searchChunks()` runs hybrid search with RRF. |
| Embeddings | Supports Docker Ollama by default and optional OpenAI, Google Gemini, LM Studio/external providers. |
| Chunking | `indexer.ts` uses a three-tier strategy: minified character chunks, AST-aware chunks via ast-grep, line fallback. |
| Graph | File-level import graph plus symbol-level call graph, impact radius, flow tracing, symbol context. |
| Non-code knowledge | `.socraticodecontextartifacts.json` defines artifacts with `name`, `path`, `description`; artifacts are auto-indexed/staleness-checked. |
| Operations | Indexing is batched, checkpointed, resumable, stoppable, and watched for changes. |
| Multi-agent | File locks via `proper-lockfile` coordinate cross-process index/watch operations. |
| Claimed benchmark | README claims VS Code benchmark totals: 61.5% less data, 84% fewer tool calls, 37.2x speedup vs grep-style exploration. |

### 3.2 First-Agent facts constraining adoption

| Constraint | Source |
|------------|--------|
| v0.1 scope is UC1 + UC3; UC2 best-effort; UC4/UC5 deferred | ADR-1 |
| Memory architecture is Mechanical Wiki: filesystem-canonical Markdown + deterministic chunker + grep/FTS5 | ADR-3 |
| No embeddings, no vector index, no graph layer in v0.1 | ADR-3 / ADR-4 |
| Storage backend is SQLite FTS5, disposable and rebuildable from filesystem canon | ADR-4 |
| Chunker baseline is universal-ctags + markdown-it-py; tree-sitter is out of scope for v0.1 | ADR-5 |
| Sandbox policy is deny-by-default read/write allow-list | ADR-6 |
| Project package is proprietary | `pyproject.toml` |

## 4. Reasoning: where SocratiCode fits First-Agent

### 4.1 SocratiCode is "future FA retrieval backend", not "current FA core"

SocratiCode already implements the destination that First-Agent's ADRs keep
explicitly out of v0.1: vectors, external vector DB, AST parsing, file graph,
symbol graph, multi-project search, branch-aware indexing, resumable daemon-like
operation, and MCP-host tool contracts.

That means the safe adoption path is not "port SocratiCode to Python". It is:

1. Extract interface and UX lessons.
2. Preserve v0.1's low-dependency architecture.
3. Add metadata/IDs now so v0.2 can attach a graph/vector layer without a
   schema reset.
4. Avoid code copying because the implementation is AGPL-licensed.

### 4.2 The strongest immediate lesson is routing metadata

SocratiCode's Context Artifacts section is unusually relevant because it solves
an LLM-navigation problem First-Agent already has: not "where is the file?",
but "when should an agent consult this file?".

The key field is not `path`; First-Agent already has paths via `llms.txt`. The
key field is `description`, especially descriptions that say:

- "Check this before writing migrations."
- "Check this before adding endpoints."
- "Check this before naming domain entities."

First-Agent can adapt this without embeddings:

```yaml
artifacts:
  - name: adr-index
    path: knowledge/adr/
    description: >
      Accepted architectural decisions. Check this before proposing implementation
      work or changing scope.
  - name: research-briefing-template
    path: knowledge/prompts/research-briefing.md
    description: >
      Goal-driven research workflow. Check this before writing cross-source
      research notes.
```

This is compatible with `knowledge/llms.txt`; it can be a future structured
companion or a section in the existing index.

### 4.3 RRF can be local and deterministic

SocratiCode's README describes hybrid search as dense vector + BM25 sparse
vector, fused by RRF. Its `qdrant.ts` creates both `dense` and `bm25` vectors
and calls Qdrant Query API with two prefetches plus `query: { fusion: "rrf" }`.

First-Agent should not add Qdrant in v0.1. But RRF itself does not require
Qdrant or vectors. It only requires ranked lists:

- list A: filename/title/tag exact hits,
- list B: SQLite FTS5 BM25 hits,
- list C: symbol/path hints from ctags metadata,
- later list D: vector hits.

RRF then gives a single result order with stable provenance:

`score(chunk) = Σ 1 / (k + rank_in_source)`

This is small, testable, and fits ADR-4. It also gives a clean v0.2 hook: add
the vector list later without rewriting caller logic.

### 4.4 Graph/impact is a high-value v0.2 feature, not a v0.1 dependency

SocratiCode's symbol tools answer the exact questions that coding agents need:

- `codebase_impact`: what breaks if I change X?
- `codebase_flow`: what does this entry point do?
- `codebase_symbol`: definition + callers + callees.
- `codebase_symbols`: find/list symbols.

This is more useful for UC1 than generic knowledge graphs. But First-Agent's
ADR-3 explicitly says no graph extraction in v0.1, and ADR-5 chooses ctags
instead of tree-sitter/ast-grep.

The v0.1 lesson is therefore schema hygiene:

- preserve line ranges,
- preserve symbol names and kinds,
- preserve stable anchors,
- preserve content hashes and chunker version,
- avoid anchors that collapse filenames or duplicate headings.

Those choices make a future graph layer additive.

### 4.5 Operational reliability is worth copying early

SocratiCode has a mature operational contract around indexing:

- background index,
- explicit status,
- graceful stop,
- checkpoint after batch,
- resume after interruption,
- watcher catch-up after restart,
- file locks for cross-process safety.

First-Agent v0.1 may be smaller, but `notes/inbox/` and `fa ingest` can still
hit large Markdown/source files. A one-shot reindex that loses progress on
interrupt will create avoidable user friction. SQLite can cheaply store the same
minimum state in `meta`.

This does not require Docker, Qdrant, or MCP.

### 4.6 Search-before-read is a prompt-level behaviour worth stealing

SocratiCode's AGENTS.md/CLAUDE.md is a compact operating manual for avoiding
context bloat:

1. semantic/broad search before reading files,
2. grep only when exact string is known,
3. graph before following imports,
4. impact before refactor,
5. context artifacts before schema/API/domain work,
6. status check when search returns nothing.

First-Agent can encode the same ladder in its own prompts, substituting current
tools:

| If the user asks... | First-Agent v0.1 action |
|---------------------|--------------------------|
| "Where is concept X?" | `llms.txt` / manifest route → FTS5 BM25 top-k |
| "Find exact function/error/path" | grep/path/symbol exact search |
| "Edit file X" | sandbox read check → chunk read → edit |
| "Research source set" | research-briefing workflow |
| "Why did we choose Y?" | ADR index → exploration DAG |

This is cheap and directly addresses the user's critique about shallow or noisy
research/context handling.

## 5. Do / do not copy

| Category | Copy? | Why |
|----------|-------|-----|
| Context-artifact manifest shape (`name`, `path`, `description`) | YES, conceptually | Small product pattern; can be independently implemented. |
| "Description says when to use this" | YES | High-value LLM-routing idea; no dependency. |
| RRF over ranked lists | YES, independently | Algorithmic ranking idea; implement from scratch over SQLite/grep results. |
| Status/stop/resume/checkpoint contract | YES, independently | General operational pattern; maps well to SQLite. |
| Stable IDs for future graph | YES | Prevents future migration cost. |
| Search-before-read prompt guidance | YES | Prompt/process design, not source code. |
| Qdrant/Ollama Docker manager | NO for v0.1 | Violates ADR-4 minimal backend and adds infra. |
| ast-grep/tree-sitter style code parsing | DEFER | ADR-5 chooses ctags for v0.1; revisit after corpus shows need. |
| Symbol graph implementation internals | NO now | Heavy, AGPL source, and v0.2 scope. |
| Source code from SocratiCode | NO | AGPL-3.0/commercial license; First-Agent is proprietary. |

## 6. Risks and caveats

1. **Benchmark caveat.** SocratiCode's benchmark is not reproduced here. Treat
   it as motivation, not as First-Agent evidence.
2. **Static-analysis caveat.** SocratiCode itself documents that impact analysis
   misses dynamic dispatch, reflection, macros, DI/framework magic, and unresolved
   edges. A future FA graph must expose quality signals, not pretend completeness.
3. **Scope caveat.** Importing graph/vector infrastructure into v0.1 would
   contradict ADR-3 and ADR-4. Use this note to resist that scope creep.
4. **License caveat.** Do not copy AGPL implementation code into First-Agent.
5. **Tooling caveat.** SocratiCode's own repo instructions say to use its MCP
   search tools before reading files. Those exact MCP tools were not available
   in this Devin session; targeted file reading plus Devin wiki questions were
   used instead.
6. **Language/ecosystem caveat.** SocratiCode is TypeScript/Node; First-Agent is
   Python. Some operational choices (`@parcel/watcher`, `proper-lockfile`,
   `@ast-grep/napi`) do not transfer directly.

## 7. Numbered recommendations (R-1..R-7)

### R-1 — Import context artifacts as a manifest (cost: cheap)

First-Agent already has `knowledge/llms.txt`, but it is a URL index, not an
action-trigger index. SocratiCode's `description` field is the missing layer:
"what is this, and when should an agent check it?".

The simplest version is a new `knowledge/context-artifacts.yaml` or a structured
section in `llms.txt`. It should list ADRs, research prompts, research notes,
`notes/inbox/`, and external specs/schemas if they exist. No embeddings required.

### R-2 — Add local RRF over current retrieval sources (cost: medium)

RRF is the easiest SocratiCode search lesson to adopt without importing its
backend. First-Agent can combine exact filename/title/tag hits with FTS5 BM25
hits and future ctags symbol hits. This should produce better top-k than any
single retriever while staying deterministic and testable.

The implementation should be independent, tiny, and explainable in tests:
same chunk found by multiple retrievers rises; a chunk high in only one list
can still win; ties are stable by source priority then chunk ID.

### R-3 — Make ingest/reindex resumable from the first implementation (cost: medium)

The SocratiCode indexing contract is useful even for a smaller SQLite project:
status, stop, checkpoints, hashes, and restart recovery. First-Agent should not
wait for a daemon or vector backend to define these fields.

Minimum viable SQLite state:

- `indexing_status`: `idle | in_progress | stopped | failed`,
- `last_checkpoint_batch`,
- `files_total`, `files_processed`,
- `last_error`,
- `chunker_version`,
- per-file content hash.

### R-4 — Reserve symbol/graph compatibility metadata now (cost: cheap)

ADR-5's chunker work should keep more metadata than v0.1 search strictly needs.
The current `Chunk` dataclass already trends this way with path, anchor, parent
title, breadcrumb, language, line/byte ranges, and topic in the PR #31 design.

Keep that richness. Do not compress anchors too aggressively. Future graph
edges need stable targets.

### R-5 — Defer Qdrant/Ollama managed infra (cost: expensive)

SocratiCode's managed infrastructure is impressive, but it is exactly the
dependency surface that First-Agent deferred. Do not import it until the corpus
or evaluation shows SQLite FTS5 is insufficient.

If semantic search is needed before full vector infra, prefer a same-file
SQLite option first (`sqlite-vec` / `sqlite-vss` style) because it preserves
ADR-4's single-disposable-cache story.

### R-6 — Add search-before-read guidance to prompts (cost: cheap)

First-Agent prompts should tell future agents to avoid speculative file reads.
The rule should be explicit enough that a mid-tier Coder follows it:

- broad concept → retrieval,
- exact string/path → grep/path search,
- architecture decision → ADR/DAG,
- research task → research-briefing,
- schema/API/domain work → context artifact manifest.

This is a high-ROI prompt change independent of implementation.

### R-7 — Keep a license firewall (cost: cheap)

Use this note as a policy pointer: SocratiCode is a reference, not a source pool.
It is safe to use public behaviour, tool names as inspiration, and general
algorithms/patterns that are independently implemented. It is not safe to copy
AGPL source files into a proprietary First-Agent package without legal review.

## 8. Open questions (Q-1..Q-4)

### Q-1 — Should the context-artifact manifest be separate or folded into `llms.txt`?

Separate YAML is easier for tools; `llms.txt` is easier for one-fetch agents.
The practical compromise may be: keep `llms.txt` as the human/agent index and
add a small machine-readable YAML later when code needs it.

### Q-2 — How much symbol metadata should the v0.1 chunk table store?

Too little metadata makes v0.2 graph backfill expensive. Too much can freeze an
unproven schema. A safe middle ground is to store optional symbol fields as
nullable columns or JSON payload while keeping `(path, anchor, body)` canonical.

### Q-3 — When does First-Agent earn vectors?

The trigger should be measured: FTS5 top-k fails on conceptual queries across a
real corpus, or token-efficiency misses the ≤10% context target from
`project-overview.md`. Do not add vectors because SocratiCode has them.

### Q-4 — Should graph/impact become ADR-7/ADR-8 scope?

Probably not for v0.1. It fits a v0.2 "code intelligence layer" ADR after the
chunker, search index, and sandbox tools exist and have real fixture data.

## 9. Files used

- `https://github.com/giancarloerra/SocratiCode`
- `/home/ubuntu/research_sources/SocratiCode/README.md`
- `/home/ubuntu/research_sources/SocratiCode/DEVELOPER.md`
- `/home/ubuntu/research_sources/SocratiCode/package.json`
- `/home/ubuntu/research_sources/SocratiCode/AGENTS.md`
- `/home/ubuntu/research_sources/SocratiCode/CLAUDE.md`
- `/home/ubuntu/research_sources/SocratiCode/src/index.ts`
- `/home/ubuntu/research_sources/SocratiCode/src/services/indexer.ts`
- `/home/ubuntu/research_sources/SocratiCode/src/services/qdrant.ts`
- `/home/ubuntu/research_sources/SocratiCode/src/services/graph-impact.ts`
- `/home/ubuntu/research_sources/SocratiCode/src/services/graph-symbols.ts`
- `/home/ubuntu/research_sources/SocratiCode/src/services/symbol-graph-store.ts`
- `/home/ubuntu/research_sources/SocratiCode/src/services/context-artifacts.ts`
- `/home/ubuntu/research_sources/SocratiCode/src/services/lock.ts`
- `/home/ubuntu/research_sources/SocratiCode/src/services/startup.ts`
- `/home/ubuntu/research_sources/SocratiCode/src/services/watcher.ts`
- `/home/ubuntu/repos/First-Agent-fork2/knowledge/project-overview.md`
- `/home/ubuntu/repos/First-Agent-fork2/knowledge/adr/ADR-1-v01-use-case-scope.md`
- `/home/ubuntu/repos/First-Agent-fork2/knowledge/adr/ADR-2-llm-tiering.md`
- `/home/ubuntu/repos/First-Agent-fork2/knowledge/adr/ADR-3-memory-architecture-variant.md`
- `/home/ubuntu/repos/First-Agent-fork2/knowledge/adr/ADR-4-storage-backend.md`
- `/home/ubuntu/repos/First-Agent-fork2/knowledge/adr/ADR-5-chunker-tool.md`
- `/home/ubuntu/repos/First-Agent-fork2/knowledge/adr/ADR-6-tool-sandbox-allow-list.md`

## 10. Out of scope

- Reproducing SocratiCode's VS Code benchmark.
- Running Docker/Qdrant/Ollama locally.
- Porting or copying SocratiCode implementation code.
- Drafting a new ADR.
- Implementing First-Agent search, chunker, graph, or manifest changes in this
  PR.
