# Backlog — deferred ideas with unblock triggers

> **Purpose.** Single canonical list of architectural ideas
> deferred from Stage 1 (Devin-driven, per
> [`project-overview.md` §1.3](./project-overview.md#13-three-stage-project-evolution)).
> Without this, deferred ideas get lost between sessions. Each
> entry has an **unblock-trigger** — the concrete artefact whose
> existence moves the idea from «deferred» to «actionable».
>
> **Maintenance rule.** When an idea becomes actionable, move it
> from this file into the next PR's scope; do not delete it
> silently — leave a one-line «landed in PR #N» marker so the
> session that originally deferred it can be audited. When a new
> idea is deferred (rather than rejected) during a session,
> append it here in the same session it was proposed.
>
> **Distinction from [`HANDOFF.md`](../HANDOFF.md) §Current state.**
> HANDOFF tracks items **in flight right now**; BACKLOG tracks
> items **deferred with an unblock-trigger**. Different scopes,
> different cadences, do not merge.

## I-1 — Planner picks needed skills / tool-calls at planning stage

- **Status:** deferred from Stage 1 (proposed 2026-05-08 chat).
- **Idea:** The lower-tier Coder LLM should not see all ~20 tool
  specs in every call. The Planner pre-selects the relevant 3-5
  for the current task; the tool registry shape allows lazy load
  so unused specs never enter Coder context.
- **Blocked-on:** ADR-7 (tool registry contract — currently in
  ADR-7 prep per
  [`research/efficient-llm-agent-harness-2026-05.md`](./research/efficient-llm-agent-harness-2026-05.md)
  §10 ToolSpec sketch). Without a registry, there is nothing to
  pick from.
- **Unblock-trigger:** ADR-7 merges **and** `src/fa/tool_registry/`
  module lands with a `ToolSpec` dataclass plus loader.
- **First concrete step once unblocked:** Extend
  [`knowledge/prompts/architect-fa.md`](./prompts/architect-fa.md)
  Step 2 «Bounded recon» with a tool-selection sub-step; the
  Coder system prompt receives the selected subset, not the full
  registry.
- **Why it satisfies rule #11 mitigation (b) «Lazy-load».** This
  idea is the lazy-load primitive
  [AGENTS.md rule #11](../AGENTS.md#pr-checklist) explicitly
  references when a harness component pushes past ~100 k tokens.

## I-2 — Agent + sub-agents for context-load reduction

- **Status:** deferred from Stage 1 (proposed 2026-05-08 chat).
- **Idea:** A parent orchestrator spawns child sub-agents for
  isolatable sub-tasks (research fan-out across multiple sources,
  parallel chunker over multiple inbox files, parallel test
  runs); the parent merges results. The parent's context stays
  bounded because the big-context work happens inside child
  contexts that vanish after returning their summary.
- **Blocked-on:** Phase-M `src/fa/` runner (child-process
  plumbing, sandbox propagation per
  [ADR-6](./adr/ADR-6-tool-sandbox-allow-list.md), merge
  protocol). No runner exists yet — only the chunker scaffolding
  under `src/fa/chunker/`.
- **Unblock-trigger:** UC1 end-to-end demo working **and** the
  first Phase-M PR lands a runner with a child-spawn primitive.
- **First concrete step once unblocked:** A small ADR (Pre-ADR-9?)
  scoping the sub-agent boundary — same model tier? Per-sub-agent
  audit log? Whose `sandbox.toml` applies, parent or child?
- **Prior art:** archived `research/agent-video-research.md` §12
  (deferred Mem0-style workspace), archived
  `research/llm-wiki-community-batch-2.md` (Whisper +
  Claude-subagents pattern — rejected for v0.1 single-user
  scope; revisit when UC4 returns).
- **Why it satisfies rule #11 mitigation (a) «Sub-agent split».**
  This is the canonical instance of the sub-agent split rule #11
  references; until I-2 lands, mitigation (a) is hypothetical.

## I-3 — Dispatcher LLM (lazy-load skills + collect repo parts on-the-fly)

- **Status:** deferred from Stage 1 (proposed 2026-05-08 chat).
- **Idea:** A small dispatcher LLM (between session-start router
  and main Coder) collects relevant repo parts on-the-fly and
  injects them into the main Coder context. Lazy-load skills
  (`~/.fa/skills/` or `knowledge/skills/`) and lazy-load research
  notes; cache invariants per
  [`research/efficient-llm-agent-harness-2026-05.md`](./research/efficient-llm-agent-harness-2026-05.md)
  R-8 static-layered-prompt finding.
- **Blocked-on:** Same as I-1 (tool registry exists) **plus** the
  skills system (ADR-8 TBD per
  [`project-overview.md` §1.1](./project-overview.md#11-четыре-столпа-цели-project-goal--four-pillars)
  Pillar 4 «iteration via measurement» — agent writes its own
  `SKILL.md`).
- **Unblock-trigger:** ADR-7 **and** ADR-8 both merged;
  `~/.fa/skills/` or `knowledge/skills/` directory exists with a
  loader contract.
- **First concrete step once unblocked:** Extend
  [`knowledge/prompts/RESOLVER.md`](./prompts/RESOLVER.md) from
  static intent table to a prompt-callable dispatcher; the
  current T1..T5 rows remain as the fallback table when the
  dispatcher cannot route.
- **Collapses with I-1.** Both need ADR-7 + ADR-8 first; the
  «lazy-load» framing is the key delta over today's static
  RESOLVER.md. Open question for ADR-8: do I-1 and I-3 ship as
  one component or two?

## See also

- [`knowledge/MAINTENANCE.md`](./MAINTENANCE.md) — recurring
  sweeps + cross-reference cascade rules; companion to this file.
- [`HANDOFF.md`](../HANDOFF.md) §Current state — for items
  actively in flight (not deferred).
- [`AGENTS.md` PR Checklist rule #11](../AGENTS.md#pr-checklist)
  — mitigations (a) and (b) reference I-2 and I-1/I-3
  respectively; the rule's «tracked in BACKLOG.md until ADR-7/8
  lands» wording points here.
