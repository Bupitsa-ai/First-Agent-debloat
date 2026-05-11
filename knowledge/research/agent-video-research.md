# Research — Agent Patterns from 5 YouTube Videos

> **Status:** superseded by [`research/efficient-llm-agent-harness-2026-05.md`](./efficient-llm-agent-harness-2026-05.md) (archived 2026-05-08; body trimmed 2026-05-11 per PR-M).
>
> Excluded from `knowledge/llms.txt §BY-DEMAND-INDEX` for the OSS-agent routing surface. Vocabulary and concept map predate ADR-1..ADR-6; live ADR-7 (inner-loop / tool-contract) input is now the harness research note.
>
> **Body trimmed in PR-M to a TL;DR abstract; full pre-trim text in git history at commit `cf7db4d`** (`git show cf7db4d:knowledge/research/agent-video-research.md`).

> **Status:** draft research note, 2026-04-23. Synthesizes transcripts of
> five YouTube videos into a concept map + ranking + concrete
> recommendations for First-Agent.

## 0. TL;DR — five cross-cutting theses

1. **Deterministic shell, probabilistic core.** LLM is the kernel, not
   the system. The system around it must be deterministic: fixed loop,
   strict steps, verifiable contracts between steps. Non-determinism is
   allowed only inside the «which tool to call» choice, not in the loop
   shape.
2. **Andrew Ng's four composable patterns:** *tool use → RAG → planning →
   multi-agent*. Every working agent is a composition. Start with tool
   use; add layers only as needed.
3. **Memory is seven levels, not one.** Automemory → `CLAUDE.md` → state
   files → Obsidian vault → naive RAG → graph RAG → agentic multimodal
   RAG. Level 7 is overkill for most; v0.1 targets 3-4.
4. **Harness engineering** (rules + commands + hooks + skills) is a
   first-class artefact. It defines *what* the agent must do, *how*, and
   *what must never happen*. Without an explicit harness, the agent is
   «smart but unmanageable».
5. **Self-improving loop is feasible today** (Hermes pattern):
   auto-retrospective → extract skill after 3+ successful executions →
   persist → refresh.

## Where the content lives now

- **Active superseder:** [`research/efficient-llm-agent-harness-2026-05.md`](./efficient-llm-agent-harness-2026-05.md)
  — single source of truth for harness research, ADR-7 prep.
- **Memory architecture decision:** [ADR-3](../adr/ADR-3-memory-architecture-variant.md)
  (v0.1 Mechanical Wiki — filesystem-canonical Markdown + SQLite FTS5).
- **Storage backend:** [ADR-4](../adr/ADR-4-storage-backend.md).
- **Project pillars** (incl. «harness engineering as Pillar 3»):
  [`project-overview.md` §1.1](../project-overview.md#11-четыре-столпа-цели-project-goal--four-pillars).
- **Skill-as-procedural-memory commitment:** Pillar 4 + TBD ADR-8.

## Videos analysed

| # | Title | Channel | Length |
|---|---|---|---|
| V1 | Hermes Agent: Self-Improving Strategy Explained | Devs Kingdom | 18:34 |
| V2 | The 7 Levels of Claude Code & RAG | Chase AI | 45:58 |
| V3 | 4 Design Patterns Behind Every AI Agent | TechWhistle | 09:38 |
| V4 | The Core of an AI Agent Is Determinism | personal vlog | ~07:30 |
| V5 | Claude Design & the Harness Question | personal vlog | ~07:30 |

## Full pre-trim text

`git show cf7db4d:knowledge/research/agent-video-research.md` — 330
lines, last full revision 2026-05-08. Contains: full concept catalogue
(§2: architectural patterns; 7-level memory table; Hermes reference; 4
harness primitives; secondary ideas like AskUserQuestion / rerankers /
RAG-vs-textual cost), ranking matrix (§3), implications mapping for
`docs/architecture.md` (§5), full source links + transcripts.
