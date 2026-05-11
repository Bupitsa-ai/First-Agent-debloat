# How to Create an AI Agent on GitHub — Tutorial Walkthrough

> **Status:** superseded by [`research/efficient-llm-agent-harness-2026-05.md`](../knowledge/research/efficient-llm-agent-harness-2026-05.md) (archived 2026-05-08; body trimmed 2026-05-11 per PR-M).
>
> Excluded from `knowledge/llms.txt §BY-DEMAND-INDEX` for the OSS-agent routing surface — tutorial transcript of an external single-agent repo (`czl9707/build-your-own-openclaw`); no live ADR / prompt / harness consumer.
>
> **Body trimmed in PR-M to a one-paragraph abstract; full pre-trim text in git history at commit `cf7db4d`** (`git show cf7db4d:docs/agent-creation-github.md`). Trim rationale: `repo-audit-2026-05-10-revised.md` §4.1.

## Abstract

18-step Russian-language walkthrough of the GitHub tutorial repository
[czl9707/build-your-own-openclaw](https://github.com/czl9707/build-your-own-openclaw),
which builds a minimal single-agent harness from scratch in Python.
Covers: tool definition, LLM API setup (Anthropic / OpenAI), agent loop,
prompt engineering, error handling, multi-step planning, persistence,
context window management.

Predates ADR-7 (inner-loop / tool-contract); active replacement is the
harness research note. Useful as a teaching artefact only — no live
component reads it; OSS-agent flow skips via `llms.txt` exclusion.

## Where the content lives now

- **Active research:** [`research/efficient-llm-agent-harness-2026-05.md`](../knowledge/research/efficient-llm-agent-harness-2026-05.md)
- **Ampcode-style harness reference:** [`research/how-to-build-an-agent-ampcode-2026-04.md`](../knowledge/research/how-to-build-an-agent-ampcode-2026-04.md)
- **ADR-2 LLM tiering:** [`adr/ADR-2-llm-tiering.md`](../knowledge/adr/ADR-2-llm-tiering.md)
- **Upstream tutorial:** <https://github.com/czl9707/build-your-own-openclaw>

## Full pre-trim text

`git show cf7db4d:docs/agent-creation-github.md` — 553 lines, last full
revision 2026-05-08. Contains: full 18-step walkthrough with code
snippets per step, Russian commentary on each design decision, comparison
notes with First-Agent's planned architecture.
