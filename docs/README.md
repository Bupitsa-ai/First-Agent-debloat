# First-Agent Knowledge Base

A reference wiki for working with **devin.ai** on this repository while we design and
build our new LLM agent. This is written for a project that is currently in the
**research → start of module creation** phase.

> Everything here is distilled from Cognition's official guidance
> ([docs.devin.ai](https://docs.devin.ai/essential-guidelines/when-to-use-devin),
> [Coding Agents 101](https://devin.ai/agents101)) and adapted for this repo.

## How to read this wiki

| # | Doc | When to read it |
|---|---|---|
| 01 | [When to use Devin](./01-when-to-use-devin.md) | Before you open a new session — decide whether this task suits an agent. |
| 02 | [Coding Agents 101 — quick primer](./02-coding-agents-101.md) | First time using Devin, or when onboarding a new collaborator. |
| 03 | [Prompting playbook](./03-prompting-playbook.md) | Every time you write a prompt. Copy a template. |
| 04 | [Devin's tools & capabilities](./04-devin-tools-and-capabilities.md) | When scoping what Devin can do for you (MCP, browser, scheduled sessions, …). |
| 05 | [Research → module creation workflow](./05-research-to-module-workflow.md) | Our current phase. The day‑to‑day guide for this repo. |
| 06 | [Knowledge-bank template](./06-knowledge-bank-template.md) | When you want to seed Devin with durable context about the project. |
| 07 | [Glossary](./07-glossary.md) | When a term is unfamiliar. |

Supporting material lives under [`/knowledge`](../knowledge/README.md):

- [`knowledge/project-overview.md`](../knowledge/project-overview.md) — one‑page product & scope snapshot.
- [`knowledge/adr/`](../knowledge/adr/) — architecture decision records.
- [`knowledge/prompts/`](../knowledge/prompts/) — reusable prompts for recurring tasks.

## Status of this document set

| Area | Status | Owner |
|---|---|---|
| Docs scaffolding | Seeded by Devin | `@devin.ai` |
| Project overview | **Fill in during research phase** | you |
| ADRs | Empty — add one per significant decision | you |
| Prompt library | Seeded with two templates | evolves per task |

## Contributing

1. Edit Markdown directly and open a PR.
2. When a doc disagrees with the official Devin docs, the official docs win — update ours.
3. When you discover something Devin *should remember across sessions*, copy it into
   [`knowledge/`](../knowledge/) **and** promote it to a Devin Knowledge note
   (see [06-knowledge-bank-template.md](./06-knowledge-bank-template.md)).
