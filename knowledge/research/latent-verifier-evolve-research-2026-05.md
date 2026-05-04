---
title: "Latent Space / CUA Verifiers / Squeeze Evolve — разбор трёх paper'ов и репо vs ADR-1..6 (2026-05-04)"
compiled: "2026-05-04"
source:
  - "https://arxiv.org/abs/2604.02029v1"
  - "https://arxiv.org/abs/2604.06240v1"
  - "https://arxiv.org/abs/2604.07725v2"
  - "https://github.com/squeeze-evolve/squeeze-evolve"
chain_of_custody: >
  Факты из paper'ов цитируются по arxiv HTML-версиям (v1/v2); факты по
  squeeze-evolve — из README репозитория на момент 2026-05-04.
  Все mapping'и на First-Agent ADR выведены из текущих версий
  ADR-1..6 в этом репо. Рекомендации — research input, не ADR.
claims_requiring_verification:
  - "Числа по Squeeze Evolve (3× cost reduction, 10× throughput) — из abstract
    paper'а. Конкретные цифры зависят от выбранных моделей и задач; до
    применения в First-Agent нужно воспроизвести на целевых tier'ах из ADR-2."
  - "Утверждение о 70% expert quality auto-research (CUA Verifier paper)
    основано на Cohen's κ ~0.55 vs ~0.7 для эксперта. Метрика — agreement
    с human labels на CUAVerifierBench; переносимость на другие task-типы
    не проверена."
  - "Latent Space Survey — обзорная работа с ~400 цитатами. Конкретные
    performance-числа по отдельным methods (Coconut, LSRL, etc.) не
    верифицировались здесь; они вторичны для наших целей."
status: research
tier: stable
supersedes: none
extends:
  - knowledge/research/cutting-edge-agent-research-radar-2026-05.md
  - knowledge/research/cross-reference-ampcode-sliders-to-adr-2026-04.md
related:
  - knowledge/adr/ADR-1-v01-use-case-scope.md
  - knowledge/adr/ADR-2-llm-tiering.md
  - knowledge/adr/ADR-3-memory-architecture-variant.md
  - knowledge/adr/ADR-4-storage-backend.md
  - knowledge/adr/ADR-5-chunker-tool.md
  - knowledge/adr/ADR-6-tool-sandbox-allow-list.md
  - knowledge/research/cutting-edge-agent-research-radar-2026-05.md
  - knowledge/research/semi-autonomous-agents-cross-reference-2026-05.md
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
  - "Coconut"
  - "LSRL"
  - "SoftCoT"
  - "Universal Verifier"
  - "CUAVerifierBench"
  - "Squeeze Evolve"
  - "squeeze-evolve/squeeze-evolve"
  - "Microsoft Research"
  - "Together AI"
  - "UC Berkeley"
  - "Qwen"
  - "vLLM"
confidence: inferred
---

# Latent Space / CUA Verifiers / Squeeze Evolve — разбор трёх paper'ов vs ADR-1..6

> **Статус:** research note, 2026-05-04.
>
> **Что внутри:** критический разбор трёх свежих sources (latent-space survey,
> CUA-verifier paper от Microsoft, Squeeze-Evolve framework от Together AI /
> UC Berkeley) и companion-репозитория squeeze-evolve. Для каждого source —
> summary, mapping на ADR-1..6, принять / отложить / отклонить, вопросы,
> и конкретные артефакты для First-Agent.

---

## 1. Обзор источников

