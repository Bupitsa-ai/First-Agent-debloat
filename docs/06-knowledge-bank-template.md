# 06 — Knowledge-Bank Template

Devin has a **Knowledge** feature: short notes, each tagged with a *trigger*, that get
auto-injected into future sessions when the trigger is relevant. Use it for things
Devin should *always remember* about First-Agent.

Rule of thumb: **if you had to re-explain it to Devin twice, it belongs in a note.**

## Anatomy of a good note

```
Name:     <short, searchable label>
Trigger:  "When <situation>, this applies."
Body:
  - 1-5 bullets or a tiny code block.
  - Say what to do, not a narrative.
  - Link to the file/URL instead of pasting its content.
Scope:    repo-pinned if only relevant to First-Agent; org if broader.
```

### Good vs bad

✅ Good

> **Name:** Run tests in First-Agent
> **Trigger:** When running tests in GITcrassuskey-shop/First-Agent.
> **Body:** Use `make test` (wraps `pytest -q`). Unit tests live under `src/**/tests/`.
> Network and LLM calls must be mocked — fixtures in `tests/conftest.py`.
> **Scope:** pinned to `GITcrassuskey-shop/First-Agent`.

❌ Bad

> **Name:** testing info
> **Trigger:** (empty)
> **Body:** We have tests and you should run them. Usually pytest. Also sometimes we
> use mocks. Ask if unsure.

## Seed notes for First-Agent (add these first)

Copy these into Devin's knowledge UI (or propose via `suggest_knowledge`). File
equivalents under [`../knowledge/`](../knowledge/) act as the source of truth we can
version.

### N1 — Repo conventions

- **Trigger:** When editing code in `GITcrassuskey-shop/First-Agent`.
- **Body:**
  - Python 3.11+, full type hints required.
  - Lint: `ruff check .` and `ruff format .`.
  - Types: `mypy --strict src/` (or `pyright`).
  - Tests: `pytest -q`.
  - One module per PR. Follow the layout in [docs/05](./05-research-to-module-workflow.md#module-layout-weve-standardised-on).
  - Prompts live in `src/<module>/prompts/`, never inline in Python.

### N2 — How to run checks

- **Trigger:** When running checks in `GITcrassuskey-shop/First-Agent`.
- **Body:**
  - `make lint` / `make typecheck` / `make test` / `make check` (runs all three).
  - CI runs the same targets on every PR.

### N3 — Research note conventions

- **Trigger:** When writing research notes for First-Agent.
- **Body:**
  - Path: `knowledge/research/<slug>.md`.
  - Sections: TL;DR, Key concepts, Trade-offs vs. our approach, Open questions, Sources.
  - Cite every non-obvious claim.

### N4 — Prompt library

- **Trigger:** When asked to reuse a prompt for First-Agent.
- **Body:**
  - Reusable prompts live in `knowledge/prompts/` as `<verb>-<slug>.md`.
  - Prefer editing an existing prompt to adding a near-duplicate.

### N5 — Credentials & secrets

- **Trigger:** When First-Agent code needs credentials (LLM keys, DB creds, …).
- **Body:**
  - Never hardcode. Use environment variables; document them in `.env.example`.
  - Request via Devin's `secrets` tool with descriptive names (`OPENAI_API_KEY`, `ANTHROPIC_API_KEY`, …).
  - For TOTP prefix with `_2FA_`.

## Governance

- Review the knowledge bank **monthly**. Stale notes do harm.
- If two notes disagree, consolidate — don't let both live.
- When the repo's conventions change, update the notes in the same PR.

## Where to keep the "source of truth" copy

- [`/knowledge/README.md`](../knowledge/README.md) lists the notes we intend to keep.
- When you change a note in Devin's UI, update the corresponding markdown here too.
