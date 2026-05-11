---
title: "Research — LLM Wiki критика: применимость к First-Agent"
source:
  - "./llm-wiki-critique.md"
  - "./llm-wiki-critique-sources.md"
  - "./agent-roles.md"
compiled: "2026-04-24"
chain_of_custody: >
  Этот файл — *применение выводов* к нашему проекту. Все внешние
  утверждения со ссылкой на критиков или литературу — верифицировать
  через parent-заметку llm-wiki-critique.md (§Sources).
claims_requiring_verification: []
superseded_by: "knowledge/research/llm-wiki-critique.md"
---

# Research — LLM Wiki критика: применимость к First-Agent

> **Status:** superseded by [`research/llm-wiki-critique.md`](./llm-wiki-critique.md) (archived 2026-05-08; body trimmed 2026-05-11 per PR-M).
>
> Excluded from `knowledge/llms.txt §BY-DEMAND-INDEX` for the OSS-agent routing surface. Provenance-frontmatter schema (T1) is in [`knowledge/README.md`](../README.md).
>
> **Body trimmed in PR-M to a key-takeaways abstract; full pre-trim text in git history at commit `cf7db4d`** (`git show cf7db4d:knowledge/research/llm-wiki-critique-first-agent.md`). Trim rationale: `repo-audit-2026-05-10-revised.md` §4.1.

## Abstract

«Что из критики LLM Wiki (Lahoti / Gravelle / Kumar / rohitg00) мы тащим
в First-Agent». Применимость к памяти **LLM-агента** (не вики для
человека), списки «берём / заглядываем / не берём», пересечения с ролями
агента, Engelbart-фрейм.

## Key takeaways (decisions that already shipped)

- **T1 — Provenance-frontmatter on every research note** (`source`,
  `compiled`, `chain_of_custody`, `claims_requiring_verification`).
  Shipped to [`knowledge/README.md`](../README.md).
- **T2 — Корпус-стратификация** (стабильное vs volatile). Shipped to
  [`docs/architecture.md`](../../docs/architecture.md) §Архитектура
  памяти.
- **T3 — Supersession not silent overwrite.** Shipped to AGENTS.md PR
  Checklist rule #5 + `knowledge/README.md` conventions.
- **T4 — Cognitive-taxonomy mapping** (working/episodic/semantic/
  procedural). Shipped to `docs/architecture.md`.
- **T5 — Доступ через search, не load.** Shipped as principle in
  `docs/architecture.md`; concrete tier mapping in
  [ADR-3](../adr/ADR-3-memory-architecture-variant.md) + ADR-4.
- **T6 — Routing-раздел в AGENTS.md** («куда смотреть для разного типа
  знания»). Shipped as AGENTS.md §Query Routing.
- **T7 — Schema как продукт** (AGENTS.md, README.md эволюционируют через
  PR как код). De facto adopted; codified via PR Checklist.

## Deferred (L-N items moved to v0.2+)

- L1 confidence scoring; L2 typed knowledge graph; L3 hybrid BM25+vector+
  graph search; L4 retention decay; L5 crystallization; L6 multi-agent
  conflict files.

## Rejected (N-N items)

- N1 Ebbinghaus formula (metaphor, not measurement); N2 jDocMunch
  (vendor lock-in); N3 self-healing lint; N4 contradiction resolver;
  N5 «LLM Wiki kills RAG» dichotomy; N6 native KG before v0.1.

## Full pre-trim text

`git show cf7db4d:knowledge/research/llm-wiki-critique-first-agent.md` —
230 lines, last full revision 2026-05-08. Contains:

- §5 «Применимость к памяти LLM-агента» — feedback-loop tightness,
  scale mismatch, multi-agent write
- §6 full TAKE / LATER / REJECT tables with rationale per row
- §7 cross-reference to `agent-roles.md` (Critic / Executor / Planner)
- §8 Engelbart bootstrapping frame
- §9 concrete edits to `docs/architecture.md` / `knowledge/README.md` /
  `AGENTS.md`
- Provenance frontmatter template (T1)
