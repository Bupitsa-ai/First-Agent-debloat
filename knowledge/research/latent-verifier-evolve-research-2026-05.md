---
title: "Latent Space / CUA Verifiers / Squeeze Evolve vs First-Agent ADR-1..6"
source:
  - "https://arxiv.org/html/2604.02029v1"
  - "https://arxiv.org/html/2604.06240v1"
  - "https://arxiv.org/html/2604.07725v2"
  - "https://github.com/squeeze-evolve/squeeze-evolve"
  - "https://huggingface.co/datasets/microsoft/CUAVerifierBench"
compiled: "2026-05-04"
chain_of_custody: >
  Primary facts are taken from the arXiv HTML pages, the squeeze-evolve
  README, and the CUAVerifierBench dataset card. Mapping to First-Agent is
  inferred from current ADR-1..6, HANDOFF.md, AGENTS.md, and the research
  briefing/template rules on main as of 2026-05-04. Recommendations below are
  research input only, not accepted ADRs.
goal_lens: >
  Form a list of research artifacts that strengthen First-Agent and identify
  source ideas worth carrying forward.
tier: stable
links:
  - "../adr/ADR-1-v01-use-case-scope.md"
  - "../adr/ADR-2-llm-tiering.md"
  - "../adr/ADR-3-memory-architecture-variant.md"
  - "../adr/ADR-4-storage-backend.md"
  - "../adr/ADR-5-chunker-tool.md"
  - "../adr/ADR-6-tool-sandbox-allow-list.md"
  - "./cutting-edge-agent-research-radar-2026-05.md"
  - "./cross-reference-ampcode-sliders-to-adr-2026-04.md"
  - "./semi-autonomous-agents-cross-reference-2026-05.md"
mentions:
  - "Latent Space Survey"
  - "Universal Verifier"
  - "CUAVerifierBench"
  - "Squeeze Evolve"
  - "squeeze-evolve/squeeze-evolve"
  - "Coconut"
  - "SoftCoT"
  - "LSRL"
  - "Fara-7B"
  - "Online-Mind2Web"
  - "vLLM"
confidence: inferred
claims_requiring_verification:
  - >
    Latent-space mechanisms are summarized at survey/taxonomy level only. No
    individual latent reasoning or latent memory method was re-benchmarked for
    First-Agent.
  - >
    CUA Verifier numbers such as near-zero false positives, 70% expert quality,
    and dataset sizes are copied from the paper abstract/dataset card and not
    independently reproduced.
  - >
    Squeeze Evolve cost/throughput claims and registry/config details are copied
    from the paper abstract and README. Transfer to First-Agent depends on actual
    target models exposing logprobs and stable cost metadata.
superseded_by: none
---

> **Status:** active. Note produced via
> [`knowledge/prompts/research-briefing.md`](../prompts/research-briefing.md)
> as a corrective continuation of a shallower prior pass. §0 is the Decision
> Briefing; later sections hold the deeper source analysis.

## 0. Decision Briefing

### R-1 — Acceptance rubric fixtures before verifier machinery

- **What:** Перенести из CUA Verifier не «Universal Verifier как модель»,
  а cheap artefact: набор First-Agent acceptance rubric fixtures, где
  каждый пример разделяет `process`, `outcome`, `controllable_failure`,
  `uncontrollable_failure`, `evidence`, `side_effect`. Это усиливает
  Acceptance Taxonomy и будущий ADR-7 без нового runtime.