| # | Source | Тип | Авторы | Дата |
|---|---|---|---|---|
| S1 | [arXiv:2604.02029v1](https://arxiv.org/abs/2604.02029v1) | Survey (120+ pp.) | Yu, Chen, He et al. (NUS, Fudan, Tsinghua, DeepWisdom) | Apr 2026 |
| S2 | [arXiv:2604.06240v1](https://arxiv.org/abs/2604.06240v1) | Paper + benchmark | Rosset, Sharma, Zhao et al. (Microsoft Research) | Apr 2026 |
| S3 | [arXiv:2604.07725v2](https://arxiv.org/abs/2604.07725v2) | Paper + code | Maheswaran, Lakhani, Zhou et al. (UC Berkeley, Together AI, Stanford, Princeton) | Apr 2026 |
| S4 | [squeeze-evolve/squeeze-evolve](https://github.com/squeeze-evolve/squeeze-evolve) | Repo (Apache-2.0) | Nietzsche2000, bronyayang | Apr 2026 |

---

## 2. S1 — The Latent Space: Foundation, Evolution, Mechanism, Ability, and Outlook

### 2.1 Суть paper'а

Масштабный survey (~400 цитат) по latent-space computation в language-based
models. Организован вокруг двумерной таксономии:

- **Mechanism axis:** Architecture, Representation, Computation, Optimization.
- **Ability axis:** Reasoning, Planning, Modeling, Perception, Memory,
  Collaboration, Embodiment.

Ключевой тезис: latent space — не скрытый implementation detail, а
*machine-native substrate* для вычислений. Token-level reasoning
(chain-of-thought, ReAct) — лингвистически избыточный; computation
в continuous latent space может быть более compact, expressive и
вычислительно efficient.

### 2.2 Ключевые механизмы (релевантные для First-Agent)

**Latent Reasoning (Coconut, SoftCoT, LSRL):**

- Coconut (Chain of Continuous Thought) — reasoning переносится из
  текстовых токенов в continuous latent states. Вместо `<think>...</think>`
  модель оперирует hidden representations, которые не нужно декодировать
  в текст.
- SoftCoT — аналогичный подход через soft prompting reasoning states.
- Практический результат: **5–10× снижение числа generated tokens** при
  сохранении или улучшении accuracy на reasoning-задачах.

**Latent Memory:**

- MemGen / VisMem — memory retrieval через latent representations вместо
  token-level text search.
- Ключевой insight: latent memory representations сохраняют семантику
  лучше, чем текстовые chunks, особенно при длинных контекстах.

**Latent Collaboration:**

- ThoughtExchange / LatentComm — multi-agent communication через latent
  vectors вместо verbose text messages.
- Снижает bandwidth и reduces hallucination propagation между агентами.

**Latent Planning:**

- ThinkAct / SwiftVLA — планирование в latent space без explicit
  step-by-step verbalization.

### 2.3 Mapping на First-Agent ADR

| Concept из S1 | FA ADR | Fit | Действие |
|---|---|---|---|
| Latent Reasoning (Coconut) | ADR-2 (tiering) | Слабый для v0.1 — требует fine-tuned models | **Отложить.** Мониторить когда OpenRouter/vLLM models начнут поддерживать latent reasoning modes |
| Latent Memory | ADR-3 (mechanical wiki) | Средний — complementary к filesystem-first approach | **Отложить до v0.2.** Mechanical Wiki выбрана intentionally (no embeddings, deterministic). Latent memory — альтернативный path для v0.2 volatile store |
| Latent Collaboration | ADR-1 (UC5 deferred) | Низкий для v0.1 — UC5 deferred | **Записать.** Если UC5 когда-то вернётся, latent comm снижает token costs multi-agent coordination |
| Latent Planning | ADR-2 (Planner role) | Низкий — требует custom model support | **Мониторить.** Интересно если Planner-tier model получит native latent planning |
| Token efficiency gains | ADR-2 (cost routing) | Высокий по direction | **Принять direction.** Latent reasoning подтверждает, что explicit CoT — не единственный path. Confidence signals (см. S3) — более practical proxy сегодня |

### 2.4 Критические вопросы

**Q-S1.1.** Latent reasoning methods (Coconut, SoftCoT) требуют
специально fine-tuned models. Ни один из tier'ов ADR-2 (Qwen 3.6,
Kimi 2.6, GLM 5.1, Claude, Nemotron) пока не предлагает latent
reasoning mode через API. Когда это станет доступно через
OpenRouter/vLLM — нужно пересмотреть token-budget assumptions.

**Q-S1.2.** Latent memory (MemGen-style) — это embedding-based
retrieval. ADR-3 intentionally отложил embeddings до v0.2.
Вопрос: если latent memory representations стабильно лучше FTS5
для code retrieval — это аргумент за ускорение v0.2 volatile store.
Нужна фикстура-тест: 50 queries по project codebase, FTS5 vs
simple embedding recall.

---

## 3. S2 — The Art of Building Verifiers for Computer Use Agents

### 3.1 Суть paper'а

Microsoft Research описывает итеративный процесс создания Universal
Verifier для CUA (Computer Use Agent) trajectories. 4 design principles:

1. **Rubrics с non-overlapping criteria.** Каждый criterion оценивает
   ровно один aspect задачи. Overlapping criteria → noise → disagreement
   между annotators и с ground truth.

2. **Process + Outcome rewards (раздельно).** Process reward: агент делал
   правильные шаги? Outcome reward: результат корректен? Два сигнала
   complementary: агент может делать правильные шаги, но environment
   блокирует; или добиться результата unexpected path'ом.

3. **Controllable vs uncontrollable failures + cascading-error-free scoring.**
   Если агент столкнулся с uncontrollable failure (например, сайт лежит),
   downstream шаги не штрафуются. Cascading-error-free = каждый шаг
   оценивается в контексте «мог ли агент здесь реально повлиять на
   outcome?»

4. **Divide-and-conquer context management.** Длинные trajectory'и
   (десятки screenshots) обрабатываются блоками, а не truncated
   context-window'ом. Каждый блок оценивается independently, результаты
   агрегируются.

**Ключевой результат:** Universal Verifier достигает Cohen's κ ≈ 0.7
(human inter-annotator agreement level). False positive rate снижен
до 1–8% (vs 30%+ у WebVoyager/WebJudge).

**Auto-research finding:** автоматический research-агент достигает
~70% quality эксперта за ~5% времени, но не находит все structural
design decisions. Когда auto-research инициализирован с лучшей
конфигурации эксперта — превосходит peak эксперта. Вывод: **human
expertise + auto-optimization complementary.**

### 3.2 Mapping на First-Agent ADR

| Principle из S2 | FA ADR | Fit | Действие |
|---|---|---|---|
| Non-overlapping rubrics | Acceptance Taxonomy (project convention) | **Высокий.** Directly validates «literal predicates» approach | **Принять.** Формализовать: каждый acceptance criterion в task spec — atomic, non-overlapping |
| Process + Outcome rewards | ADR-2 (Eval role), Radar §5 (eval traces) | **Высокий.** FA уже планирует trace-like eval | **Принять.** Eval role должен оценивать process (tool choice sequence) И outcome (task completion) раздельно |
| Cascading-error-free scoring | ADR-7 (inner-loop, future) | **Высокий.** Критично для inner-loop retry logic | **Принять для ADR-7 prep.** Если tool call fails из-за env issue — не штрафовать subsequent decisions |
| Divide-and-conquer context | ADR-5 (chunker), ADR-4 (FTS5) | **Средний.** Parallel: long-doc retrieval тоже выигрывает от блочной обработки | **Записать.** При будущем eval длинных agent sessions — chunked evaluation, не truncated |
| Human + auto-research complementary | Project workflow | **Высокий.** Validates current pattern: human lead + Devin agent | **Принять direction.** Подтверждает, что auto-research хорош для tuning/expansion, но core structural decisions — за human lead |

### 3.3 Критические вопросы

**Q-S2.1.** Cascading-error-free scoring предполагает classification
каждого failure как controllable / uncontrollable. В First-Agent inner-loop
(ADR-7 prep) — кто классифицирует? Варианты:

- a) Pre-tool hook (из ADR-6 sandbox) проверяет environment availability
  и маркирует failure заранее.
- b) Post-tool hook анализирует error output и классифицирует по taxonomy.
- c) Eval role (отдельный LLM call) классифицирует failure post-hoc.

