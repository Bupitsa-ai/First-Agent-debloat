---
title: "Сообщество LLM-Wiki — батч 1: что из этого реально работает в продакшне"
compiled: "2026-04-26"
source:
  - https://github.com/JuliusBrussee/cavemem
  - https://github.com/Larens94/codedna
  - https://github.com/kytmanov/obsidian-llm-wiki-local
  - https://github.com/cablate/llm-atomic-wiki
  - https://github.com/alexdcd/AI-Context-OS
  - https://github.com/agent-creativity/agentic-local-brain
chain_of_custody:
  - "Досье 1–3 написаны пользователем (MondayInRussian) по его собственному
    workflow-промпту (досье 0). Я их прочёл целиком и сверил с самими
    репозиториями, которые выкачал отдельно 2026-04-23/24."
  - "Все цифры о звёздах/коммитах — на дату фетча; могут устареть."
  - "Спорные числа (см. §6) проверял отдельно: README-первоисточник."
status: research
claims_requiring_verification:
  - "cavemem ~75% token reduction — claim автора, не измеренный бенчмарк."
  - "CodeDNA +17pp F1 SWE-bench — n=10 patches, signal, не proof."
  - "Звёзды/контрибьюторы всех шести репо — на дату фетча 2026-04-23/24."
scope: |
  Часть 2A исследования сообщества вокруг LLM Wiki-паттерна. Шесть проектов.
related:
  - knowledge/research/llm-wiki-critique.md
  - knowledge/research/llm-wiki-critique-first-agent.md
  - knowledge/research/agent-roles.md
superseded_by: "knowledge/adr/ADR-3-memory-architecture-variant.md"
---

# Сообщество LLM-Wiki — батч 1

> **Status:** superseded by [`adr/ADR-3-memory-architecture-variant.md`](../adr/ADR-3-memory-architecture-variant.md) (archived 2026-05-08; body trimmed 2026-05-11 per PR-M).
>
> Excluded from `knowledge/llms.txt §BY-DEMAND-INDEX` for the OSS-agent routing surface. Survey input to ADR-3 (memory architecture variant); cheat-sheet row in [`adr/DIGEST.md`](../adr/DIGEST.md).
>
> **Body trimmed in PR-M to TL;DR + project-list abstract; full pre-trim text in git history at commit `cf7db4d`** (`git show cf7db4d:knowledge/research/llm-wiki-community-batch-1.md`).

## 1. TL;DR

- **Шесть проектов, два жанра.** Четыре делают *вики/память для
  человека-пользователя* в духе Карпатого (`obsidian-llm-wiki-local`,
  `llm-atomic-wiki`, `AI-Context-OS`, `agentic-local-brain`). Два —
  *память для агента-кодера*, не для человека (`cavemem`, `codedna`).
- **Что переиспользуется — это не код, а паттерны.** Все шесть — мелкие
  репозитории (звёзды от 19 до ~290). Конкретные инженерные решения
  внутри — повторяющиеся, проверенные, и часть из них уже подтверждена
  в research-литературе.
- **Кросс-валидация — главный фильтр.** Паттерны, которые независимо
  возникли в 2+ проектах, заведомо ценнее одиночных красивых идей.
- **Скепсис.** Цифры в README надо читать как маркетинг, не как
  бенчмарк. Сами по себе репозитории — это *инженерные эссе*, не
  научные статьи.

## Six projects at a glance

| # | Project | Stars | Verdict | What we kept |
|---|---|---|---|---|
| 3.1 | [obsidian-llm-wiki-local](https://github.com/kytmanov/obsidian-llm-wiki-local) | ~290 | Production-ready for its niche | Selective recompile; rejection-as-training; two-tier LLM routing; manual-edit protection |
| 3.2 | [llm-atomic-wiki](https://github.com/cablate/llm-atomic-wiki) | ~110 | Methodology, not runtime | Two-layer lint (programmatic → LLM); CLAUDE.md as agent constitution; parallel-compile naming lock; segment classification before extraction |
| 3.3 | [AI-Context-OS (MEMM)](https://github.com/alexdcd/AI-Context-OS) | ~115 | Richest engineering, but overengineered | L0/L1/L2 progressive loading; intent-aware scoring weights; two-pass retrieval (seeds → PPR); conflict detection by tech-pairs. **Deep-dive in [`ai-context-os-memm-deep-dive.md`](./ai-context-os-memm-deep-dive.md).** |
| 3.4 | [agentic-local-brain](https://github.com/agent-creativity/agentic-local-brain) | ~80 | Skill-routing patterns, no runtime | Skill index → trigger-based dispatch; agent constitution; markdown-as-code pattern |
| 3.5 | [cavemem](https://github.com/JuliusBrussee/cavemem) | ~50 | Agent memory (not human wiki) | Token reduction via summarization; chunk grouping; recency-based eviction |
| 3.6 | [codedna](https://github.com/Larens94/codedna) | ~40 | In-source annotation for code agents | `rules:` (architectural truth); `used_by:` (cross-ref); v0.8 `message:` (soft hypothesis channel) |

## Where the decisions live now

- **ADR-3 memory architecture:** Variant A (Mechanical Wiki —
  filesystem-canonical Markdown + SQLite FTS5). Patterns 3.1, 3.2, 3.6
  cited as input.
- **Storage backend (ADR-4):** SQLite + FTS5. Pattern 3.3 (MEMM's
  six-signal scoring) deferred to v0.2.
- **Skill dispatcher:** Pattern 3.4 → ADR-8 (skills system, open).
  See [`agentic-memory-supplement.md`](./agentic-memory-supplement.md)
  §4 for the codedna delta and gbrain/sparks patterns.

## Full pre-trim text

`git show cf7db4d:knowledge/research/llm-wiki-community-batch-1.md` —
433 lines, last full revision 2026-05-08. Contains: full §2 methodology,
§3 six per-project verdicts (with code-snippets and refs), §4 top-7
cross-validated patterns + which we ship in v0.1, §5 deferred items,
§6 verification table for marketing claims, §7 implications for FA.
