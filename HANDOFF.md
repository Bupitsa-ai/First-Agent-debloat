# HANDOFF.md — for the next agent / session

> **Read this first if you are an LLM agent (Devin, Claude, ChatGPT,
> Cursor, etc.) starting a new session on this repository.**
>
> **Last updated:** 2026-05-08 by Devin session
> [`f6d329f2152544fdbb0204e78660d7d0`](https://app.devin.ai/sessions/f6d329f2152544fdbb0204e78660d7d0)
> (`llms.txt` rewrite + `HANDOFF.md` trim; previous bump
> [`12fcdba610dd474480b1c03906a8e304`](https://app.devin.ai/sessions/12fcdba610dd474480b1c03906a8e304)
> 2026-05-07).

This file is a portable counterpart to the Devin Knowledge note
"First-Agent — current state pointer". Both contain the same
information. The Knowledge note auto-injects into Devin sessions;
this Markdown file exists for any LLM client that does not have
Devin's MCP context (Claude Code, Cursor, ChatGPT with repo
access, plain `git clone`).

If both disagree, the Devin Knowledge note is canonical (it gets
updated more often). When you finish a session that materially
changes the project state, update **both**.

## 60-second bootstrap

1. Read [`AGENTS.md`](./AGENTS.md) — repo conventions, PR
   checklist, query routing.
2. Read [`knowledge/llms.txt`](./knowledge/llms.txt) — one-fetch
   index of every documentation file in this repo
   ([llmstxt.org](https://llmstxt.org/) convention).
3. Skim [`knowledge/project-overview.md`](./knowledge/project-overview.md)
   — what the project is, what v0.1 ships, what is non-goal.
4. Skim the ADR index at
   [`knowledge/adr/README.md`](./knowledge/adr/README.md) — the
   six accepted decisions that shape v0.1 (ADR-1..6).
5. Check the **Current state** section below for what is in
   flight right now.

You should now have everything you need. Do not crawl the repo
manually beyond this point.

## Current state (as of 2026-05-08)

- **Stage:** Phase S scaffolding complete; design layer
  consolidating before first feature-module PR (Phase M).
  Minimal `src/fa` package exists only as a CLI smoke
  entrypoint. No feature module is implemented yet.
- **Working repo:** primary work happens in fork
  [`GrasshopperBoy/First-Agent-fork`](https://github.com/GrasshopperBoy/First-Agent-fork)
  (Devin-app installation lives there). Cross-fork PRs to upstream
  `GITcrassuskey-shop/First-Agent` are opened by the project lead
  via `Contribute → Open pull request` on the fork page when each
  fork PR is ready to land. Both repos' `main` are kept in sync
  by the lead.
- **Architecture decisions (accepted, on `main` of both fork and upstream):**
  - [ADR-1](./knowledge/adr/ADR-1-v01-use-case-scope.md) — UC1
    (coding + PR write) and UC3 (local-docs-to-wiki) in;
    UC4 deferred; UC2 best-effort. **Amendment 2026-05-01:** UC5
    (semi-autonomous multi-LLM research/experiment) explicitly
    deferred.
  - [ADR-2](./knowledge/adr/ADR-2-llm-tiering.md) — static role
    routing (Planner / Coder / Debug / Eval) via
    `~/.fa/models.yaml`. **Amendment 2026-04-29:**
    `tool_protocol: native | prompt-only` field per role; v0.1
    inner-loop without Critic. **Amendment 2026-05-01:** MCP
    forward-compat tool-shape convention (JSON-RPC-shaped
    `name`/`params`/`result`/`error` for all tool dispatch).
  - [ADR-3](./knowledge/adr/ADR-3-memory-architecture-variant.md) —
    Variant A (mechanical wiki, no embeddings, no graph,
    no Mem0).
  - [ADR-4](./knowledge/adr/ADR-4-storage-backend.md) — SQLite
    FTS5 (external-content mode); zero extra deps. **Amendment
    2026-04-29:** chunks schema extended with provenance fields
    (`parent_title`, `breadcrumb`, `byte_start/end`, `topic`).
  - [ADR-5](./knowledge/adr/ADR-5-chunker-tool.md) —
    universal-ctags + markdown-it-py. **Amendment 2026-04-29:**
    `Chunk` dataclass extended to match ADR-4 provenance fields.
  - [ADR-6](./knowledge/adr/ADR-6-tool-sandbox-allow-list.md) —
    Tool sandbox + path allow-list policy (deny-by-default,
    `~/.fa/sandbox.toml`, gitignore-style globs, audit log at
    `~/.fa/state/sandbox.jsonl`, one-shot CLI bypass).
- **ADR slot reservation.** ADR-7 is reserved for the future
  Inner-loop ADR (cross-reference §10 R-1, not yet drafted).
  See `cross-reference-…-2026-04.md` §11 supersession marks
  on Q-1 / Q-2 for history.
- **Scaffolding:** `pyproject.toml`, Ruff, mypy, pytest,
  pre-commit, GitHub Actions CI, `Makefile`, `markdown-it-py`,
  and system dependency documentation for `universal-ctags`
  are in place. CI in fork is limited to Devin Review (GitHub
  Actions are configured upstream; in the fork they are
  effectively no-op).
- **Open PRs in fork.** Check
  <https://github.com/GrasshopperBoy/First-Agent-fork/pulls>.
- **Research notes that close design questions:**
  - [`research/memory-architecture-design-2026-04-26.md`](./knowledge/research/memory-architecture-design-2026-04-26.md)
    — three variants for memory (input to ADR-3).
  - [`research/chunker-design.md`](./knowledge/research/chunker-design.md)
    — five tool classes, coverage matrix, ten open questions
    (input to ADR-5).
- **Research notes (no ADR yet; inputs for v0.1+ implementation,
  v0.2 roadmap, ADR-7 prep).** One-liner each; full descriptions
  live in `knowledge/llms.txt §BY-DEMAND INDEX` (open the file for
  the per-row tag and goal-lens prefix). The one note that already
  ships a contract is called out at the end.
  - 2026-04-29 —
    [`research/how-to-build-an-agent-ampcode-2026-04.md`](./knowledge/research/how-to-build-an-agent-ampcode-2026-04.md)
    (inner-loop, three-tool baseline; ADR-1 / ADR-2 / UC1) and
    [`research/sliders-structured-reasoning-2026-04.md`](./knowledge/research/sliders-structured-reasoning-2026-04.md)
    (SLIDERS framework; ADR-3 / ADR-4 / ADR-5 + v0.2 extraction-layer).
    The cross-reference note for these two
    (`cross-reference-ampcode-sliders-to-adr-2026-04.md`) was
    archived 2026-05-08; live recommendations live in ADR-2/4/5/6
    amendments and `efficient-llm-agent-harness-2026-05.md §10`.
    Q-1 / Q-2 marked superseded 2026-05-01 (ADR-6 became sandbox;
    future inner-loop = ADR-7).
  - 2026-05-01 —
    [`research/semi-autonomous-agents-cross-reference-2026-05.md`](./knowledge/research/semi-autonomous-agents-cross-reference-2026-05.md)
    (semi-autonomous LLM agents critique; basis for ADR-2 / ADR-1
    Amendment 2026-05-01; ACI / hooks input for ADR-7).
  - 2026-05-03 —
    [`research/cutting-edge-agent-research-radar-2026-05.md`](./knowledge/research/cutting-edge-agent-research-radar-2026-05.md)
    (cutting-edge radar; ADR-7 prep + module PR backlog) and
    [`research/agent-ui-research-radar-v0-2-2026-05.md`](./knowledge/research/agent-ui-research-radar-v0-2-2026-05.md)
    (v0.2 UI/control-plane radar).
  - 2026-05-07 —
    [`research/efficient-llm-agent-harness-2026-05.md`](./knowledge/research/efficient-llm-agent-harness-2026-05.md)
    — **single source-of-truth for ADR-7 prep** (consolidates upstream
    PR #37 + #38, both close-without-merge). §0 Decision Briefing:
    R-1..R-9 (8 TAKE + 1 DEFER, 0 surviving UNCERTAIN-ASK). §10 ships
    the ADR-7 contract sketch — ToolSpec / ToolResult / Trace +
    static-layered prompt-assembly invariant + subtraction-first
    acceptance-block. Tagged `[CONTRACT]` `[BRIEFING]` in `llms.txt`.

## Next steps (intended order)

1. **ADR-7 — Inner-loop + tool-registry contract** (cross-reference
   §10 R-1, future). Should pin: tool-registry contract,
   tool-call audit log shape, edit-format (string-replace vs
   unified-diff), input JSON-Schema validation, **MCP-shaped
   request/response per ADR-2 amendment 2026-05-01**, and a
   minimal **hook pipeline** primitive (pre-tool / post-tool;
   pre-run / post-run / on-event deferred to v0.2). Inputs:
   - [`research/efficient-llm-agent-harness-2026-05.md`](./knowledge/research/efficient-llm-agent-harness-2026-05.md)
     — single source-of-truth для harness research под ADR-7 prep
     (R-1..R-9 resolved + §10 ADR-7 contract sketch).
   - cross-reference §10 R-1 / R-3 / R-7.
   - semi-autonomous-agents cross-reference §7.1 (R-1 input
     summary), §7.3 (edit-format two shapes), §8.4 (large-file
     two-stage read), §8.5 (mini-hook-system rationale).
2. **Implementation PR — chunker.** Implement `src/fa/chunker/`
   with the `Chunk` dataclass and `Chunker` Protocol from
   [ADR-5 §Decision](./knowledge/adr/ADR-5-chunker-tool.md#decision)
   (now including provenance fields per 2026-04-29 amendment).
   **Hard gate:** the four sample-tests in
   [`research/chunker-design.md` §8](./knowledge/research/chunker-design.md#8-sample-test-plan-pre-implementation)
   must pass — including the PowerShell sanity-check on the
   project lead's real 1500-line `.ps1` (not synthetic). The
   project lead should provide the real `.ps1` and a
   representative Go sample before this PR is considered
   mergeable.
3. **Chunker CLI surface.** Add `fa chunk <path>` for manual
   inspection of produced chunks as part of the chunker PR.
4. **R-3 edit-format fixture.** Run a 5-10 string-replace +
   5-10 unified-diff `apply_patch` test set on each
   tool-using model from ADR-2 (Qwen 3.6, Kimi 2.6, GLM 5.1,
   Claude latest, Nemotron 3 Super). Empirically verify that
   each model handles both edit-shapes; the result pins
   default edit-format in ADR-7. Optional pre-ADR-7; can be
   parallel.
5. **Glossary** (cross-reference §10 R-8 + semi-autonomous
   note §7.8): add `MCP`, `Hook`, `ACI`,
   `Reflexion / Critic / Reflector`, `Self-evolving` terms
   to [`docs/glossary.md`](./docs/glossary.md). Optional;
   not blocking ADR-7.
6. **v0.2 UI/control-plane pre-ADR** (optional after ADR-7 prep,
   or before if project lead prioritizes UI): use
   [`research/agent-ui-research-radar-v0-2-2026-05.md`](./knowledge/research/agent-ui-research-radar-v0-2-2026-05.md)
   to decide trace-viewer-first vs live-dashboard-first, local BFF
   shape, event schema, approval UI, and non-goals.

Phase-S item #7 (auto-generated `llms.txt`) is recorded in
[`docs/workflow.md`](./docs/workflow.md) as future work; not
blocking.

## Conventions worth knowing in 5 lines

- **Branch:** `devin/<unix-timestamp>-<slug>` from `main`.
- **Commit trailer:** every LLM-driven commit ends with
  `AI-Session: <session-id>` plus
  `Co-Authored-By: <human> <email>` (codedna pattern).
  Trailers must be in the squash-merge commit body — see PRs #19,
  #21, #23 for examples.
- **PR description:** every changed/new file listed as a clickable
  blob URL on the head branch (rule #6).
- **`knowledge/llms.txt`:** must be updated whenever `docs/` or
  `knowledge/` changes structure (rule #7).
- **Research note language:** notes are read by both humans and
  agents. Prefer Russian analytical prose and recommendations unless
  the project lead asks otherwise; keep protocol names, API fields,
  code, frontmatter keys, and direct quotes in their original language
  where precision matters.
- **Code fences:** always have a language tag (`python`, `yaml`,
  `text`, …); never a bare ` ``` ` opening.

## When to update this file

- After any PR merge that materially changes the project state
  (new ADR, new phase, new module, new big research note).
- Bump the **Last updated** date at the top.
- Also update the matching Devin Knowledge note (or replace it
  entirely with the body of this file — they are meant to be
  identical).