Рекомендация: **(a)** cheapest и most reliable — environment check до
tool call. Сочетается с ADR-6 sandbox audit log.

**Q-S2.2.** Process + outcome separation предполагает, что мы можем
записывать intermediate tool-call decisions как trace. Это совпадает
с Radar §5 (eval traces) и cross-reference R-3 (structured tool results).
ADR-7 prep должен зафиксировать trace schema, включающий:
`step_id`, `tool_name`, `input`, `output`, `duration_ms`, `error_class`,
`controllable: bool`.

---

## 4. S3 + S4 — Squeeze Evolve: Multi-Model Orchestration

### 4.1 Суть paper'а и репо

Squeeze Evolve — framework для verifier-free evolutionary test-time
scaling с multi-model orchestration. Ключевой принцип:

> **Allocate model capability where it has the highest marginal utility.**

Stronger (дорогие) models — на high-impact stages; cheaper models —
на остальное. Подход решает одновременно diversity collapse и
cost-efficiency.

**Unified evolutionary framework** представляет существующие methods
как instances одного evolutionary loop:

- **Majority Voting** (Wang et al.) = shallow single-step evolution.
- **Recursive Self-Aggregation (RSA)** = verifier-free multi-step evolution.
- **AlphaEvolve** = feedback-driven evolutionary search с verifier.

