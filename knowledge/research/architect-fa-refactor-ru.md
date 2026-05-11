---
title: "Architect/Planner — рефакторинг системного промпта (v2.1, RU)"
compiled: "2026-04-26"
source:
  - knowledge/prompts/architect-fa.md
  - knowledge/prompts/architect-fa-compact.md
  - предыдущие версии Architect prompt (v1.0 и GPT-5.5 refactor) — приложены к
    исходному запросу, в репозиторий не коммитились
chain_of_custody: "Сам системный промпт лежит в knowledge/prompts/architect-fa.md
  и knowledge/prompts/architect-fa-compact.md. Цитаты конкретных правил/полей —
  оттуда. Ссылки на исследования (TDP, GraSP, VeriPlan, Reflexion и т.д.) — из
  attached improvements-research документа, перепроверять по arxiv-URL при
  использовании конкретных чисел."
claims_requiring_verification:
  - "TDP снижает потребление токенов до 82% — цифра из abstract attached
    research; перед цитированием перепроверить по arxiv 2601.07577."
  - "WHO Surgical Safety Checklist снизил смертность на 47% — Haynes et al.,
    NEJM 2009; перепроверить."
  - "Reflexion / Self-Verifying Reflection: формальная гарантия улучшения при
    ограниченных ошибках верификации — теоретический результат, перепроверить
    по оригинальной статье."
superseded_by: "knowledge/prompts/architect-fa-compact.md"
---

# Architect/Planner — рефакторинг системного промпта (v2.1)

> **Status:** superseded by [`knowledge/prompts/architect-fa-compact.md`](../prompts/architect-fa-compact.md) (archived 2026-05-08; body trimmed 2026-05-11 per PR-M).
>
> Excluded from `knowledge/llms.txt §BY-DEMAND-INDEX` for the OSS-agent routing surface. Design diary for the Architect/Planner system prompt; the final prompts shipped in `architect-fa-compact.md` (default) and [`architect-fa.md`](../prompts/architect-fa.md) (full).
>
> **Body trimmed in PR-M to TL;DR + design-principles abstract; full pre-trim text in git history at commit `cf7db4d`** (`git show cf7db4d:knowledge/research/architect-fa-refactor-ru.md`). Trim rationale: `repo-audit-2026-05-10-revised.md` §4.1.

## TL;DR

- Исходный Architect v1.0 был **процедурно перегружен**: 10-стейтовый
  FSM, три параллельных YAML-артефакта (`plan.md`, `coder-handoff.yaml`,
  `debugger-handoff.yaml`), фиксированный action-vocabulary. Для
  open-source planner-моделей (Kimi-2.6, GLM-5.1) это превращается в
  process theater.
- Промежуточный refactor (GPT-5.5) исправил большую часть бюрократии,
  но перетянут в сторону Terminal-Bench / unattended execution и
  недостаточно учитывает топологию **planner сильнее coder/reviewer**.
- v2.1 решает обе проблемы: (а) обобщён под любые real-repo задачи (код,
  инфра, данные, документация, рефакторинг, конфиг), (б) усилен под более
  слабые downstream-агенты (правило step independence, acceptance
  taxonomy, фиксированный порядок полей шага).
- Итог: **один масштабируемый формат**, **типизированный evidence
  floor**, 5-полевый контракт шага, жёсткий запрет галлюцинаций,
  двухуровневая блокировка, локальное восстановление через Delta Plan,
  и pre-output self-check из 11 пунктов.

## Принципы дизайна v2.1 (вкратце)

1. **Plan for the weakest downstream agent.** Coder и reviewer слабее
   planner-а; план должен переживать слабого executor-а.
2. **Step Independence** — каждый шаг читается изолированно («как в S2»
   запрещено).
3. **Typed Evidence Floor** — build/run/test/lint/typecheck — явные
   поля, не free-form проза.
4. **Acceptance Taxonomy** — «no regressions» запрещено; разрешены
   таксономированные критерии (`builds:`, `tests_pass:`, `behaviour:`).
5. **Delta Plan для recovery** — `Keep / Invalidate / Replace`, не
   full re-plan.
6. **Bounded HOW разрешено** — planner может указать паттерн / регион
   файла / конкретную команду; запрещены только diff/код в плане.
7. **scope формируется ПОСЛЕ recon** — а не до clarification.

## Где живут финальные артефакты

- **Default prompt:** [`knowledge/prompts/architect-fa-compact.md`](../prompts/architect-fa-compact.md)
- **Full prompt:** [`knowledge/prompts/architect-fa.md`](../prompts/architect-fa.md)
- **Companion notes:** [`agent-roles.md`](./agent-roles.md),
  [`agentic-memory-supplement.md`](./agentic-memory-supplement.md),
  [`ai-context-os-memm-deep-dive.md`](./ai-context-os-memm-deep-dive.md).

## Full pre-trim text

`git show cf7db4d:knowledge/research/architect-fa-refactor-ru.md` — 451
lines, last full revision 2026-05-08. Contains: full §1 (context),
§2 (12-point diagnosis of v1.0), §3 (GPT-5.5 refactor strengths and
weaknesses), §4 (10 design principles), §5 (5-field step contract +
acceptance taxonomy + delta plan recovery details), §6 (11-point
pre-output self-check), §7 (open items), §8 (verification claims).
