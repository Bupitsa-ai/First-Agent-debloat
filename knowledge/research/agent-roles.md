---
title: "Research — Agent roles, harness patterns, and routing"
compiled: "2026-04-22"
source:
  - https://github.com/anthropics/claude-cookbooks
  - https://github.com/QwenLM/Qwen-Agent
  - https://www.anthropic.com/research/swe-bench-claude-3-5-sonnet
  - https://blog.langchain.dev/openais-bet-on-a-cognitive-architecture/
  - knowledge/research/llm-wiki-critique.md
  - knowledge/research/llm-wiki-community-batch-1.md
chain_of_custody: >
  Research note, 2026-04-22 (бумерштем). Все цитаты сверены с
  первоисточниками; цифры о бенчмарках — claim авторов, не наш
  measurement.
claims_requiring_verification:
  - "Claude Sonnet ~49% SWE-bench Verified (Anthropic blog 2024-10-22)"
  - "Qwen-Agent local benchmarks numbers (claim from QwenLM/Qwen-Agent README)"
  - "Reflexion +20-30pp on HumanEval (Shinn et al. 2023 arXiv:2303.11366)"
superseded_by: "knowledge/adr/ADR-2-llm-tiering.md"
---

# Research — Agent roles, harness patterns, and routing

> **Status:** superseded by [`adr/ADR-2-llm-tiering.md`](../adr/ADR-2-llm-tiering.md) (archived 2026-05-08; body trimmed 2026-05-11 per PR-M).
>
> Excluded from `knowledge/llms.txt §BY-DEMAND-INDEX` for the OSS-agent routing surface. Role-routing decision shipped in ADR-2 (4-tier model: Planner / Coder / Debug / Eval); cheat-sheet row in [`adr/DIGEST.md`](../adr/DIGEST.md).
>
> **Body trimmed in PR-M to a TL;DR abstract; full pre-trim text in git history at commit `cf7db4d`** (`git show cf7db4d:knowledge/research/agent-roles.md`). Trim rationale: `repo-audit-2026-05-10-revised.md` §4.1.

## 0. TL;DR

Four-bullet summary of the design space for First-Agent's role decomposition:

1. **Static role routing, not dynamic.** A small fixed roster
   (Planner / Coder / Debug / Eval) outperforms LLM-decided routing for
   the small-task domain we target. Cited from Anthropic SWE-Bench
   playbook + Qwen-Agent baseline. **Shipped in [ADR-2](../adr/ADR-2-llm-tiering.md).**
2. **Critic role is non-obvious.** Reflexion-style per-turn critic
   yields large gains on stochastic eval (HumanEval +20-30pp); but on
   deterministic tool-driven loops it duplicates work the tools already
   verify. **First-Agent v0.1: no separate Critic** — tool-feedback +
   end-of-task review covers it. Pushed to v0.2 if measurement shows
   need. **Shipped as ADR-2 §Decision.**
3. **Tool-feedback eats self-reflection** in code-edit tasks. If
   `pytest` exits non-zero the agent re-plans; the Critic adds
   marginal value over that signal. Lit. ref: Ampcode harness note
   (`research/how-to-build-an-agent-ampcode-2026-04.md`).
4. **Procedural memory = skills, not prompts.** A skill is an
   executable artefact (markdown + shell wrapper or Python function),
   versioned in `knowledge/` and dispatched by `RESOLVER.md`-style
   index. Pushed to ADR-8 (open).

## Where the content lives now

- **Active ADR:** [`adr/ADR-2-llm-tiering.md`](../adr/ADR-2-llm-tiering.md)
- **Harness research:** [`research/efficient-llm-agent-harness-2026-05.md`](./efficient-llm-agent-harness-2026-05.md)
- **Memory architecture:** [`adr/ADR-3-memory-architecture-variant.md`](../adr/ADR-3-memory-architecture-variant.md)
- **DIGEST:** [`adr/DIGEST.md`](../adr/DIGEST.md)

## Full pre-trim text

`git show cf7db4d:knowledge/research/agent-roles.md` — 932 lines, last
full revision 2026-05-08. Contains: full role catalogue (§1-§3:
Specifier / Planner / Executor / Critic + secondary roles), §4 routing
patterns (static / dynamic / hybrid), §5 harness primitives (rules /
commands / hooks / skills), §6 anti-patterns (ceremony-heavy plans,
overspecialised roles), §7 Reflexion / MetaGPT / AutoGen comparison
tables, §8 Multi-agent topologies (linear / hub-and-spoke / mesh /
hierarchical), §9 evaluation criteria, §10 First-Agent v0.1 commitments,
all source citations.
