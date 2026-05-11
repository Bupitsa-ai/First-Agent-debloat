---
title: Создание агента — разбор build-your-own-openclaw
source: https://github.com/czl9707/build-your-own-openclaw
reference_impl: https://github.com/czl9707/pickle-bot
tags:
  - llm-agent
  - tutorial
  - architecture
  - openclaw
created: 2026-04-23
superseded_by: "knowledge/research/efficient-llm-agent-harness-2026-05.md"
---

# Создание агента: разбор `build-your-own-openclaw`

> **Status:** superseded by [`knowledge/research/efficient-llm-agent-harness-2026-05.md`](../knowledge/research/efficient-llm-agent-harness-2026-05.md) (archived 2026-05-08; body trimmed 2026-05-11 per PR-M).
>
> Excluded from `knowledge/llms.txt §BY-DEMAND-INDEX` for the OSS-agent routing surface. Tutorial transcript of an external repo with no live ADR / prompt / harness consumer in First-Agent. Original tutorial: <https://github.com/czl9707/build-your-own-openclaw>. Live ADR-7 (inner-loop / tool-contract) inputs are the harness note above and [`knowledge/research/how-to-build-an-agent-ampcode-2026-04.md`](../knowledge/research/how-to-build-an-agent-ampcode-2026-04.md). Original content preserved below for human reading; **do not load top-to-bottom** — open the upstream tutorial or the active harness notes instead.

> Статья-конспект туториала
> [`czl9707/build-your-own-openclaw`](https://github.com/czl9707/build-your-own-openclaw)
> — 18 шагов от простого chat loop до облегчённой версии
> [OpenClaw](https://github.com/openclaw/openclaw). Референс-реализация —
> [`pickle-bot`](https://github.com/czl9707/pickle-bot).

## Body trimmed — pointer only

The full pre-trim body lives in git history. It is not reproduced here because earlier abstract-style trims of this file introduced factual drift (see PR-13 Devin Review). To read the original verbatim:

```bash
git show cf7db4d:docs/agent-creation-github.md
# compiled: 2026-04-23; 553 lines pre-trim
```

## Where the current canonical content lives

- Active superseder: [`knowledge/research/efficient-llm-agent-harness-2026-05.md`](../knowledge/research/efficient-llm-agent-harness-2026-05.md) — read this instead of the pre-trim body.
- Original `source:` list is preserved in the frontmatter above (restored to their `cf7db4d` values; PR-M no longer modifies frontmatter).
- Inbound cross-references from older PR descriptions, ADRs, and supersession chains continue to resolve at this path — that is why the file is kept as a stub.

## Routing

Excluded from `knowledge/llms.txt §BY-DEMAND-INDEX` for the OSS-agent routing surface. Do not load this file top-to-bottom; open the active superseder above, or run the `git show` recipe if audit context is needed.
