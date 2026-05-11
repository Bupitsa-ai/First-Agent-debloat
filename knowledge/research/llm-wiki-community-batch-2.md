---
title: "Сообщество LLM-Wiki — батч 2: вторая волна, узкие нишевые проекты"
compiled: "2026-04-26"
source:
  - https://github.com/Liadovskaya/llm-wiki-cli
  - https://github.com/dancmcleod/cog-cards
  - https://github.com/ai-noosphere/agora-wiki
  - https://github.com/mdrwiega/agents-as-yaml-files
  - https://github.com/jasper-mike/codeconcept
  - https://github.com/sirius-research/sirius-wiki
chain_of_custody:
  - "Все шесть репозиториев фетчены 2026-04-24/25. Звёзды/коммиты — на дату фетча."
  - "В отличие от батча 1, эти проекты в основном экспериментальные —
    сильнее зашумлены, скептицизм должен быть выше."
status: research
claims_requiring_verification:
  - "agora-wiki claim about consensus building between agents — claim only, no benchmark."
  - "cog-cards claim about cognitive load reduction — methodology unverified."
  - "Stars / contributors counts — on fetch date."
scope: |
  Часть 2B исследования сообщества LLM Wiki-паттерна. Шесть нишевых проектов.
related:
  - knowledge/research/llm-wiki-critique.md
  - knowledge/research/llm-wiki-community-batch-1.md
  - knowledge/research/llm-wiki-critique-first-agent.md
superseded_by: "knowledge/adr/ADR-3-memory-architecture-variant.md"
---

# Сообщество LLM-Wiki — батч 2

> **Status:** superseded by [`adr/ADR-3-memory-architecture-variant.md`](../adr/ADR-3-memory-architecture-variant.md) (archived 2026-05-08; body trimmed 2026-05-11 per PR-M).
>
> Excluded from `knowledge/llms.txt §BY-DEMAND-INDEX` for the OSS-agent routing surface. Companion to batch-1 survey; covered the long tail of nichier or weaker LLM-Wiki community projects.
>
> **Body trimmed in PR-M to TL;DR + project-list abstract; full pre-trim text in git history at commit `cf7db4d`** (`git show cf7db4d:knowledge/research/llm-wiki-community-batch-2.md`).

## 1. TL;DR

- **Шесть проектов второй волны.** В отличие от батча 1, тут больше
  *экспериментальной территории*: agents-as-yaml-files (manifesto-led),
  agora-wiki (multi-agent consensus), sirius-wiki (research-grade
  prototype). Меньше production-ready кода, больше идей.
- **Что мы взяли:** cog-cards's «card-as-atomic-unit» pattern → подкрепил
  Variant A (Mechanical Wiki) atomic-doc convention. agora-wiki's
  «typed-link primitives» (claim / refutation / evidence) → consideration
  for v0.2 typed-link experiment.
- **Что отвергли:** agents-as-yaml-files (manifesto-style, no working
  code); llm-wiki-cli (single dev, abandoned); sirius-wiki (research
  prototype with no clear API).
- **Кросс-валидация:** один паттерн совпал с батчем 1 — «namespace
  isolation per session» (cog-cards + cavemem). Подкрепили в ADR-3.

## Six projects at a glance

| # | Project | Stars | Verdict | What we kept |
|---|---|---|---|---|
| 4.1 | [llm-wiki-cli](https://github.com/Liadovskaya/llm-wiki-cli) | ~20 | Abandoned, single-dev | None — listed for completeness only. |
| 4.2 | [cog-cards](https://github.com/dancmcleod/cog-cards) | ~60 | Niche but coherent | Atomic-doc pattern; session-namespace isolation. |
| 4.3 | [agora-wiki](https://github.com/ai-noosphere/agora-wiki) | ~45 | Multi-agent consensus | Typed-link primitives (claim / refutation / evidence) — deferred to v0.2. |
| 4.4 | [agents-as-yaml-files](https://github.com/mdrwiega/agents-as-yaml-files) | ~30 | Manifesto, no working code | None. |
| 4.5 | [codeconcept](https://github.com/jasper-mike/codeconcept) | ~25 | Code-only domain, narrow | Concept-extraction-pipeline (deferred to v0.2). |
| 4.6 | [sirius-wiki](https://github.com/sirius-research/sirius-wiki) | ~15 | Research prototype | None — early-stage. |

## Where the decisions live now

- **ADR-3 memory architecture:** Variant A. Patterns 4.2 (atomic doc),
  4.2 / cavemem cross-validated «namespace isolation» cited as input.
- **Deferred (v0.2+):** Pattern 4.3 typed-link primitives; pattern 4.5
  concept extraction; pattern 4.6 research-grade write-side.
- **Companion batch-1:** [`llm-wiki-community-batch-1.md`](./llm-wiki-community-batch-1.md).

## Full pre-trim text

`git show cf7db4d:knowledge/research/llm-wiki-community-batch-2.md` —
535 lines, last full revision 2026-05-08. Contains: full §2
methodology, §3 six per-project verdicts (with code-snippets and refs),
§4 cross-batch pattern matrix (which patterns from batch-1 are
corroborated here), §5 deferred items, §6 verification table, §7
implications for FA.
