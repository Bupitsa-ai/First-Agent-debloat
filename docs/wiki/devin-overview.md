# Devin Overview

> What Devin is, how it works, and what it can do for your engineering workflow.

## What is Devin?

Devin is an **autonomous AI software engineer** built by Cognition AI. Unlike copilots that suggest code inline, Devin operates as a full agent — taking task descriptions and producing complete pull requests with minimal human intervention.

Devin has its own:
- **Shell & Terminal** — full command-line access to a development environment
- **IDE** — a code editor for writing and reviewing code
- **Browser** — a Chrome instance for web interactions, testing, and research
- **Desktop** — a full GUI environment for visual testing

## Core Capabilities

### End-to-End Task Execution

Devin can take a task from description to PR:
1. Read and understand the codebase
2. Plan an implementation approach
3. Write code across multiple files
4. Run tests and fix failures
5. Create a pull request with a clear description

### Multi-Session Parallelism

Run multiple Devin sessions simultaneously on independent tasks. This is where the "engineering manager" analogy becomes real — you delegate tasks in parallel and review the results.

- **Managed Devins**: Ask Devin to delegate sub-tasks to child sessions
- **Devin API**: Programmatic orchestration for batch operations
- **Slack/Teams integration**: Start sessions directly from conversations

### Self-Testing

Devin has a full desktop environment and can:
- Spin up your app locally
- Click through the UI using browser automation
- Take screenshots and record screen recordings
- QA its own changes before opening a PR

### Code Review & Auto-Fix

**Devin Review** automatically reviews PRs and can:
- Respond to code review comments
- Fix flagged bugs
- Iterate on CI failures
- Produce merge-ready PRs without human intervention

Enable **Auto-Fix** to let Devin close the loop entirely.

## Architecture

```
┌─────────────────────────────────────────────┐
│                  Devin Session               │
│                                              │
│  ┌──────────┐ ┌──────────┐ ┌──────────────┐ │
│  │  Shell    │ │   IDE    │ │   Browser    │ │
│  │ Terminal  │ │  Editor  │ │   Chrome     │ │
│  └──────────┘ └──────────┘ └──────────────┘ │
│                                              │
│  ┌──────────────────────────────────────────┐│
│  │           Desktop (GUI)                  ││
│  └──────────────────────────────────────────┘│
│                                              │
│  ┌──────────┐ ┌──────────┐ ┌──────────────┐ │
│  │Knowledge │ │  Skills  │ │  Playbooks   │ │
│  └──────────┘ └──────────┘ └──────────────┘ │
│                                              │
│  ┌──────────────────────────────────────────┐│
│  │     MCP Integrations (External Tools)    ││
│  └──────────────────────────────────────────┘│
└─────────────────────────────────────────────┘
```

## Key Concepts

| Concept | Description |
|---------|-------------|
| **Session** | A single Devin work unit — has its own machine, shell, browser, and file system |
| **Knowledge** | Persistent tips and instructions Devin recalls across sessions |
| **Skills** | `SKILL.md` files committed to repos that teach Devin reusable procedures |
| **Playbooks** | Reusable prompt templates for repeated tasks |
| **AGENTS.md** | A README-for-agents file providing repo-specific context |
| **MCP** | Model Context Protocol — connects Devin to external tools and data sources |
| **Managed Devins** | Child sessions that Devin delegates sub-tasks to |
| **Devin Review** | Automated PR review and auto-fix system |
| **Ask Devin** | Codebase exploration and prompt generation tool |
| **DeepWiki** | AI-generated documentation for any GitHub repository |

## When to Use Devin

Devin handles the majority of engineering tasks, including medium and hard complexity work. The key factors:

1. **Clear success criteria** — tasks with test suites, CI checks, or verifiable outcomes work best
2. **Sufficient context** — provide relevant files, patterns, docs, or examples
3. **Appropriate scope** — if a task takes you 3 hours or less, Devin can likely do it; for longer tasks, break them into focused sessions

**Best for:**
- Bug fixes with reproduction steps
- Feature implementation with clear specs
- Refactoring with test coverage
- Documentation updates
- Dependency upgrades
- Test coverage expansion
- CI/CD pipeline work
- Data migrations with clear schemas

## Official Resources

- [Devin Documentation](https://docs.devin.ai)
- [When to Use Devin](https://docs.devin.ai/essential-guidelines/when-to-use-devin)
- [Coding Agents 101](https://devin.ai/agents101)
- [Devin App](https://app.devin.ai)
