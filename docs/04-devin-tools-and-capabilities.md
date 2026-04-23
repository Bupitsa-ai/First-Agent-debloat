# 04 — Devin's Tools & Capabilities

A quick reference to the tools Devin has at its disposal on its VM and how we use them
for First-Agent. Keep in sync with <https://docs.devin.ai>.

## What Devin gets out of the box

| Capability | What it is | How we use it |
|---|---|---|
| **Shell** | Full Linux shell on a persistent VM | Run tests, lint, build, package scripts. |
| **Editor / file tools** | Read/write/edit files with dedicated tools | All code and docs changes. |
| **Browser** | Chromium w/ CDP on `localhost:29229` | Scripted logins (Playwright), doc scraping, UI QA. |
| **Desktop/GUI** | X server on `:0` | Manual-style verification of UI flows. |
| **Screen recording** | Built-in record / annotate | Share proof of UI tests in PRs. |
| **PR tooling** | `git_pr` (template, create, update) | Every code change goes through a PR. |
| **CI tooling** | `git pr_checks`, `ci_job_logs` | Wait on CI; fetch failing job logs. |
| **Secrets** | Org / user / repo-scoped, TOTP supported | API keys for LLM providers, DB creds, test accounts. |
| **MCP integrations** | Marketplace + custom | Datadog, Sentry, DBs, Figma, Notion, Stripe, … |
| **Scheduled sessions** | Cron-like recurring runs | Weekly eval regressions, dep upgrades, triage. |
| **Knowledge notes** | Durable memory across sessions | Conventions, gotchas, credentials locations. |
| **Playbooks** | Reusable multi-step recipes | Canonical release flow, eval pipeline, etc. |
| **Managed Devins** | Delegate sub-tasks to child sessions | Fan out A/B experiments in parallel. |

## Devin Review + Auto-Fix

When enabled, Devin:

- Reviews its own PRs and flags likely bugs.
- Responds to code-review comments automatically.
- Iterates on CI failures until green.

For First-Agent we want both **Devin Review** and **Auto-Fix** on. Final sign-off is
still manual.

## MCP — extending Devin beyond this repo

MCP (Model Context Protocol) lets Devin talk to external systems from inside a session.
Candidates we should wire up early:

- **Notion / Google Docs** — research notes live outside the repo.
- **Linear / GitHub Issues** — ticket lookups and updates.
- **A vector DB** (Qdrant / Pinecone / pgvector) — once we pick an embedding store.
- **An LLM-provider observability tool** (LangSmith / Helicone / Arize) — for eval runs.

How to add one: install via the MCP Marketplace on the Devin org settings page, then
reference the integration in session prompts.

## Scheduled sessions — ideas for First-Agent

- **Weekly paper / blog sweep.** "Fetch top posts about `<topic>` since last Monday,
  summarise into `knowledge/research/weekly/<date>.md`, open a draft PR."
- **Dependency bumps.** Dependabot-style updates in a batch PR.
- **Eval regression.** Run the agent against a fixed test set, post a diff of metrics.
- **Doc freshness.** Re-check `docs/` links weekly; flag 404s.

## Knowledge notes

Durable memory. Promote anything Devin should *remember next time* into a note:

- Project conventions (file layout, naming, commit style).
- Where to find credentials (by name — never paste secrets).
- Commands Devin kept getting wrong until it learned them.

See [06-knowledge-bank-template.md](./06-knowledge-bank-template.md) for format.

## Playbooks

Store multi-step recipes as playbooks for anything we do more than once. Initial
candidates for First-Agent:

- `ship-new-module` — scaffold, test, doc, changelog, open PR.
- `run-eval-suite` — load golden set, run, compare, post report.
- `cut-release` — bump version, tag, changelog, announce.

## What Devin can test for us

Devin can spin up the agent locally (shell), hit HTTP endpoints, click through a UI in
the browser, record the flow, and attach the recording to the PR. Always ask for a
recording when we're changing externally-visible behaviour.
