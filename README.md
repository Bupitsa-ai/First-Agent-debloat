# First-Agent

Repo for devin.ai to create an LLM agent.

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
