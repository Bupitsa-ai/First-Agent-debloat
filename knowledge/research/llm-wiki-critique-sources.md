---
title: "Research — Критика Karpathy's LLM Wiki — разбор по источникам"
source:
  - "https://foundanand.medium.com/the-hidden-flaw-in-karpathys-llm-wiki-e3a86a94b459"
  - "https://dev.to/jgravelle/a-radical-diet-for-karpathys-token-eating-llm-wiki-59ng"
  - "https://ranjankumar.in/llm-wiki-synthesis-time-decision-rag-agentic-memory"
  - "https://github.com/ChavesLiu/second-brain-skill/blob/main/README.en.md"
  - "https://gist.github.com/rohitg00/2067ab416f7bbe447c1977edaaa681e2"
  - "https://www.dougengelbart.org/content/view/110/460/"
compiled: "2026-04-24"
chain_of_custody: >
  Все цитаты, формулировки тезисов и цифры — через ссылки `source:`.
  Этот файл — критический пересказ, не первоисточник.
claims_requiring_verification:
  - "jDocMunch benchmark numbers (19.9× / 95%)"
  - "Claim that Karpathy wiki runs at ~100 articles / 400K words"
  - "rohitg00's list of eight extensions matches the current gist revision"
superseded_by: "knowledge/research/llm-wiki-critique.md"
---

# Research — Разбор источников критики LLM Wiki

> **Status:** superseded by [`research/llm-wiki-critique.md`](./llm-wiki-critique.md) (archived 2026-05-08; body trimmed 2026-05-11 per PR-M).
>
> Excluded from `knowledge/llms.txt §BY-DEMAND-INDEX` for the OSS-agent routing surface. Companion source-list to the parent critique note.
>
> **Body trimmed in PR-M to a per-source one-paragraph abstract; full pre-trim text in git history at commit `cf7db4d`** (`git show cf7db4d:knowledge/research/llm-wiki-critique-sources.md`).

## Abstract

Six-source critique of Karpathy's LLM Wiki pattern. Each source covered
with **Тезис → Иллюстрация → Что сильно → Что слабо → Что берём**
structure in the pre-trim full body.

## Sources at a glance

| # | Source | Core thesis | What we kept |
|---|---|---|---|
| 2.1 | Anand Lahoti — *The Hidden Flaw* ([Medium](https://foundanand.medium.com/the-hidden-flaw-in-karpathys-llm-wiki-e3a86a94b459)) | LLM-authored prose, indexed alongside raw, creates knowledge-base poisoning. Lint catches internal consistency, not ground-truth drift. | Diagnosis only (mechanism of drift); see T1 / T3 in parent note. |
| 2.2 | J. Gravelle — *A Radical Diet* ([dev.to](https://dev.to/jgravelle/a-radical-diet-for-karpathys-token-eating-llm-wiki-59ng)) | Access pattern is the bug, not structure. «Load full wiki» dies at 50-100K tokens; long-context degrades at 200-300K. Fix: search, don't load. | T5 «доступ — search, не load». Cited in parent note + ADR-3 §Decision. |
| 2.3 | Ranjan Kumar — *Synthesis-Time Decision* ([ranjankumar.in](https://ranjankumar.in/llm-wiki-synthesis-time-decision-rag-agentic-memory)) | Most thorough. **Synthesis Horizon** + **corpus-stratified synthesis** (ingest-time for stable, query-time for authoritative). Provenance-frontmatter schema. Write-governance for multi-agent. | T1, T2, T3 — all shipped. Cited in parent note + ADR-3 + `docs/architecture.md`. |
| 2.4 | ChavesLiu — *second-brain-skill* ([GitHub](https://github.com/ChavesLiu/second-brain-skill/blob/main/README.en.md)) | Implementation reference, not critique. Slash-commands wrapper for Karpathy pattern in Claude Code. | Directory layout (`wiki/{index, log, overview, conventions, sources, entities, concepts, analyses}/`) cited as one inspiration for `knowledge/` layout. |
| 2.5 | rohitg00 — *LLM Wiki v2* ([gist](https://gist.github.com/rohitg00/2067ab416f7bbe447c1977edaaa681e2)) | Largest extension (8 systems): memory lifecycle, typed KG, hybrid search, automation hooks, quality+self-correction, multi-agent mesh, privacy, crystallisation. | T4 (cognitive taxonomy framing); rest deferred to v0.2+ (L1-L6 in parent note). |
| 2.6 | Doug Engelbart — OHS / NLS / Bootstrapping ([dougengelbart.org](https://www.dougengelbart.org/content/view/110/460/)) | Frame, not spec. Bootstrapping loop: the system improves itself. Typed-link primitives. | Cited as bootstrapping target in `docs/architecture.md`; not a v0.1 implementation input. |

## Cross-references in parent note

- [`research/llm-wiki-critique.md`](./llm-wiki-critique.md) §Sources —
  full TL;DR + verification table for jDocMunch numbers, gist revisions,
  Karpathy stats.
- [`research/llm-wiki-critique-first-agent.md`](./llm-wiki-critique-first-agent.md)
  — applicability mapping (T1-T7, L1-L6, N1-N6).

## Full pre-trim text

`git show cf7db4d:knowledge/research/llm-wiki-critique-sources.md` — 302
lines, last full revision 2026-05-08. Contains: full Тезис / Иллюстрация
/ Сильно / Слабо / Берём breakdown per source + cross-source cites to
Liu et al. 2024 (Lost-in-the-Middle), Park 2023 (Generative Agents),
Packer 2023 (MemGPT), Shinn 2023 (Reflexion), Doyle 1979 (TMS),
DokuWiki / Gutmans, Engelbart NLS demo.