**Core findings:**

1. **Diversity collapse** — central bottleneck verifier-free evolution.
   Без external correction повторная evolution коллапсирует к narrow modes.
2. **Initialization quality** — strongest predictor of final accuracy.
   Expensive model на init, cheap на recombination — best cost-quality.
3. **Confidence signals** из token log-probabilities = reliable fitness
   proxy. Не нужен отдельный verifier — model's own confidence достаточно
   для routing.
4. **3× cost reduction, 10× throughput** при equivalent or better accuracy.

### 4.2 Squeeze Evolve repo — архитектурные паттерны

Репозиторий [`squeeze-evolve/squeeze-evolve`](https://github.com/squeeze-evolve/squeeze-evolve)
(Python, Apache-2.0) содержит несколько design patterns, интересных
для First-Agent:

**Operator Registry Pattern:**

```python
from squeeze_evolve import fitness

@fitness.register("entropy")
def entropy_fitness(scores, **kwargs):
    p = np.array(scores) / sum(scores)
    return float(-np.sum(p * np.log(p + 1e-10)))
```

Каждый operator (fitness, selection, recombination, evaluation, update)
регистрируется по имени через декоратор. Orchestrator resolves operators
из config strings at initialization. Config — YAML:

```yaml
routing:
  fitness: confidence
  selection: uniform
  recombination: aggregate
  evaluation: exact_match
```

**Confidence-based Routing:**

Per-problem adaptive thresholds at configurable percentiles разделяют
groups на N tiers. Low fitness (hard) → expensive model; high fitness
(easy) → cheap model; full consensus → lightweight non-LLM aggregation.

**N-model support:**

```yaml
routing:
  confidence_percentiles: [33.0, 66.0]  # N-1 thresholds for N models
models:
  - name: cheap-model      # easiest groups
  - name: mid-model        # medium groups
  - name: expensive-model  # hardest groups
```

### 4.3 Mapping на First-Agent ADR

| Concept из S3/S4 | FA ADR | Fit | Действие |
|---|---|---|---|
| Multi-model routing by difficulty | ADR-2 (static role routing) | **Высокий.** ADR-2 = static routing; SE добавляет dynamic confidence-based routing | **Принять direction.** v0.1 — static roles; v0.2 — confidence-based dynamic routing внутри каждого role |
| Confidence from log-probabilities | ADR-2 (models.yaml) | **Средний.** Требует access к logprobs (vLLM: да; OpenRouter: зависит от provider) | **Записать.** Добавить `logprobs: bool` field в models.yaml spec. Если logprobs доступны — использовать как difficulty signal для routing |
| Diversity collapse prevention | ADR-2 (multi-tier) | **Средний.** Для single-pass agent (UC1) не critical; для UC3 multi-retrieval — relevant | **Отложить.** Если FA начнёт iterative refinement (R-3 self-correction), diversity collapse — реальный risk. Мониторить |
| Initialization quality → best predictor | ADR-2 (Planner role) | **Высокий.** Validates: Planner (expensive model) для init, Coder (mid) для execution | **Принять.** Подтверждает: не экономить на Planner tier. Quality планирования определяет quality execution |
| Operator Registry pattern | ADR-7 (tool registry, future) | **Высокий.** Clean, extensible pattern для tool registration | **Принять pattern.** `@registry.register("name")` + YAML config — хороший API surface для FA tool registry |
| YAML config with N-model support | ADR-2 (models.yaml) | **Высокий.** Совпадает с `~/.fa/models.yaml` convention | **Записать.** SE's config structure — reference для models.yaml schema evolution |
| Pluggable evaluation | Radar §5 (eval) | **Высокий.** `@evaluation.register("math_boxed")` — clean eval-plugin pattern | **Принять pattern.** Eval plugins через registry для будущей Eval role |

### 4.4 Критические вопросы

**Q-S3.1.** Squeeze Evolve оптимизирован под math/code benchmarks с
clear correct answers. FA UC1 (coding + PR write) — задача с
*fuzzy acceptance*: PR может быть «correct» по-разному. Confidence
routing работает хуже когда ground truth ambiguous. Вопрос: нужен ли
FA собственный fitness signal, отличный от logprob confidence?
Кандидаты: test-pass rate, lint-pass rate, diff-size heuristic.

**Q-S3.2.** Operator registry pattern в SE — global singletons
(`from squeeze_evolve import fitness`). В FA tool registry — нужен
ли instance-level scoping (per-session tool registry) или global
достаточен? Рекомендация: per-session, потому что ADR-6 sandbox
может restrict tools per-session.

---

## 5. Cross-source синтез: tensions и convergences

### 5.1 Convergences (сходимости)

1. **Multi-tier model allocation работает.** S1 (latent space efficiency
   varies by task complexity), S3 (confidence-based routing), и FA ADR-2
   (static role routing) — три independent signals в одном направлении:
   expensive compute → high-impact decisions, cheap compute → routine.

2. **Structured eval > unstructured judgement.** S2 (rubrics + process/outcome
   separation) и S3 (fitness signals + pluggable evaluation) оба
   показывают, что structured evaluation frameworks значительно
   превосходят ad-hoc judgement. FA Acceptance Taxonomy уже на
   этом пути.

3. **Registry/plugin patterns для extensibility.** S3 (operator registry)
   и S2's iterative development approach оба указывают на: core framework
   должен быть pluggable, не monolithic. FA ADR-7 tool registry →
   следовать этому pattern'у.

4. **Human + AI complementary.** S2 explicit: auto-research agent
   достигает 70% quality за 5% time, но не находит structural insights.
   FA workflow (human lead + Devin) уже в этом pattern'е.

### 5.2 Tensions (напряжения)

1. **Latent vs Explicit reasoning.** S1 advocates latent (token-efficient).
   FA ADR-2/ADR-7 предполагают explicit reasoning traces для audit.
   **Tension:** latent reasoning не оставляет human-readable traces →
   conflict с chain-of-custody principle FA.
   **Resolution:** для v0.1 — explicit traces mandatory (audit > efficiency).
   Для v0.2 — hybrid: latent reasoning с periodic explicit checkpoints.

2. **Static vs dynamic routing.** ADR-2 = static roles (Planner, Coder,
   Debug). S3 = dynamic confidence-based routing. **Tension:** static
   simpler и predictable; dynamic efficient но less auditable.
   **Resolution:** v0.1 static; v0.2 dynamic routing *within* each role
   (не заменяя roles, а выбирая model *tier* внутри role по difficulty).

3. **Verifier-free vs verifier-based.** S3 shows verifier-free evolution
   can match verifier-based on discovery tasks. S2 shows robust verifier
   = critical for training signal. FA UC1 — somewhere in between:
   tests и lint = partial verifier; overall PR quality = fuzzy.
   **Resolution:** FA should treat test-pass + lint-pass as hard verifiers
   и use confidence signals as soft fitness for tool-choice routing.

---

## 6. Артефакты для First-Agent (рекомендации)

### 6.1 Список research-артефактов для усиления репо

| # | Артефакт | Тип | Приоритет | Blocking? | Input от |
|---|---|---|---|---|---|
| A1 | Trace schema для inner-loop eval | ADR-7 prep input | High | Блокирует Eval role design | S2 (process+outcome), Radar §5 |
| A2 | Operator Registry pattern note | Design pattern reference | High | Блокирует tool-registry API | S3/S4 (registry), ADR-7 prep |
| A3 | Acceptance rubric формализация | Convention update | Medium | Не блокирует, но reduces review noise | S2 (non-overlapping criteria) |
| A4 | models.yaml schema evolution note | ADR-2 amendment input | Medium | Не блокирует v0.1 | S3 (logprobs, N-model support) |
| A5 | Eval plugin interface sketch | ADR-7 prep input | Medium | Не блокирует v0.1 | S3 (evaluation registry) |
| A6 | Latent reasoning watch-list | Research radar appendix | Low | Не блокирует | S1 (Coconut, SoftCoT) |
| A7 | Confidence-based routing design note | v0.2 roadmap input | Low | Не блокирует v0.1 | S3 (confidence routing) |
| A8 | CUA Verifier benchmark reference | Research bookmark | Low | Не блокирует | S2 (CUAVerifierBench) |

### 6.2 Конкретные предложения для ближайших PR

**Для ADR-7 prep (inner-loop + tool-registry):**

- Trace schema: включить `step_id`, `tool_name`, `input_hash`,
  `output_summary`, `duration_ms`, `error_class`,
  `controllable: bool`, `model_tier`, `confidence_score`.
  Source: S2 cascading-error-free + S3 confidence signals.

- Tool registry API: `@tool.register("name")` decorator +
  JSON Schema input validation + structured result type.
  Source: S3/S4 operator registry pattern, ADR-2 MCP-shaped convention.

- Eval separation: process reward (was the tool sequence reasonable?)
  + outcome reward (did the task succeed?). Оба — required fields
  в eval output. Source: S2 Universal Verifier.

**Для models.yaml schema (ADR-2 addendum):**

- Добавить optional fields: `logprobs: bool`, `cost_per_1k_tokens: float`,
  `max_concurrent: int`. Source: S3 confidence routing needs logprobs;
  cost field enables budget-aware routing.

**Для Acceptance Taxonomy convention:**

- Каждый acceptance criterion в task description должен быть:
  (a) atomic — оценивает ровно один aspect;
  (b) non-overlapping — не пересекается с другими criteria;
  (c) binary — pass/fail, не graduated scale.
  Source: S2 rubric design principle #1.

---

## 7. Что ещё стоит внимания (авторская оценка)

### 7.1 Squeeze Evolve как reference implementation

Repo squeeze-evolve — один из чистейших примеров pluggable
multi-model orchestration. Даже если FA не будет делать evolutionary
inference, **architectural patterns** (registry, YAML config, N-model
routing, pluggable evaluation) — directly reusable для FA tool registry
и model routing. Стоит сохранить как reference.

### 7.2 Auto-research insight из CUA Verifier paper

Самый неочевидный finding S2: auto-research agent, initialized с
expert configuration, **превосходит** peak эксперта. Это значит:

1. Для FA: после того как human lead принял structural decisions
   (ADR-1..7), AI-agent может заниматься **incremental optimization**
   (parameter tuning, config exploration, edge-case coverage) и будет
   делать это лучше human'а.

2. Практическое следствие: не пытаться заменить human lead на agent
   для architectural decisions. Но после ADR зафиксирован —
   agent > human для tuning.

### 7.3 Latent Space Survey как roadmap

S1 — не actionable для v0.1, но это самый полный mapping
«что latent space может» на domain capabilities. Если в v0.2+
FA начнёт собственный inference pipeline (vLLM self-hosted),
survey — reference для выбора между explicit и latent reasoning
modes по задаче.

Конкретный watch item: **latent memory для code retrieval.**
Если появятся embedding-free latent retrieval methods, совместимые
с FTS5-style deterministic indexing — это natural extension для
ADR-3/ADR-4 mechanical wiki.

---

## 8. Open questions для project lead

**Q1.** A1 (trace schema) — приоритет High. Нужно ли включить его
в ADR-7 prep note или оформить как отдельный mini-ADR?

**Q2.** A2 (Operator Registry pattern) — стоит ли зафиксировать
`@tool.register("name")` как binding convention уже сейчас, до
implementation chunker'а? Это позволит chunker'у сразу
регистрироваться через этот pattern.

**Q3.** A4 (models.yaml schema) — добавить `logprobs`, `cost`, `max_concurrent`
как optional fields в ADR-2 amendment, или отложить до ADR-7?

**Q4.** S1 latent reasoning — ставить ли в v0.2 roadmap explicit
item «evaluate latent reasoning modes when available via API»?

---

## 9. Итоговая radar-таблица

| Source | Accept для v0.1 | Defer до v0.2 | Reject / Monitor |
|---|---|---|---|
| S1 Latent Space Survey | Token efficiency direction | Latent memory, latent collaboration | Latent reasoning (requires custom models) |
| S2 CUA Verifiers | Process+outcome eval separation; cascading-error-free; rubric design; human+AI complementary | Divide-and-conquer long-session eval | CUAVerifierBench as benchmark (different task domain) |
| S3 Squeeze Evolve | Operator registry pattern; initialization quality > routing; YAML N-model config | Confidence-based dynamic routing; diversity monitoring | Evolutionary inference loop (over-engineered for UC1) |
| S4 squeeze-evolve repo | Registry decorator pattern; pluggable eval interface | Full multi-model orchestration | vLLM fork (too heavy for v0.1) |
