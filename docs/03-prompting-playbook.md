# 03 — Prompting Playbook

Practical, copy-pasteable prompt patterns for First-Agent work. Every prompt below
assumes Devin can read this repo; link specific files so it can anchor quickly.

## The anatomy of a good Devin prompt

```
[Objective]      1-2 sentences. What outcome do you want?
[Context]        Links to files / docs / prior PRs / ADRs.
[Approach]       How you want it done (optional, but recommended).
[Constraints]    Stack, style, perf budget, security, backwards-compat.
[Acceptance]     Tests to pass, behaviour to verify, format of the output.
[Out of scope]   What Devin should NOT touch.
```

## Templates

### T1 — Research & summarise into a note

```
[Objective]
Research <topic> and produce a structured note in knowledge/research/<slug>.md.

[Context]
- Starting reading: <link1>, <link2>
- Related code in this repo: <path>
- Related ADR: knowledge/adr/<file>

[Approach]
- Skim the 3-5 most relevant primary sources.
- Produce: (1) TL;DR, (2) key concepts, (3) trade-offs vs. our current approach,
  (4) open questions.

[Constraints]
- Cite every non-obvious claim with a URL.
- No code changes in this PR.

[Acceptance]
- File exists, passes markdownlint (see scripts/lint-md.sh when it exists).
- Open a draft PR; do NOT mark ready-for-review.
```

### T2 — Scaffold a new module

```
[Objective]
Create src/<module>/ with a clean, typed skeleton for <purpose>.

[Context]
- Existing pattern to copy: src/<other_module>/
- Public API sketch:
    class X:
        def run(self, inp: InputT) -> OutputT: ...

[Approach]
- Add pyproject entry, module __init__, core class, one happy-path test, one error test.
- Wire into CI (add to pytest collection path if needed).

[Constraints]
- Python 3.11+, full type hints, ruff clean, mypy --strict clean for this module.
- No network calls in tests; mock the LLM client.

[Acceptance]
- `pytest src/<module>` green, `ruff check .` clean, `mypy src/<module>` clean.
- PR includes a short README.md in the module explaining responsibilities.

[Out of scope]
- Don't refactor sibling modules.
```

### T3 — Compare two approaches (analysis-paralysis buster)

```
[Objective]
Draft two implementations of <feature> in parallel branches so we can compare.

[Context]
- Option A: <framework / approach / link>
- Option B: <framework / approach / link>
- Evaluation rubric: latency, readability, test coverage, ease of extension.

[Approach]
- Two separate PRs, each implementing the smallest end-to-end slice.
- Each PR includes a short `DECISION_NOTES.md` in the diff.

[Acceptance]
- Both PRs pass CI.
- Each contains a short benchmark script under `bench/` with a reproducible command.
```

### T4 — Co-develop a PRD

```
[Objective]
Collaborate on a PRD for <feature>. No code yet.

[Context]
- Problem statement: ...
- Users: ...
- Known constraints: ...

[Approach]
- Read relevant code/docs in this repo and produce a draft PRD in docs/prd/<slug>.md.
- After the first draft, pause and wait for my feedback before iterating.

[Acceptance]
- PRD covers: goals, non-goals, user stories, API sketch, risks, rollout plan, metrics.
```

### T5 — Bug reproduction first, then fix

```
[Objective]
Reproduce <bug> with a failing test, THEN fix it.

[Context]
- Report: <link>
- Suspected area: <path>

[Approach]
- Step 1: Write a failing test that encodes the bug. Open a draft PR with just the test.
  Wait for my thumbs-up.
- Step 2: Implement the minimal fix. Push to the same PR.

[Acceptance]
- Test fails before the fix commit, passes after.
- No unrelated changes.
```

## Anti-patterns to avoid

- ❌ "Improve the code." — no success criteria.
- ❌ "Build the agent." — too big; decompose.
- ❌ Pasting a stack trace with no context about the repo or reproduction path.
- ❌ Letting Devin choose between two major approaches silently. Pick one, or ask it to draft both (T3).
- ❌ Merging without reading the diff, even on small PRs.

## Where to store prompts

Commit reusable prompts to [`knowledge/prompts/`](../knowledge/prompts/). One file per
prompt, named `<verb>-<slug>.md`. See [knowledge/prompts/README.md](../knowledge/prompts/README.md).
