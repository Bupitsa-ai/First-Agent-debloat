# Glossary

Short definitions of terms used in First-Agent and this wiki.

| Term | Description  |
|---|---|
| **ACI** | Agent–Computer Interface. The minimal surface through which an agent interacts with a computational environment (tools, files, shell). See [`research/cutting-edge-agent-research-radar-2026-05.md`](../knowledge/research/cutting-edge-agent-research-radar-2026-05.md). |
| **ADR** | Architecture Decision Record. Canonical location: [`knowledge/adr/`](../knowledge/adr/); process: [`knowledge/adr/README.md`](../knowledge/adr/README.md). Cheat sheet: [`knowledge/adr/DIGEST.md`](../knowledge/adr/DIGEST.md). |
| **Agent** | A program that uses an LLM to select a sequence of actions (usually via tool calls) in pursuit of a goal. |
| **Ask Devin** | Devin feature: task scoping, codebase search and automatic construction of a high-context prompt prior to opening a session. |
| **Auto-Fix** | Devin Review feature: automatic responses to review comments and CI failures. |
| **Axis (project)** | High level project axis, synonym for *Pillar* as defined in [`project-overview.md` §1.1](../knowledge/project-overview.md#11-четыре-столпа-цели-project-goal--four-pillars). Used in phrases such as "follow project axis". |
| **Axis (PR-checklist A/B/C)** | Evaluation criterion in §0 Decision Briefing per [AGENTS.md PR Checklist rule #8](../AGENTS.md#pr-checklist): (A) reduces session-start noise; (B) helps LLM locate context; (C) advances the selected `goal_lens`. (A) and (B) are stable axes for all notes; (C) is per-session. |
| **Devin Review** | Devin's automatic PR reviewer. |
| **Draft PR** | A Pull Request in draft state; CI runs, but it is not marked as ready-for-review. |
| **Eval / Eval suite** | Reproducible set of inputs + expected behaviour used to measure agent quality between changes. |
| **Feedback loop** | The cycle `action → observation → reflection → next action` — the core pattern of a reliable agent. |
| **goal_lens** | Frontmatter v2 field; one-sentence research goal, elicited at session start (Stage 1) per [`prompts/research-briefing.md`](../knowledge/prompts/research-briefing.md). Mandatory for notes produced by the research-briefing workflow; optional for all others. Allows the agent to filter the corpus against the current task without loading notes. See also *Lens*. |
| **Golden set** | A small, stable, manually annotated set of inputs used for regression evaluation. |
| **Harness** | Control layer around an LLM: loop / orchestration, prompts, tool registry, retrieval pipeline, sandbox. This is **not** ML or the model itself; this is everything an agent needs in between LLM calls. Pillar 3 of the project is to build the most token/tool-call efficient open-source harness for UC1+UC3. |
| **Hook** | Pre/post-tool extension point. In v0.1 the only implemented hook is the sandbox check ([ADR-6](../knowledge/adr/ADR-6-tool-sandbox-allow-list.md)); the v0.2+ inner loop ADR (ADR-7) formalises the hook primitive as a contract. |
| **Knowledge note** | A short trigger note that Devin automatically pulls into all future sessions. |
| **Lens** | See *goal_lens*. Used in phrases such as "follow project axis and lens goal" — almost always refers to the `goal_lens:` frontmatter of a note or session. |
| **LLM agent** | See *Agent*. |
| **Managed Devin** | A child Devin session spawned by another, usually for parallelism. |
| **MCP** | Model Context Protocol — a JSON-RPC shaped contract between an MCP host (agent) and MCP server (source of tools / resources / prompts). FA v0.1 implements a **convention** (in-process dispatcher mirrors JSON-RPC), not a dependency — see [ADR-2 amendment 2026-05-01](../knowledge/adr/ADR-2-llm-tiering.md#amendment-2026-05-01--mcp-forward-compat-tool-shape-convention). In Devin context, also the mechanism for connecting Devin to external systems. |
| **Minimalism-first** | Project principle ([`project-overview.md` §1.2](../knowledge/project-overview.md#12-enforceable-principle--minimalism-first)): every proposed new harness component passes a 3 question test before addition (research evidence; precedent for removal; replacement capability). A prevention strategy for greenfield projects. Contrast: *Subtraction-first*. |
| **Module** | A self contained unit of agent code located at `src/<name>/` with its own tests and README. |
| **NLAH** | Natural-Language Agent Harness — an externalised, editable natural language artifact describing harness behaviour; runtime is the Intelligent Harness Runtime (IHR). Source: Tsinghua paper `arXiv:2603.25723`. First-Agent already implements the text half of NLAH (AGENTS.md + ADRs + research notes); ADR-7 is the bridge to the code half. See [`research/efficient-llm-agent-harness-2026-05.md`](../knowledge/research/efficient-llm-agent-harness-2026-05.md). |
| **Phase R / S / M** | Lifecycle stages from [`docs/workflow.md`](./workflow.md): Phase R (Research) → Phase S (Scaffolding, complete) → Phase M (Module creation, **current**). See also *R-S-M*. |
| **Pillar** | One of 4 project goal pillars defined in [`project-overview.md` §1.1](../knowledge/project-overview.md#11-четыре-столпа-цели-project-goal--four-pillars): (1) research-backed implementation-first reference; (2) pragmatic single-user product; (3) most token/tool-call efficient open-source harness; (4) iteration via measurement. Synonym *Project axis*. |
| **Playbook** | A reusable multi-step recipe that Devin can execute on demand. |
| **PRD** | Product Requirements Document. For First-Agent this is a short markdown file under `docs/prd/`. |
| **Prompt** | An instruction sent to an LLM (or Devin). Reusable prompts are stored in `knowledge/prompts/`. |
| **R-S-M** | Lifecycle: Research → Scaffolding → Module. See *Phase R / S / M*. |
| **ReAct** | Reasoning + Acting loop — the ubiquitous agent orchestration pattern: thought → action → thought. |
| **Scheduled session** | A cron-like recurring Devin session — e.g. weekly eval run. |
| **Session Insights** | Post-session analytics: timeline, cost, suggestions for prompt improvement. |
| **Skill** | A `SKILL.md` file in the repository — a procedure that Devin knows how to execute. v0.1 commitment: agent capability to write its own skills under `~/.fa/skills/` or `knowledge/skills/` (Pillar 4 foundation; ADR-8 TBD). |
| **Subtraction-first** | Design rule originating at Anthropic: every harness component encodes an assumption about a model limitation; assumptions expire as models improve → remove the component if measurements show it is no longer effective. A **retrofit** strategy for legacy harnesses. FA has selected *Minimalism-first* (prevention) over subtraction. |
| **Tool call** | A structured request from an LLM to invoke a named tool with typed arguments. Native vs prompt-only — see [ADR-2 amendment 2026-04-29](../knowledge/adr/ADR-2-llm-tiering.md#amendment-2026-04-29--tool_protocol-field--native-by-default-v01-inner-loop-without-critic). |
| **UC1 — UC5** | Use case labels from [`project-overview.md` §4](../knowledge/project-overview.md#4-scope) + [ADR-1](../knowledge/adr/ADR-1-v01-use-case-scope.md): UC1 coding + PR; UC2 multi-source research (best effort in v0.1); UC3 local-docs-to-wiki; UC4 Telegram multi-user (deferred to v0.2); UC5 eval-driven harness iteration (deferred to v0.2; expanded in [ADR-1 amendment 2026-05-06](../knowledge/adr/ADR-1-v01-use-case-scope.md#amendment-2026-05-06--uc5-expanded-to-eval-driven-harness-iteration)). |
| **Vector DB** | Database for similarity search over embeddings (Qdrant, pgvector, Pinecone etc). |

---

All entries, formatting, links, bolding and internal conventions are preserved 100% unchanged. This is translated to idiomatic standard technical English used in the modern agent engineering community, not literal translation.

Would you like me to adjust any term for consistency, or make any other changes?
