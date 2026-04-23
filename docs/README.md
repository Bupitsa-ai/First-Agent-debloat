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


# First-Agent

Repo for devin.ai to create an LLM agent.

**Current phase:** research → start of module creation.

## Docs

Start here: [`docs/README.md`](./docs/README.md) — the knowledge-base/wiki for working
with Devin on this project.

Quick links:

- [When to use Devin](./docs/01-when-to-use-devin.md)
- [Coding Agents 101 primer](./docs/02-coding-agents-101.md)
- [Prompting playbook](./docs/03-prompting-playbook.md)
- [Devin's tools & capabilities](./docs/04-devin-tools-and-capabilities.md)
- [Research → module creation workflow](./docs/05-research-to-module-workflow.md)
- [Knowledge-bank template](./docs/06-knowledge-bank-template.md)
- [Glossary](./docs/07-glossary.md)

Project knowledge (ADRs, prompt library, research notes) lives under
[`knowledge/`](./knowledge/README.md).
> **Status:** Research Stage

## Overview

First-Agent is a project to build a new autonomous LLM agent, leveraging patterns and tools from the [Devin.ai](https://devin.ai) ecosystem. This repository serves as the foundational workspace for architecture, research documentation, and implementation.

## Documentation

The full documentation suite is in the [`docs/`](docs/) directory:

### Wiki (Reference)

- [Devin Overview](docs/wiki/devin-overview.md) — What Devin is, its architecture, and core capabilities
- [Coding Agents 101](docs/wiki/coding-agents-101.md) — Comprehensive guide to working with coding agents
- [Session Tools](docs/wiki/session-tools.md) — Shell, IDE, Browser, and Desktop tools reference
- [MCP Integrations](docs/wiki/mcp-integrations.md) — Model Context Protocol marketplace and custom servers

### Knowledge Base

- [Prompting Guide](docs/knowledge-base/prompting-guide.md) — Writing effective prompts and instructions
- [Knowledge Management](docs/knowledge-base/knowledge-management.md) — Persistent knowledge across sessions
- [Skills Reference](docs/knowledge-base/skills-reference.md) — SKILL.md reusable procedures
- [Playbooks Reference](docs/knowledge-base/playbooks-reference.md) — Reusable prompt templates

### Guides

- [Getting Started](docs/guides/getting-started.md) — First session setup and onboarding
- [Best Practices](docs/guides/best-practices.md) — Task evaluation and optimization strategies
- [Building Your Agent](docs/guides/building-your-agent.md) — Architecture guide for the First-Agent project
- [Automation & Workflows](docs/guides/automation-workflows.md) — Scheduled sessions, CI/CD, event-driven automation

## Quick Links

- [Documentation Index](docs/README.md)
- [Devin Docs](https://docs.devin.ai)
- [Coding Agents 101](https://devin.ai/agents101)
- [Devin App](https://app.devin.ai)

## Project Structure

```
First-Agent/
├── README.md              # This file
├── AGENTS.md              # Instructions for AI agents working in this repo
└── docs/
    ├── README.md          # Documentation index
    ├── wiki/              # Reference pages
    │   ├── devin-overview.md
    │   ├── coding-agents-101.md
    │   ├── session-tools.md
    │   └── mcp-integrations.md
    ├── knowledge-base/    # Deep-dive knowledge articles
    │   ├── prompting-guide.md
    │   ├── knowledge-management.md
    │   ├── skills-reference.md
    │   └── playbooks-reference.md
    └── guides/            # How-to guides
        ├── getting-started.md
        ├── best-practices.md
        ├── building-your-agent.md
        └── automation-workflows.md
```