- **Project-axis fit (stable across notes):**
  - (A) reduces session-start noise: YES (~400-800 tokens per future
    verification discussion; один fixture-файл заменяет пересказ paper'а)
  - (B) helps LLM find context when needed: YES (fixtures are a pointer-shape
    source for future Coder/Eval agents)
- **Goal-lens fit (per session, dynamic):**
  - (C) advances chosen goal_lens "Form a list of research artifacts that
    strengthen First-Agent and identify source ideas worth carrying forward.":
    YES (directly produces a concrete research artefact from the CUA paper)
- **Cost:** cheap (<1h)
- **Verdict:** TAKE
- **If UNCERTAIN-ASK:** n/a (TAKE)
- **Alternative-if-rejected:** Keep CUA Verifier as background reading only
  and let ADR-7 invent verification fields ad hoc.
- **Concrete first step (if TAKE):** Add
  `knowledge/research/acceptance-rubric-fixtures-2026-05.md` with 5-8
  coding-agent examples.

### R-2 — ADR-7 tool registry should be per-session, registry-by-name

- **What:** Borrow the Squeeze Evolve operator-registry pattern
  (`@registry.register("name")` + config string resolution), but scope it
  per session/run rather than global singleton. This fits ADR-6 sandboxing:
  allowed tools can differ by session and by path allow-list.
- **Project-axis fit (stable across notes):**
  - (A) reduces session-start noise: YES (~300-600 tokens saved in ADR-7
    design passes; one registry shape replaces repeated API debate)
  - (B) helps LLM find context when needed: YES (stable names let agents
    grep tool definitions, config references, and audit logs)
- **Goal-lens fit (per session, dynamic):**
  - (C) advances chosen goal_lens "Form a list of research artifacts that
    strengthen First-Agent and identify source ideas worth carrying forward.":
    YES (turns a repo implementation pattern into an ADR-7 artefact)
- **Cost:** medium (1-4h)
- **Verdict:** TAKE
- **If UNCERTAIN-ASK:** n/a (TAKE)
- **Alternative-if-rejected:** Use an implicit Python module of tool functions;
  cheaper now, worse for audit and config-driven routing.
- **Concrete first step (if TAKE):** In future ADR-7, add a `ToolRegistry`
  contract: `register(name, input_schema, fn)`, config lookup by `name`,
  and per-session registry construction after sandbox load.

### R-3 — Minimal trace/eval schema should separate process and outcome

- **What:** First-Agent should not wait for a full verifier. Start with a
  trace/eval JSONL shape that records `process_result`, `outcome_result`,
  `failure_class`, `evidence_refs`, `tool_calls`, and `side_effects`.
  CUA Verifier shows why process/outcome separation matters; Squeeze Evolve
  shows why cheap fitness/eval signals become routing inputs later.
- **Project-axis fit (stable across notes):**
  - (A) reduces session-start noise: YES (~500-1000 tokens saved when future
    agents ask "what does a good run record?")
  - (B) helps LLM find context when needed: YES (trace fields become stable
    retrieval anchors across tests, audit logs, and future eval notes)
- **Goal-lens fit (per session, dynamic):**
  - (C) advances chosen goal_lens "Form a list of research artifacts that
    strengthen First-Agent and identify source ideas worth carrying forward.":
    YES (a direct architecture artefact for ADR-7 and Phase M modules)
- **Cost:** medium (1-4h)
- **Verdict:** TAKE
- **If UNCERTAIN-ASK:** n/a (TAKE)
- **Alternative-if-rejected:** Keep pytest/lint as the only signals and add
  trace semantics later when failures are already inconsistent.
- **Concrete first step (if TAKE):** Add a compact schema block to ADR-7 or a
  pre-ADR note `knowledge/research/trace-eval-schema-2026-05.md`.

### R-4 — Keep latent-space methods as v0.2 watch-list, not v0.1 architecture

- **What:** The Latent Space survey is strategically interesting but should
  not change ADR-3/4/5 now. Latent reasoning/memory are mostly model-internal
  or training-time mechanisms; First-Agent v0.1 needs auditable,
  filesystem-canonical context, not opaque hidden-state memory.
- **Project-axis fit (stable across notes):**
  - (A) reduces session-start noise: YES (explicitly prevents re-litigating
    embeddings/latent memory during chunker work)
  - (B) helps LLM find context when needed: PARTIAL (watch-list pointer helps,
    but no immediate file/API shape)
- **Goal-lens fit (per session, dynamic):**
  - (C) advances chosen goal_lens "Form a list of research artifacts that
    strengthen First-Agent and identify source ideas worth carrying forward.":
    PARTIAL (worthy signal, but mainly as a non-goal guardrail)
- **Cost:** cheap (<1h)
- **Verdict:** DEFER
- **If UNCERTAIN-ASK:** n/a (DEFER)
- **Alternative-if-rejected:** Remove latent-space coverage entirely from the
  active research backlog.
- **Concrete first step (if TAKE):** n/a (DEFER; keep as one watch-list row in
  this note and do not amend ADR-3).

### R-5 — Do not add dynamic model routing to ADR-2 before trace data exists

- **What:** Squeeze Evolve's confidence routing is attractive, but First-Agent
  should not amend ADR-2 from static role routing to dynamic routing yet.
  Without local traces, logprob availability checks, and fuzzy-task fitness
  rules, dynamic routing would add a control loop we cannot evaluate.
- **Project-axis fit (stable across notes):**
  - (A) reduces session-start noise: YES (pins "not now" and prevents
    premature ADR-2 churn)
  - (B) helps LLM find context when needed: YES (points future routing work to
    the prerequisites: traces, logprobs, cost metadata, fuzzy acceptance)
- **Goal-lens fit (per session, dynamic):**
  - (C) advances chosen goal_lens "Form a list of research artifacts that
    strengthen First-Agent and identify source ideas worth carrying forward.":
    YES (records a valuable idea with explicit gating conditions)
- **Cost:** cheap (<1h)
- **Verdict:** DEFER
- **If UNCERTAIN-ASK:** n/a (DEFER)
- **Alternative-if-rejected:** Amend ADR-2 now with `logprobs` and N-model
  routing fields, accepting speculative schema churn.
- **Concrete first step (if TAKE):** n/a (DEFER; revisit after R-3 traces
  exist for at least one Phase M module).

### R-6 — Add CUAVerifierBench as eval-design reference, not benchmark target

- **What:** CUAVerifierBench should be cited as an example of verifier
  dataset shape: trajectories, screenshots, logs, human labels, rubric scores.
  It should not become a First-Agent benchmark because FA v0.1 is not a browser
  CUA project.
- **Project-axis fit (stable across notes):**
  - (A) reduces session-start noise: YES (keeps future eval discussions from
    confusing "reference shape" with "target benchmark")
  - (B) helps LLM find context when needed: YES (dataset-card fields are a
    concrete schema reference for trace/eval notes)
- **Goal-lens fit (per session, dynamic):**
  - (C) advances chosen goal_lens "Form a list of research artifacts that
    strengthen First-Agent and identify source ideas worth carrying forward.":
    YES (clear artifact/reference boundary)
- **Cost:** cheap (<1h)
- **Verdict:** TAKE
- **If UNCERTAIN-ASK:** n/a (TAKE)
- **Alternative-if-rejected:** Treat CUA verifier work as unrelated because FA
  is not browser-control-first.
- **Concrete first step (if TAKE):** In the R-1 fixture note, add a short
  "reference fields from CUAVerifierBench" table.

### Summary

| R-N | Verdict | Project-fit (A / B) | Goal-fit (C) | Cost | Alternative-if-rejected | User decision needed? |
|-----|---------|---------------------|--------------|------|--------------------------|------------------------|
| R-1 | TAKE | YES / YES | YES (fixtures) | cheap | Background-only CUA paper | No (TAKE) |
| R-2 | TAKE | YES / YES | YES (ADR-7 API) | medium | Implicit function module | No (TAKE) |
| R-3 | TAKE | YES / YES | YES (trace schema) | medium | Pytest/lint only | No (TAKE) |
| R-4 | DEFER | YES / PARTIAL | PARTIAL (watch-list) | cheap | Drop latent-space from backlog | No (DEFER) |
| R-5 | DEFER | YES / YES | YES (gated routing) | cheap | Speculative ADR-2 amendment | No (DEFER) |
| R-6 | TAKE | YES / YES | YES (eval reference) | cheap | Treat CUA work as unrelated | No (TAKE) |

## 1. TL;DR

- Предыдущий shallow pass был полезен как source list, но нарушал новый
  repo-template contract: не было §0 Decision Briefing / `goal_lens:`, а branch
  diff accidental удалял unrelated files from main. Эта версия исправляет форму
  и углубляет mapping.
- CUA Verifier — самый практичный источник для v0.1: не как full verifier, а как
  язык acceptance/eval artefacts: process vs outcome, controllable vs
  uncontrollable failures, evidence refs, side effects.
- Squeeze Evolve — второй практичный источник: не как evolutionary inference
  loop, а как API pattern for registries/config and future confidence routing.
- Latent Space Survey — стратегический watch-list. Он подтверждает, что
  explicit token traces не всегда efficient, но FA v0.1 deliberately optimizes
  for auditability and filesystem-canonical memory.
- Следующий immediate artefact: `acceptance-rubric-fixtures-2026-05.md`
  (cheap), затем ADR-7 включает registry + trace schema (medium).
- Dynamic routing by confidence/logprobs следует отложить до появления trace
  data from real Phase M modules; иначе ADR-2 станет speculative.

## 2. Scope, метод

**Goal-lens (verbatim):** "Form a list of research artifacts that strengthen
First-Agent and identify source ideas worth carrying forward."

Метод:

1. Перечитаны primary sources: три arXiv HTML страницы, squeeze-evolve README,
   CUAVerifierBench dataset card.
2. Сверены выводы с current FA constraints: ADR-1..6, HANDOFF next steps,
   AGENTS.md PR rules, research-briefing template.
3. Каждый source разделён на:
   - directly actionable artefacts for v0.1;
   - deferred v0.2/watch-list ideas;
   - non-fit / avoid importing.
4. Recommendations in §0 use the repo's eight-field Decision Briefing shape.

Out-of-method:

- Не воспроизводились benchmark numbers.
- Не читался full codebase squeeze-evolve beyond README/API surface.
- Не предлагается менять accepted ADRs directly in this PR.

## 3. Key concepts

- **Latent space:** continuous hidden-state substrate where model computation
  can happen without being decoded into human-readable tokens.
- **Explicit / verbal space:** generated text tokens, including CoT/ReAct traces;
  auditable but sequential and linguistically redundant.
- **Universal Verifier:** CUA paper's verifier system combining rubric creation,
  multimodal scoring over trajectory screenshots, outcome judgment, and failure
  diagnosis.
- **Process reward / outcome reward:** process asks whether the agent followed a
  good path; outcome asks whether the final task succeeded. They can disagree.
- **Controllable / uncontrollable failure:** whether failure is attributable to
  the agent/tool choice vs external environment constraints.
- **CUAVerifierBench:** dataset measuring verifiers, not agents; rows include
  trajectories, screenshots, logs, human labels, and Universal Verifier outputs.
- **Verifier-free evolution:** iterative population refinement without an
  external correctness verifier.
- **Fitness-based routing:** Squeeze Evolve routes candidate groups to cheap/mid/
  expensive models based on confidence or diversity signals.
- **Operator registry:** functions registered by string name and selected from
  config, e.g. `routing.fitness: confidence`.

## 4. Source analysis and mapping

### 4.1 S1 — Latent Space Survey

The survey frames latent space as a machine-native substrate and explicit token
reasoning as only one interface to model computation. Its abstract identifies
four mechanism lines — Architecture, Representation, Computation, Optimization —
and seven ability areas — Reasoning, Planning, Modeling, Perception, Memory,
Collaboration, Embodiment.

**Useful signal for FA:** the paper is a reminder that token-visible reasoning
is not free. Long CoT and verbose agent traces burn context. This supports FA's
existing bias toward compact, structured artefacts: `llms.txt`, ADRs,
Mechanical Wiki chunks, and HANDOFF rather than full transcript replay.

**Non-fit for v0.1:** latent reasoning and latent memory are model-internal or
training-time mechanisms. FA v0.1 is a local coding-agent project whose key
constraint is auditability. Hidden-state memory would conflict with ADR-3's
filesystem-canonical choice and ADR-4's SQLite FTS5 baseline.

| Latent-space idea | FA fit | Action |
|---|---|---|
| Token traces are inefficient | High | Keep notes/ADR/trace schemas concise and structured. |
| Latent memory | Low for v0.1 | Defer; do not replace Mechanical Wiki. |
| Latent planning/reasoning | Medium future | Watch model-provider features; no local architecture change. |
| Multi-agent latent collaboration | Low now | Out of ADR-1 v0.1 scope. |

### 4.2 S2 — The Art of Building Verifiers for Computer Use Agents

The paper's abstract gives four design principles:

1. rubrics with meaningful, non-overlapping criteria;
2. process and outcome rewards as complementary signals;
3. controllable vs uncontrollable failure diagnosis;
4. divide-and-conquer context management over screenshot evidence.

It also reports that the Universal Verifier agrees with humans as often as
humans agree with each other, reduces false positives near zero compared with
WebVoyager/WebJudge baselines, and that an auto-research agent reached 70% of
expert quality in 5% of the time while missing key structural decisions.

For First-Agent, the paper is not a call to build a CUA verifier. FA v0.1 is
not browser-control-first. The transferable artifact is the *evaluation
language*.

| CUA verifier principle | FA equivalent | Why it matters |
|---|---|---|
| Non-overlapping rubric criteria | Acceptance Taxonomy fixtures | Prevents vague PR acceptance like "looks good". |
| Process reward | Tool-call / plan-following trace fields | Separates good path blocked by external issue from bad path. |
| Outcome reward | Tests/lint/typecheck/manual check result | Keeps final user-facing success explicit. |
| Controllable failure | Agent/tool/design bug | Actionable for Coder/Debug. |
| Uncontrollable failure | missing secret, flaky CI, unavailable API | Should trigger escalation, not code churn. |
| Screenshot evidence | file/snippet/log/test evidence refs | Same shape without browser screenshots. |
| Side-effect detection | sandbox/audit log + git diff scope | Prevents hidden repo/environment damage. |

CUAVerifierBench strengthens this mapping because its dataset card exposes
fields FA can mimic at a smaller scale: `web_surfer_log`, `screenshots`,
`uv_rubric_score`, `uv_outcome_success`, `final_human_outcome_label`, and
`final_human_process_label`. FA substitutes code/test/log artefacts for
screenshots, but the schema split is directly useful.

### 4.3 S3/S4 — Squeeze Evolve paper and repo

The paper/repo present a multi-model evolutionary loop:

1. expensive model initializes candidate population;
2. candidates are scored by confidence or diversity;
3. groups are routed by difficulty to cheap/mid/expensive models;
4. outputs are recombined and population updates across loops.

The README is more immediately useful than the algorithm itself. It documents
config-driven routing and operator registries:

```yaml
routing:
  confidence_percentiles: [50.0]
  fitness: confidence
  selection: uniform
  recombination: aggregate
  evaluation: none
```

It also states that confidence can use token log-probabilities already produced
during inference, while diversity can be a zero-cost answer-level signal. For N
models, the README uses N-1 percentile thresholds.

For First-Agent:

- **Take now:** registry-by-name for tools/eval plugins in ADR-7.
- **Take later:** optional `supports_logprobs` / cost metadata after trace data
  exists.
- **Do not take now:** evolutionary inference loop. UC1 coding + PR-write is
  fuzzy and expensive to score; chunker Phase M does not need population
  evolution.

| Squeeze Evolve concept | FA mapping | Verdict |
|---|---|---|
| Operator registries | ADR-7 tool registry / eval plugin registry | TAKE |
| YAML config resolves operators | `~/.fa/models.yaml` and future tool config | TAKE shape, not exact fields |
| Confidence routing | future v0.2 role-internal routing | DEFER |
| Diversity preservation | useful for multi-candidate planning/eval | DEFER |
| Expensive initialization | supports elite Planner role in ADR-2 | TAKE as rationale |
| Evolutionary loop | not needed for first chunker/module | SKIP for v0.1 |

## 5. Cross-source synthesis

### 5.1 Convergences

1. **Structured artefacts beat raw transcripts.** Latent-space work criticizes
   verbose token traces; CUA Verifier needs structured rubrics/evidence; Squeeze
   Evolve needs config-resolved operators. This supports FA's Mechanical Wiki
   and explicit ADR/research artefact discipline.
2. **Evaluation needs typed signals.** CUA Verifier separates process/outcome;
   Squeeze Evolve turns fitness into routing; FA should start with trace fields
   before adding smarter routing.
3. **Strong models should be spent on high-leverage decisions.** Squeeze
   Evolve's expensive initialization aligns with ADR-2's elite Planner. CUA's
   auto-research result also warns that weaker autonomous research may miss
   structural choices even when it is fast.
4. **Plugin/registry shape is recurring.** MCP tools, Squeeze operators, future
   eval plugins, and FA tools all want stable names, schemas, and config
   resolution.

### 5.2 Tensions

| Tension | Source side | FA side | Resolution |
|---|---|---|---|
| Hidden-state efficiency vs auditability | Latent-space methods prefer compact hidden computation | FA needs inspectable repo memory | Defer latent memory. |
| Browser-CUA verification vs coding-agent verification | CUA Verifier uses screenshots and UI trajectory evidence | FA uses code diff, tests, logs, snippets | Translate fields, not benchmark. |
| Dynamic routing vs static ADR-2 roles | Squeeze Evolve routes by confidence | FA chose static role routing | Gate on traces and logprobs. |
| Evolutionary loops vs focused PRs | Squeeze Evolve optimizes candidate populations | FA optimizes reviewable PRs | Skip for v0.1. |

## 6. Risks and caveats

- **Benchmark transfer risk:** CUA and Squeeze Evolve numbers are from their
  own domains. They are not evidence that FA's coding tasks will improve.
- **Schema churn risk:** Adding `logprobs` / N-model routing to `models.yaml`
  before real traces may create unstable config surface.
- **Verifier overbuild risk:** A full CUA-style verifier is too large for v0.1.
  The cheap useful artefact is a fixture/rubric note.
- **Latent-space hype risk:** Survey scope is broad. Treat it as trend radar,
  not an implementation recipe.
- **Singleton registry risk:** Squeeze Evolve's registry examples look global.
  FA should prefer per-session registry construction because ADR-6 sandbox rules
  are session/environment dependent.

## 7. Numbered recommendations (R-1..R-6)

### R-1 — Acceptance rubric fixtures before verifier machinery (cost: cheap)

Create a small research artefact of examples. Each example should show:

- user request / task;
- expected outcome predicate;
- acceptable process evidence;
- unacceptable side effect;
- controllable failure example;
- uncontrollable failure example;
- evidence refs expected from the agent.

This is the highest-value immediate artifact because it converts CUA Verifier's
paper-level ideas into a repo-local language future agents can reuse.

### R-2 — Per-session registry-by-name for ADR-7 (cost: medium)

ADR-7 should specify tool registration and lookup by stable name. The shape is
simple:

```text
ToolRegistry(session_config, sandbox)
  .register(name, input_schema, fn)
  .resolve(name) -> ToolSpec
```

The exact implementation should not copy Squeeze Evolve's global singleton
style. A per-session registry is safer: sandbox permissions, tool allow-lists,
and audit-log destinations can differ between runs.

### R-3 — Process/outcome trace schema (cost: medium)

Before building Eval role behavior, define the minimal event shape a Phase M
module can emit or save:

```yaml
task_id: "<stable id>"
process_result: pass | fail | blocked | unknown
outcome_result: pass | fail | unknown
failure_class: controllable | uncontrollable | mixed | none
evidence_refs:
  - "tests/test_chunker.py::test_markdown_headings"
tool_calls: []
side_effects: []
```

This is intentionally smaller than CUA Verifier. It gives future agents enough
structure to compare runs without introducing a model judge.

### R-4 — Latent-space watch-list only (cost: cheap)

Do not change ADR-3/4/5. If future models expose reliable latent-memory or
latent-planning APIs, evaluate them after the filesystem-canonical baseline has
measured failure modes. Until then, latent-space work is a justification for
concise external artefacts, not a replacement for them.

### R-5 — Dynamic routing gated on traces/logprobs (cost: cheap now, expensive later)

Record the prerequisites for any future ADR-2 amendment:

1. at least one Phase M module produces trace/eval data;
2. target providers expose logprobs or equivalent confidence signals;
3. `models.yaml` has cost metadata that is true enough for routing;
4. fuzzy acceptance is mapped to a fitness signal.

Only after those four exist should confidence-based routing move from research
note to ADR.

### R-6 — CUAVerifierBench as schema reference (cost: cheap)

Use CUAVerifierBench as an eval-design reference because it measures verifier
agreement against human labels and stores both trajectory and annotation
configs. Do not run FA against it as a benchmark; it is browser-task-specific.

## 8. Open questions (Q-1..Q-4)

### Q-1 — Should acceptance fixtures be standalone or folded into ADR-7?

Standalone research note is cheaper and reviewable before ADR-7. Folding into
ADR-7 reduces file count but risks overloading the ADR. Recommendation: start
standalone, cite it from ADR-7.

### Q-2 — What is the smallest evidence ref shape for code tasks?

Candidate fields: `file`, `lines`, `command`, `exit_code`, `log_excerpt`,
`pr_url`. This should be settled when writing the trace/eval schema.

### Q-3 — Are logprobs available for the actual ADR-2 model tiers?

Squeeze-style confidence routing is only useful if providers expose comparable
confidence data. Need a provider capability check before any config amendment.

### Q-4 — Should Eval role exist in v0.1 or remain post-v0.1?

R-1/R-3 do not require a separate Eval LLM. They only create artefacts and
trace fields. A dedicated Eval role can remain deferred until traces exist.

## 9. Files used

- `https://arxiv.org/html/2604.02029v1`
- `https://arxiv.org/html/2604.06240v1`
- `https://arxiv.org/html/2604.07725v2`
- `https://github.com/squeeze-evolve/squeeze-evolve`
- `https://raw.githubusercontent.com/squeeze-evolve/squeeze-evolve/main/README.md`
- `https://huggingface.co/datasets/microsoft/CUAVerifierBench`
- `AGENTS.md`
- `HANDOFF.md`
- `knowledge/prompts/research-briefing.md`
- `knowledge/research/_template.md`
- `knowledge/adr/ADR-1-v01-use-case-scope.md`
- `knowledge/adr/ADR-2-llm-tiering.md`
- `knowledge/adr/ADR-3-memory-architecture-variant.md`
- `knowledge/adr/ADR-4-storage-backend.md`
- `knowledge/adr/ADR-5-chunker-tool.md`
- `knowledge/adr/ADR-6-tool-sandbox-allow-list.md`

## 10. Out of scope

- Implementing the acceptance fixture note.
- Writing ADR-7.
- Changing `models.yaml` schema.
- Reproducing CUA Verifier or Squeeze Evolve benchmarks.
- Importing latent-space methods into First-Agent runtime.
