# AGENTS.md

Instructions for AI agents (Devin and similar) working in this repo.

## Project Overview

**First-Agent** — research-backed implementation-first LLM agent project,
aimed at becoming the most token/tool-call efficient open-source
coding-agent harness under UC1+UC3 single-user scope. Stage:
`research → start of module creation`. No code in `src/` yet.
Goal-formulation in 4 pillars + minimalism-first principle:
[`knowledge/project-overview.md` §1.1](./knowledge/project-overview.md).
README intro: [`README.md`](./README.md).

## Repository Structure

- [`README.md`](./README.md) — project overview.
- [`AGENTS.md`](./AGENTS.md) — this file.
- [`docs/`](./docs/README.md) — wiki (architecture, workflow,
  prompting, devin-reference, glossary, agent creation tutorial).
- [`knowledge/`](./knowledge/README.md) — durable memory (project-overview, ADR, prompts, research).

## Pre-flight checklist

Run BEFORE making any edits, opening a branch, or writing analysis on
non-trivial tasks. Output is cheap; skipping is the failure mode.

```bash
# 1. Recency surface. For any new 2026-MM-DD research note in the
#    output, skim its §0 Decision Briefing.
git log -n 5 --since="7 days" --oneline -- knowledge/ docs/ AGENTS.md

# 2. Term expansion. Run once per project-specific noun in the prompt
#    (axis, lens, pillar, harness, UC*, ...). Fall back to
#    project-overview.md §1.1-§1.2 if the glossary row is missing.
grep -i "^| \*\*<term>\*\*" docs/glossary.md

# 3. Symmetric reading. Before citing a research note as evidence,
#    read every other note that mentions the same key term.
grep -ril "<key-term>" knowledge/research/
```

Then state in your analysis (not silently): (a) inferred `goal_lens`,
(b) project-axes advanced (A noise / B context / C goal_lens),
(c) subtraction evaluated — *would removing this artefact / rule /
field instead achieve the same goal?* If not, why not.

## Working in This Repo

- **Session bootstrap.** At the start of any new agent session, fetch
  [`knowledge/llms.txt`](./knowledge/llms.txt) first. It is the
  project map for LLM agents. files are reachable from there in
  one hop. Do not crawl the repo before reading this file.
