# 01 — When to Use Devin

> Source: <https://docs.devin.ai/essential-guidelines/when-to-use-devin>.
> This is a condensed, project‑specific restatement. Read the original if anything here is ambiguous.

## TL;DR

Devin handles most medium/hard engineering tasks. **The clearer and more specific your
instructions, the higher the success rate.** Rule of thumb: *if the task would take
you ≤ 3 hours, Devin can most likely do it*. Longer → split into sessions.

## Best‑practice checklist for First-Agent

- [ ] **Scope with Ask Devin first.** Let it search the repo and draft a high-context prompt before you spend a full session.
- [ ] **Run Devins in parallel** when subtasks are independent (e.g. prompt-engineering on module A while evaluating embeddings for module B).
- [ ] **Tag Devin on Slack/Teams** for quick "could we try X?" asks instead of breaking flow.
- [ ] **Enable Devin Review + Auto-Fix** so review comments and CI failures close themselves.
- [ ] **Extend reach with MCP** — connect Notion/Linear/Datadog/DB once and every session benefits.
- [ ] **Let Devin test its own work** — full shell + browser + IDE on its VM.
- [ ] **Automate recurring work with Scheduled Sessions** (dependency bumps, eval regression checks, triage).

## Evaluate a task before opening a session

Ask these three questions:

1. **Can I describe clear success criteria?** Passing tests, matching a pattern, green CI, a specific output format.
2. **Is there enough context?** Files, docs, examples, prototypes, MCP integrations, existing patterns in the codebase.
3. **Would breaking it down help?** For large work, split into focused sub-sessions.

## Pre‑task checklist (copy into the session prompt if useful)

```
[Task definition & scope]
- Clear start/end? Explicit success criteria?
- Did I scope with Ask Devin first?

[Context]
- Example files / patterns to follow:
- Relevant docs / links:
- MCP integrations needed:

[Validation]
- Tests / lint / typecheck commands:
- Can Devin run the app itself?
- Is Devin Review enabled on this repo?

[Review effort]
- Auto-Fix on?

[Task size]
- Session size target (XS / S / M). Break down if larger.
```

## Post‑task review

- Skim **Session Insights** — timeline, usage‑limit hits, env friction.
- Promote anything Devin learned into a **Knowledge note** so it sticks for next time.
- Re-use the *improved prompt* Session Insights suggests as the seed for similar work.

## Project‑specific mapping (First-Agent)

We're at **research → module creation**. Good fits right now:

- Literature/doc scraping into structured notes (runs nicely as scheduled sessions).
- Scaffolding a new module with tests + type hints + lint.
- Writing comparison prototypes for "should we use framework X vs Y?" (see §"Skip analysis paralysis" in [02](./02-coding-agents-101.md)).
- Drafting and iterating on PRDs together (see §"Co-develop a PRD" in [02](./02-coding-agents-101.md)).

Less good fits until we have more scaffolding:

- Tasks that depend on a test suite or lint config we **haven't written yet** — write those first (see [05](./05-research-to-module-workflow.md)).
- Broad "make the whole agent" prompts. Decompose first.
