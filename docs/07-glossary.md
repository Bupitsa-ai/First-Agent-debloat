# 07 — Glossary

Short definitions of terms used in this wiki and throughout First-Agent.

| Term | Meaning |
|---|---|
| **Agent** | A program that uses an LLM to decide a sequence of actions, usually via tool calls, to achieve a goal. |
| **Ask Devin** | Devin feature: scope a task, search the codebase, and auto-draft a high-context prompt before opening a session. |
| **Auto-Fix** | Devin Review feature that closes the loop on review comments and CI failures automatically. |
| **Devin Review** | An automated reviewer that inspects Devin's own PRs for likely bugs. |
| **Draft PR** | PR opened in draft state — CI runs, but it's not marked "ready for review" yet. |
| **Eval / Eval suite** | A reproducible set of inputs + expected behaviours used to measure agent quality across changes. |
| **Golden set** | Small, stable, hand-curated input set used for regression evaluation. |
| **Knowledge note** | Short, triggerable memory that Devin auto-retrieves in future sessions. |
| **LLM agent** | See *Agent*. |
| **MCP** | Model Context Protocol — lets Devin connect to external tools (Notion, Datadog, a DB, …). |
| **Managed Devin** | A child Devin session spawned by another session, typically to parallelise work. |
| **Module** | A self-contained unit of our agent code living in `src/<name>/` with its own tests and README. |
| **PRD** | Product Requirements Document. For First-Agent, a short markdown doc under `docs/prd/`. |
| **Playbook** | Reusable, versioned multi-step recipe Devin can execute on demand. |
| **Prompt** | Instruction we send to an LLM (or to Devin). Reusable ones live in `knowledge/prompts/`. |
| **ReAct** | Reasoning + Acting loop; a common agent orchestration pattern alternating thoughts and tool calls. |
| **Scheduled session** | Cron-like recurring Devin run — e.g. weekly eval or dep bump. |
| **Session Insights** | Post-session analytics: timeline, usage, suggested prompt improvements. |
| **Tool call** | A structured request from the LLM to invoke a named tool with typed arguments. |
| **Vector DB** | Database specialised for similarity search over embeddings (e.g. Qdrant, pgvector). |
