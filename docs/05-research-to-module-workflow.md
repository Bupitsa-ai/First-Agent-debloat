# 05 — Research → Module Creation Workflow

This is the day-to-day guide for where we are **right now** in First-Agent:
we've chosen to build an LLM agent, we're researching approaches, and we're about to
start creating modules.

The workflow has three phases. Use them in order. Don't skip.

---

## Phase R — Research

**Goal:** end with a written, reviewed plan for what we'll build and how.

### Steps

1. **Frame the question.** One-sentence problem + 3-5 sub-questions. Commit it to
   `knowledge/research/_open-questions.md`.
2. **Scope with Ask Devin.** Point it at any existing code + a short description;
   let it produce a high-context prompt for actual research sessions.
3. **Spawn parallel research sessions** (one per sub-question). Use prompt
   [T1 — Research & summarise](./03-prompting-playbook.md#t1--research--summarise-into-a-note).
   Each session produces a note in `knowledge/research/<slug>.md`.
4. **Consolidate.** A single session (or human) merges the notes into
   `knowledge/research/_consolidated.md` with a recommendation.
5. **Write a PRD** — use prompt
   [T4 — Co-develop a PRD](./03-prompting-playbook.md#t4--co-develop-a-prd).
   Output: `docs/prd/<slug>.md`. Review it with a human before leaving Phase R.

### Exit criteria for Phase R

- [ ] Problem statement is written and agreed on.
- [ ] PRD exists and has been reviewed.
- [ ] At least one ADR (`knowledge/adr/NNNN-*.md`) captures the "big decision" (framework, orchestration style, model choice).
- [ ] Open questions not answered by research are explicitly listed as **deferred**.

---

## Phase S — Scaffolding (must precede module work)

Before writing the first module, make sure Devin has the feedback loops it needs
(see [§ "Give access to CI, tests, types, and linters"](./02-coding-agents-101.md#prompting-basics-memorise-these-five)).

### Steps

1. **Pick a language** (likely typed Python 3.11+). Create `pyproject.toml`.
2. **Add lint + type + test tooling.**
   - `ruff` for lint+format.
   - `mypy --strict` or `pyright` for types.
   - `pytest` (+ `pytest-asyncio` if we go async).
3. **Set up CI** — minimal GitHub Actions workflow running lint, type, tests on PRs.
4. **Add a `Makefile`** (or `justfile`) with `make lint / test / typecheck / run`.
5. **Commit a `.pre-commit-config.yaml`** with ruff + eol + whitespace + markdownlint.
6. **Seed a Devin *knowledge note*** pointing at the Makefile. Template in
   [06-knowledge-bank-template.md](./06-knowledge-bank-template.md).

### Exit criteria for Phase S

- [ ] `make test` runs (even if only a smoke test) and returns 0.
- [ ] CI is green on `main`.
- [ ] Pre-commit hooks installed and working.
- [ ] Knowledge note for "how to run checks" is live.

---

## Phase M — Module creation (iterative)

Now we can actually build modules. Use this loop per module:

```
┌────────────────────────────────────────────────────────┐
│  1. Draft module design note in knowledge/adr/ if new  │
│  2. Prompt Devin with T2 (scaffold) → draft PR         │
│  3. Human review pass → request changes or merge       │
│  4. Promote any new gotchas to Knowledge notes         │
│  5. Update docs/README.md index if a new doc landed    │
└────────────────────────────────────────────────────────┘
```

### Module layout we've standardised on

```
src/<module_name>/
  __init__.py
  <module_name>.py        # one public entry class / function
  types.py                # pydantic / dataclass I/O types
  prompts/                # prompts as .md or .jinja2 files
  README.md               # what this module is for
  tests/
    test_<module>.py
```

### Rules of engagement for module PRs

- One module per PR. Small PRs merge faster.
- No untyped code. Ever.
- All network/LLM calls must be injectable + mockable.
- Every prompt file lives under `prompts/` — no inlining into Python strings.
- Every PR updates `CHANGELOG.md` (or we add a script to generate it).

---

## Anti-patterns specific to this phase

- ❌ Writing the first module before lint/test/types exist. Devin loses its feedback loop.
- ❌ Letting research notes rot on someone's laptop. Commit them.
- ❌ Skipping the PRD because "it's obvious." If it's obvious, writing it takes 10 minutes.
- ❌ Letting a single session span research + scaffolding + implementation. Split it.
