---
title: "Agentic memory — supplement: что добавилось после wiki-критики и role-исследования"
compiled: "2026-04-27"
source:
  - knowledge/research/llm-wiki-critique.md
  - knowledge/research/llm-wiki-community-batch-1.md
  - knowledge/research/llm-wiki-community-batch-2.md
  - knowledge/research/agent-roles.md
  - https://github.com/Larens94/codedna
  - https://github.com/agent-creativity/agentic-local-brain
  - https://github.com/sparks-org/sparks-memory
chain_of_custody:
  - "Эта заметка консолидирует delta-выводы из четырёх предыдущих research notes."
  - "Все ссылки на сторонние репозитории — на дату фетча 2026-04-25/26."
  - "Все ссылки на in-repo research — verified path:line."
status: research
related:
  - knowledge/research/agent-roles.md
  - knowledge/research/llm-wiki-community-batch-1.md
  - knowledge/research/llm-wiki-community-batch-2.md
  - knowledge/research/ai-context-os-memm-deep-dive.md
  - knowledge/adr/ADR-3-memory-architecture-variant.md
superseded_by: "knowledge/adr/ADR-3-memory-architecture-variant.md"
---

# Agentic memory — supplement

> **Status:** superseded by [`adr/ADR-3-memory-architecture-variant.md`](../adr/ADR-3-memory-architecture-variant.md) (archived 2026-05-08; body trimmed 2026-05-11 per PR-M).
>
> Excluded from `knowledge/llms.txt §BY-DEMAND-INDEX` for the OSS-agent routing surface. Delta supplement to four prior research notes; ADR-3 captures the consolidated decision.
>
> **Body trimmed in PR-M to TL;DR + delta-list abstract; full pre-trim text in git history at commit `cf7db4d`** (`git show cf7db4d:knowledge/research/agentic-memory-supplement.md`). Trim rationale: `repo-audit-2026-05-10-revised.md` §4.1.

## 1. TL;DR — what's new since the earlier notes

This note collects **deltas** that emerged after the four prior research
notes (`llm-wiki-critique.md`, `llm-wiki-community-batch-1.md`,
`llm-wiki-community-batch-2.md`, `agent-roles.md`). Each delta either
sharpens an existing pattern or adds a new one not in the parent notes.

Six deltas. Three shipped (D1, D3, D5), two deferred (D2, D6), one
rejected (D4).

| # | Delta | Source | Status |
|---|---|---|---|
| D1 | **codedna `message:` channel** — soft hypothesis annotation in source, separate from `rules:` (authoritative). Allows planner-to-coder hint without overcommitment. | [Larens94/codedna](https://github.com/Larens94/codedna) v0.8 | **Shipped** in ADR-8 prep notes |
| D2 | **agentic-local-brain skill-index dispatch** — markdown skill files indexed by trigger phrase, agent picks at runtime. | [agent-creativity/agentic-local-brain](https://github.com/agent-creativity/agentic-local-brain) | Deferred to ADR-8 |
| D3 | **sparks-memory's «working-set» concept** — separates *what is in context* from *what is on disk*; working-set bounded by token budget. | sparks-memory (unmaintained) | **Shipped** as AGENTS.md rule #11 (≤100k context budget) |
| D4 | **Per-document confidence scoring** — every doc tagged 0-1 confidence at write time. | rohitg00 LLM Wiki v2 | **Rejected** — premature, no measurement framework |
| D5 | **Provenance frontmatter as canonical** — `source:`, `compiled:`, `chain_of_custody:`, `claims_requiring_verification:`. | llm-wiki-critique §T1 | **Shipped** to `knowledge/README.md` schema |
| D6 | **Two-pass retrieval (FTS5 seeds → graph walk)** — deeper than batch-1 §3.3 surface mention. | AI-Context-OS / MEMM | Deferred to ADR-4 v0.2 |

## Where the decisions live now

- **ADR-3** — Variant A (Mechanical Wiki). D1, D5 cited.
- **ADR-4** — SQLite + FTS5. D6 deferred.
- **ADR-8 (open)** — skills system. D1, D2 cited as inputs.
- **AGENTS.md rule #11** — context budget ≤100k tokens. D3 cited as
  inspiration.
- **`knowledge/README.md`** — frontmatter schema. D5 shipped.

## Full pre-trim text

`git show cf7db4d:knowledge/research/agentic-memory-supplement.md` —
597 lines, last full revision 2026-05-08. Contains: §2 detailed
discussion of each delta (D1-D6) with code citations and trade-off
analysis, §3 cross-validation matrix (which deltas are corroborated by
multiple sources), §4 commitment table for FA v0.1, §5 deferred items
with unblock triggers, §6 verification claims.
