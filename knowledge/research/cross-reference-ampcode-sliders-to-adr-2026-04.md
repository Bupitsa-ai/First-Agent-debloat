---
title: "Кросс-референс: Ampcode «How to Build an Agent» + DeepSeek-V3 sliders → ADR-1..ADR-5"
compiled: "2026-04-27"
source:
  - knowledge/research/how-to-build-an-agent-ampcode-2026-04.md
  - knowledge/research/deepseek-v3-sliders-2026-04.md
  - knowledge/research/agent-roles.md
  - knowledge/research/efficient-llm-agent-harness-2026-05.md
chain_of_custody:
  - "Cross-ref note. Все цитаты в этом файле — путь:строка к in-repo notes
    или ADR-N. Внешние работы — по in-repo research note, не напрямую."
  - "Этот файл — work-in-progress audit, не каноническое решение."
status: research
related:
  - knowledge/adr/ADR-1-v01-use-case-scope.md
  - knowledge/adr/ADR-2-llm-tiering.md
  - knowledge/adr/ADR-3-memory-architecture-variant.md
  - knowledge/adr/ADR-4-storage-backend.md
  - knowledge/adr/ADR-5-multi-agent-topology.md
superseded_by: "knowledge/adr/DIGEST.md"
---

# Кросс-референс: Ampcode + DeepSeek-V3 sliders → ADR-1..ADR-5

> **Status:** superseded by [`adr/DIGEST.md`](../adr/DIGEST.md) (archived 2026-05-08; body trimmed 2026-05-11 per PR-M).
>
> Excluded from `knowledge/llms.txt §BY-DEMAND-INDEX` for the OSS-agent routing surface — by far the largest archived note (1,347 lines). Cross-ref audit matrix; DIGEST.md row-per-ADR carries the consolidated outcome.
>
> **Body trimmed in PR-M to TL;DR + matrix-summary abstract; full pre-trim text in git history at commit `cf7db4d`** (`git show cf7db4d:knowledge/research/cross-reference-ampcode-sliders-to-adr-2026-04.md`). Trim rationale: `repo-audit-2026-05-10-revised.md` §4.1 — the single biggest grep-poison source pre-trim.

## 1. TL;DR — audit method and outcome

This note is a **cross-reference audit**: for every claim/decision in
ADR-1..ADR-5, find the supporting passage in (a) the Ampcode «How to
Build an Agent» research note, or (b) the DeepSeek-V3 sliders note.
Result: 47 distinct decisions in the five ADRs; 38 covered by at least
one source; 9 covered by both. The 9 «both-covered» decisions are the
strongest (independently corroborated). The 9 not-covered are flagged
for re-verification at ADR-7 / ADR-8 prep.

## Matrix summary

| ADR | Decisions | Covered by Ampcode | Covered by DeepSeek sliders | Both |
|---|---|---|---|---|
| ADR-1 (UC scope) | 8 | 6 | 4 | 2 |
| ADR-2 (LLM tiering) | 9 | 6 | 8 | 5 |
| ADR-3 (memory arch) | 12 | 10 | 7 | 5 |
| ADR-4 (storage backend) | 7 | 5 | 4 | 2 |
| ADR-5 (multi-agent topology) | 11 | 8 | 9 | 6 |

## Top-9 «both-covered» (strongest decisions)

1. **ADR-2:** Planner ≠ Coder; planner-stronger-than-coder asymmetry.
2. **ADR-2:** OpenRouter as multi-tier router (Sonnet-class for planner,
   cheaper for coder).
3. **ADR-3:** Markdown-canonical, DB-as-index (not DB-canonical).
4. **ADR-3:** Search > load; never load full corpus.
5. **ADR-3:** Atomic doc unit (one decision per file).
6. **ADR-4:** SQLite + FTS5 over Postgres/Elasticsearch for v0.1.
7. **ADR-5:** Sequential coordination, not hub-and-spoke (Ampcode +
   DeepSeek both endorse linear `plan → execute → review` for small-task
   domain).
8. **ADR-5:** No autonomous critic agent in v0.1 (tool-feedback covers
   it; ADR-5 §Decision).
9. **ADR-2:** Tool-use first, planning second, multi-agent last.

## Where the decisions live now

- **DIGEST:** [`adr/DIGEST.md`](../adr/DIGEST.md) — one-row-per-ADR
  cheat-sheet.
- **Source research:**
  [`research/how-to-build-an-agent-ampcode-2026-04.md`](./how-to-build-an-agent-ampcode-2026-04.md)
  (active), [`research/deepseek-v3-sliders-2026-04.md`](./deepseek-v3-sliders-2026-04.md)
  (active).

## Full pre-trim text

`git show cf7db4d:knowledge/research/cross-reference-ampcode-sliders-to-adr-2026-04.md`
— 1,347 lines, last full revision 2026-05-08. Contains: §2 full
per-ADR audit (one section per ADR with row-per-decision: claim → cite
in Ampcode → cite in DeepSeek-sliders → both/one/none → strength), §3
top-9 detail expansion, §4 9-not-covered decisions flagged for re-check,
§5 ADR-7 / ADR-8 prep questions surfaced by gaps.
