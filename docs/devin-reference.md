# Devin — компактный справочник

> **Status:** superseded by [`docs/README.md`](./README.md) (archived 2026-05-08; body trimmed 2026-05-11 per PR-M).
>
> Excluded from `knowledge/llms.txt §BY-DEMAND-INDEX` for the OSS-agent routing surface — it describes Devin-specific features (Knowledge notes, Skills, Playbooks, MCP marketplace) that the OSS-agent flow (DeepSeek 4 / Kimi 2.6) cannot exercise. Will be restored as a per-host gated entry once `llms.txt` learns to gate by agent host.
>
> **Body trimmed in PR-M to a one-paragraph abstract; full pre-trim text in git history at commit `cf7db4d`** (`git show cf7db4d:docs/devin-reference.md`). Trim rationale: `repo-audit-2026-05-10-revised.md` §4.1 — archived bodies cohabit the grep-surface and create context-poison for mid-tier OSS LLMs (Lost-in-the-Middle, Liu 2023 arXiv:2307.03172).

## Abstract

Russian-language compact reference for Cognition's Devin agent (the
upstream platform that drives this repo's PRs in Stage 1). Covers:

- **Execution surface** — Shell / IDE / Browser (Chrome + CDP) / Desktop
  (X server) / Screen recording / PR tools / CI tools / Secrets / MCP /
  Scheduled sessions / Managed Devins.
- **Memory** — Knowledge notes (long-term, trigger-pinned, auto-injected),
  Skills (`SKILL.md` checked into repo), Playbooks (org-level recipes).
- **When to invoke** — rule of thumb: «if it would take you ≤ 3 hours,
  Devin probably handles it». Three pre-flight questions: clear success
  criteria? enough context? would decomposition help?
- **Devin Review + Auto-Fix** — automatic review responses and CI
  iteration; final approval stays human.
- **MCP integration candidates** for First-Agent — Notion / Linear /
  vector DB / LLM observability tools.

Targeted at human readers and Devin sessions in this repo. The OSS-agent
flow (DeepSeek 4 / Kimi 2.6) cannot exercise Devin-specific features —
hence the routing exclusion.

## Where the content lives now

- General Devin docs: <https://docs.devin.ai>
- Devin Agents 101: <https://devin.ai/agents101>
- This repo's agent instructions: [`AGENTS.md`](../AGENTS.md)
- This repo's bootstrap sequence: [`HANDOFF.md`](../HANDOFF.md) §60-second
  bootstrap

## Full pre-trim text

`git show cf7db4d:docs/devin-reference.md` — 204 lines, last full revision
2026-05-08 (PR-B archive marker pass).