- All documentation is Markdown. ATX headings (`#`, `##`), short lines ~150 chars.
- Fenced code blocks
  - ALWAYS open with a language tag:
    - Code: `python`, `yaml`, `json`, `bash`.
    - Non-code (ASCII art, directory trees, prompts, logs): `text`.
  -Close with bare ` ``` `.
- New docs go in the right folder:
  - Guides / references → `docs/`. Update [`docs/README.md`](./docs/README.md).
  - Project artifacts (decisions, research, prompts) → `knowledge/`.
- Research notes are read by both humans and agents. Prefer Russian for
  analytical prose, project recommendations. Keep protocol names, API field names, code,
  and direct quotes in their source language.
- readability > size
- Architectural decisions → ADR from [`knowledge/adr/ADR-template.md`](./knowledge/adr/ADR-template.md).

## PR Checklist

Verify before opening a PR. Each item has triggered wasted review cycles.

1. **Code fences have language tags.** No bare ` ``` ` at opening! See rule above.
2. **Frontmatter uses `compiled:`, not `date:`.** Schema: [`knowledge/README.md`](./knowledge/README.md#conventions).
3. **File length within tier limits.**
   - Summaries / overviews: **<1000 lines**.
   - Deep-dive research: **<2000 lines**.
   - Readability > size
4. **`compiled:` date ≥ all dates cited in text.** No temporal impossibilities.
5. **Supersession, not overwrite.** Mark old file `> **Status:** superseded by <link>`. Keep for audit.
6. **PR description lists changed/new files as clickable blob-URLs**
   (`https://github.com/<owner>/<repo>/blob/<branch>/<path>`), at
   least for non-trivial files. Plain bullet text is insufficient —
   reviewers should be able to open each file in one click without
   copy-pasting paths. Use the head branch of the PR, not `main`.
7. **`knowledge/llms.txt` reflects reality.** If this PR adds,
   removes, or renames any file under `docs/` or `knowledge/`, update
   the corresponding entry (or add / remove a row) in
   [`knowledge/llms.txt`](./knowledge/llms.txt). The index is
   hand-maintained; it drifts silently if not enforced on every PR.
   A pre-commit hook or generator can be added later (see
   [`docs/workflow.md`](./docs/workflow.md) Phase S).
8. **Research notes from the research-briefing workflow start with §0
   Decision Briefing.** Notes under `knowledge/research/` produced via
   [`knowledge/prompts/research-briefing.md`](./knowledge/prompts/research-briefing.md)
   MUST place a `## 0. Decision Briefing` section as the first
   section after the frontmatter (before TL;DR / Scope). Each
   recommendation in §0 follows the eight-field format (What /
   Project-axis fit (A, B) / Goal-lens fit (C) / Cost / Verdict / If
   UNCERTAIN-ASK / Alternative-if-rejected / Concrete first step).
   Axes (A) "reduces session-start noise" and (B) "helps LLM find
   context" are stable project-axis criteria evaluated identically
   for every note; axis (C) "advances chosen goal_lens" is the only
   per-session axis and references the goal_lens elicited in Stage 1.
   §0 closes with a 7-column summary table (R-N / Verdict /
   Project-fit / Goal-fit / Cost / Alternative-if-rejected / User
   decision needed?). Frontmatter MUST include a `goal_lens:` field
   capturing the one-sentence research goal elicited at session
   start. The agent also posts §0 verbatim in chat after handover.
   This rule applies to **new** notes with `compiled: ≥ 2026-05-04`;
   older notes are exempted and not retro-fitted.
9. **New ADR PRs add at least one node to the exploration DAG and
   a DIGEST.md row.** Any PR that introduces or amends an accepted
   ADR MUST also add at least one node to
   [`knowledge/trace/exploration_tree.yaml`](./knowledge/trace/exploration_tree.yaml).
   The shape: one `question` node per new ADR, one `decision` child
   for the chosen option (with `chosen: true`), and one `dead_end`
   child per rejected option carrying `reason:` (why rejected at
   decision time) + `lesson:` (what new evidence would re-open the
   branch). Amendments append a follow-up `decision` or `pivot` node
   referencing the original question via `also_depends_on:`. Schema
   reference: [`knowledge/README.md` §`trace/`](./knowledge/README.md#trace--exploration-dag).
   Rationale: the DAG is the cheap-read overlay agents use to
   understand *why* alternatives were rejected without re-reading
   every ADR end-to-end (origin: research note
   [`ara-protocol-cross-reference-2026-05.md`](./knowledge/research/ara-protocol-cross-reference-2026-05.md)
   §9 R-1). **In the same PR**, also update
   [`knowledge/adr/DIGEST.md`](./knowledge/adr/DIGEST.md) — add a
   one-paragraph row for a new ADR or extend the **Amendments**
   bullet of the matching ADR's row. DIGEST.md is the agent-reading
   cheat-sheet (one paragraph per ADR ≈ 80 lines for all six);
   stale rows defeat the purpose.
10. **Harness-component PRs cite minimalism-first evidence.** PRs
    that introduce or amend a harness component (tool, prompt-layer,
    retrieval-stage, executor, sandbox-rule) MUST include in the
    description **explicit answers** to the 3-question minimalism-first
    test from
    [`knowledge/project-overview.md` §1.2](./knowledge/project-overview.md#12-enforceable-principle--minimalism-first):

    1. Research-evidence supporting the component's necessity under
       UC1+UC3 single-user scope (paper / primary-source post /
       eval-report citation).
    2. Open-source agent-stack precedent that **already** removed or
       did not add a similar component, and the observed result.
    3. Concrete capability lost if the component is omitted, and
       whether it can be replaced by an existing tool or config
       setting.

    After UC5 landing, KPI-delta on a reproducible benchmark replaces
    the 3-question test for harness components measurably evaluated.
    Documentation-only or non-harness PRs (research notes, README
    updates, lint fixes) are exempted. This rule applies to **new**
    PRs from the merge of this PR forward; older PRs are not
    retro-fitted.
11. **Context budget for any single LLM call is ≤ 100 k tokens in
    ≥ 90 % of cases.** When designing or amending a harness component
    that issues an LLM call (prompt-layer, retrieval-stage, role
    invocation, sub-agent), the request shape MUST keep input context
    under ~100 k tokens for at least 9 out of 10 invocations in the
    component's expected workload. Justification: First-Agent's
    Pillar-1 target is the **lower-tier OSS LLM** (Planner / Coder /
    Eval tiers per [ADR-2](./knowledge/adr/ADR-2-llm-tiering.md)),
    whose effective context window degrades sharply past ~100 k input
    tokens — accuracy drops, latency jumps, cost grows super-linearly.
    Elite-tier Debug (Claude) is exempt, but routine calls do not run
    on Debug.
    **What «context» counts.** System prompt + role prompt + injected
    tool definitions + retrieved chunks + scrollback / conversation
    history + any in-line memory the harness paste in.
    **What this rule forces at design time.** If a component's natural
    shape pushes a single call past ~100 k for a non-edge-case
    workload, the design MUST adopt **at least one** mitigation
    before merge:
    a. **Sub-agent split** — delegate the big-context work to a
       sub-agent so the parent context stays bounded (Phase-M
       runner; rationale tracked in `BACKLOG.md` until ADR-7 lands).
    b. **Lazy-load** — load skills / tool-specs / repo chunks on
       demand instead of injecting upfront (dispatcher pattern;
       tracked in `BACKLOG.md` until ADR-7 + ADR-8 land).
    c. **Step-as-function** — replace the LLM call with a
       deterministic Python function where the step does not need an
       LLM (see rule #10 question 4).
    d. **Explicit elite-tier escalation** — route the call to elite
       tier *with* a written justification in the PR description
       (treat «route to elite» as a last resort, not a default).
    The PR description for such a component MUST state which
    mitigation it adopts and cite expected p90 input-token shape.
    Documentation-only PRs and non-harness PRs are exempt. Forward-
    only from the merge of this rule; older harness PRs not
    retro-fitted.

## PR Description Style

PR descriptions are the *first reading-pass* for both human review
and LLM agents loading repo context. They should be readable
end-to-end (no bullet-soup), and they should be cheap to parse for
the same agents that wrote them.

**Language split:**

- **Default to Russian** for analytical prose, rationale, scope
  discussion, retro-fit notes — this matches the convention already
  in force for research notes
  ([`knowledge/README.md` §Conventions](./knowledge/README.md#conventions))
  and keeps the human-review path natural.
- **Keep in English** any *identifier* whose precision matters for
  later grep / cross-reference: file paths, frontmatter keys
  (`compiled:`, `goal_lens:`), AGENTS.md rule references
  («PR Checklist rule #N»), full PR titles when referencing other
  PRs (e.g. «PR #16 *docs: add research-briefing workflow…*»), code
  blocks, schema examples, verdict tokens (`TAKE` / `SKIP` /
  `DEFER` / `UNCERTAIN-ASK`).

**Recommended structure:**

One-paragraph what+why opening — Russian prose, what ships +
motivating problem. No bullets here.
Files (clickable blob-URLs) per
PR Checklist rule #6.
Design-rationale prose for any non-obvious choice — flowing
paragraphs, not bullets, when explanation > 3 lines.
Scope / ordering / retro-fit — short list (≤5 items)
flagging merge-order, deferrals, forward-only clauses.
Review & Testing Checklist for Human — GitHub PR template
block; Russian for action items, English for technical referents.
Notes — Russian; mention follow-up PRs and any session-
continuity context. AI-Session trailer is appended automatically.

**Execution Rules:**

Develop complex lists into prose: If a sequence exceeds 5 items, requires 2-3 lines per item,
write cohesive Russian paragraphs.
Reserve bullet points strictly for short, scannable lists.
Synthesize the commit history: Write a fresh, high-level summary and explicitly reference the commit SHA.
Treat the PR body as an independent overview rather than a verbatim copy of the commit log.
Only reference identifiers (like PRs or issues) that already exist and resolve perfectly at read-time.
Inline review comments / replies follow the same language split:
Russian prose for the response; keep the cited identifier (file
path / line / suggestion code-block) in English.

**Canonical examples:**

- [PR #17 *docs: add knowledge/trace/exploration_tree.yaml backfilling ADR-1..6 (R-1)*](https://github.com/GrasshopperBoy/First-Agent-fork/pull/17)
  — DAG backfill PR; description retro-rewritten in this style as a
  demonstration before this convention merged.
- [PR #18 *docs(AGENTS): add §PR Description Style — Russian prose +
  English identifiers*](https://github.com/GrasshopperBoy/First-Agent-fork/pull/18)
  — this PR; self-demonstrating description.

## Development Workflow

- Branch: `devin/<timestamp>-<slug>` from `main`.
- All changes via Pull Request.
- Commit messages: descriptive, English, present tense (`docs: add architecture note`).
- Never push directly to `main`.
- **`AI-Session:` git trailer.** When a commit is driven by a Devin
  (or other LLM-agent) session, add an `AI-Session: <session-id>`
  trailer to the commit message. This preserves the link from a
  squash-merged commit back to the originating session for audit and
  re-entry. Pattern lifted from `codedna` (see
  [`research/agentic-memory-supplement.md` §3](./knowledge/research/agentic-memory-supplement.md)).
  Example:

  ```text
  docs: add ADR-N on <topic>

  Body...

  AI-Session: 2f45f66ef9ff45eab03161ecef165c0e
  Co-Authored-By: <human> <email>
  ```

## Query Routing

Route questions to the right folder. Do not load everything into context.

| Question type | Look first | Verify with |
|---|---|---|
| Architecture, patterns | [`docs/architecture.md`](./docs/architecture.md) | ADR |
| Decisions and rationale | [`knowledge/adr/`](./knowledge/adr/) | — |
| Workflow, Devin usage | `docs/workflow.md`, `docs/devin-reference.md` | — |
| Research findings | [`knowledge/research/`](./knowledge/research/) | Primary sources from `source:` frontmatter |
| Specific number / date / quote | **Primary source** (URL / code / gist), not a summary note | — |
| Terms | [`docs/glossary.md`](./docs/glossary.md) | — |

**Chain-of-custody rule.** If citing a specific number, date, name,
or decision — go to the primary source and quote from there.
Summaries in `knowledge/research/` are pointers, not authoritative
sources.
Rationale: [`knowledge/research/llm-wiki-critique.md`](./knowledge/research/llm-wiki-critique.md).

**Supersession, not overwrite.** Never silently overwrite an outdated
note. Mark it `> **Status:** superseded by <link>` and keep for audit.
