---
title: "Bootstrap-cost baseline — post-2026-05 readability refactor"
source:
  - "https://app.devin.ai/sessions/925cf134572d4f9aaa611591d62720d8"
  - "https://app.devin.ai/sessions/0fc8f9b26cf04aec92f598031e0dcf0f"
  - "https://app.devin.ai/sessions/1f41214431bd4c888071b6598c725710"
  - "https://app.devin.ai/sessions/89c32745c44f47dea679af42ed2d2dd8"
compiled: "2026-05-11"
chain_of_custody: |
  Counts (tool calls / files opened / context tokens) are
  self-reported by each Devin session in a BOOTSTRAP REPORT block at
  the end of its session. The session URLs above are the authoritative
  source for chain-of-thought audit; numbers are agent-self-reported
  and may carry accounting error of order ±10 % (see §7 caveats).
goal_lens: "Establish a quantitative bootstrap-cost baseline for Devin sessions on this repo after the 2026-05 readability refactor, so future refactor cycles can measure delta against this datapoint."
tier: stable
links:
  - "../adr/DIGEST.md"
  - "../project-overview.md"
  - "../llms.txt"
  - "../../HANDOFF.md"
mentions:
  - "Devin (Cognition AI)"
confidence: extracted
claims_requiring_verification:
  - "Session B agent self-reported `session_model: Devin (Cognition AI)` rather than `Opus 4.7`, and session C self-reported `Claude (via Devin)` rather than `GPT-5.5`. Selection-labels reflect user-side model choice at session-creation; agent introspection does not consistently return the requested model name."
  - "Session E (66 calls / 51 files) used a two-message prompt with «thoroughly analyze» framing; included only as cautionary datapoint, not part of the baseline range."
---

> **Status:** active. Measurement-evidence note (not a research-briefing
> note — exempt from [AGENTS.md PR Checklist rule #8](../../AGENTS.md#pr-checklist)
> §0 Decision Briefing requirement, which is forward-only for the
> research-briefing workflow). First persistent datapoint for
> [`project-overview.md` §1.1](../project-overview.md#11-четыре-столпа-цели-project-goal--four-pillars)
> Pillar 4 (iteration via measurement).

## 1. Method

**Prompt** (single message, no attachments, sent verbatim to each session):

```text
Read repo MondayInRussian/First-Agent-fork2 until you would be ready to
start work on the next ADR (ADR-7 — inner-loop + tool-registry contract,
per HANDOFF.md §Next steps item 1).

Stop the moment you feel ready, and output exactly the following
report:

================ BOOTSTRAP REPORT ================
tool_calls_total: <int — ALL calls in this session>
  read_calls: <int>
  exec_calls: <int>
  other_calls: <int>

files_opened_count: <int — unique files opened via tool calls>
files_opened_list:
  - <relative path 1>
  ...
(Count only files YOU opened via read/exec; exclude system-injected
content like rules / knowledge notes / blueprint.)

context_tokens_estimate: <int, K tokens — best estimate of total
context-window consumption at this moment>

session_model: <model name>
session_url: <this session's URL>

stopping_reason: <one sentence — why you decided you were ready>
================ END REPORT ================
```

**Stopping criterion.** Model self-declares ready to start drafting
ADR-7. No external constraints on what to read.

**Sessions.** Four Devin sessions on this repo at HEAD
`a1aa74e8c114035d4745216e8745ec82e739346d` (2026-05-11). Labels A / B / C
reflect user-side model selection at session creation; the
`session_model` field in BOOTSTRAP REPORT blocks frequently returns the
generic «Devin (Cognition AI)» rather than the requested model, so
labels are selection-tags, not verified model identity. Session D is
the meta-session that processed this baseline; it ran a different
task-shape (analysis of an improvements file, not ADR-7 prep) and is
included for context only — excluded from the baseline range.

## 2. Results

| # | Selection-label              | Calls  | Reads | Exec | Other | Files | Context (≈) |
|---|------------------------------|--------|-------|------|-------|-------|-------------|
| A | "Sonnet 4 standard"          | 32     | 21    | 3    | 8     | 16    | ~95 K       |
| B | "Opus 4.7"                   | **24** | 12    | 7    | 5     | **7** | ~95 K       |
| C | "GPT-5.5"                    | 43     | 27    | 7    | 9     | 16    | ~80 K       |
| D | "Sonnet 4.5" (different task)| ~22    | ~12   | 2    | ~8    | 7     | ~43 K       |

Session URLs in frontmatter `source:` (D = this PR's authoring session).

## 3. The 7-file post-refactor bootstrap core

All three ADR-7-prep sessions (A, B, C) independently opened the same
seven files:

- `HANDOFF.md`
- `knowledge/llms.txt`
- `knowledge/adr/DIGEST.md`
- `knowledge/adr/ADR-template.md`
- `knowledge/adr/ADR-2-llm-tiering.md`
- `knowledge/research/efficient-llm-agent-harness-2026-05.md`
- `knowledge/trace/exploration_log.md`

Этот набор почти дословно воспроизводит
[`knowledge/llms.txt`](../llms.txt) §MUST READ FIRST top-5 +
TASK ROUTING ADR-7 primary entry + PR Checklist rule #9
(exploration_log + DIGEST). Три независимые модели сошлись на одной
и той же траектории чтения — эмпирическое evidence что routing-
сигналы после 2026-05 refactor'а работают.

## 4. Where models diverged from the core

**A (Sonnet 4)** прочёл core + extended docs (README.md, AGENTS.md,
project-overview.md, glossary.md, adr/README.md, ADR-6) + **3 src/fa
code files** (`chunker/types.py`, `chunker/__init__.py`, `cli.py`).
Inner-loop ADR хочется проектировать с пониманием существующего
scaffold'а — разумный инженерный инстинкт; routing-сигналы такое
поведение не запрещают и не должны.

**C ("GPT-5.5")** прочёл core + extended docs + **3 extra research-
notes** (semi-autonomous, cutting-edge-radar, cross-reference-ampcode
-sliders). TASK ROUTING явно называет efficient-llm-agent-harness
primary, остальные — secondary; C прочёл всех secondary без явной
необходимости (+9 calls vs B при сопоставимом deliverable). CoT длился
3 м 40 с — модель плохо отслеживает свою call-history и тратит
cognitive effort на самопересчёт для report-формата. Это **structural
цена report-format**, не bootstrap-cost.

**B (Opus)** = clean minimum. 7-файловый core, 100 % compliance с
TASK ROUTING. Best-case routing-compliant floor.

## 5. Context-saturation effect

| # | Files | Context (K) | Tokens-per-file |
|---|-------|-------------|-----------------|
| A | 16    | ~95         | ~5.9 K          |
| B | 7     | ~95         | ~13.6 K         |
| C | 16    | ~80         | ~5.0 K          |

Все три сходятся к ~80–95 K context при 2.3× разнице в files-count.
Внутренний «feel ready» threshold ≈ 85 K у Devin-агентов: routing-
signals меняют **что именно** агент читает, не **сколько токенов**
он тратит. Польза refactor'а — в качестве потреблённых токенов
(нужный core), не в их сокращении.

## 6. Baseline range

| Метрика                                       | Значение                              | Источник                  |
|-----------------------------------------------|---------------------------------------|---------------------------|
| Bootstrap-floor (routing-compliant best)      | **24 calls / 7 files / ~95 K**        | Session B                 |
| Bootstrap-typical (A + C range, N = 2)        | **32–43 calls / 16 files / ~80–95 K** | Sessions A + C range  |
| Bootstrap-outlier (deprecated framing)        | 66 calls / 51 files / ~87 K (read)    | Session E — see §8        |

## 7. Caveats

- **N = 1 per cell.** Within-model variance не измерена. Future PR
  может повторить тот же промпт на каждом label'е 2-3 раза для
  оценки variance.
- **No pre-refactor baseline.** Effectiveness claim — structural
  (7-файловый core воспроизводится → routing работает), не
  количественный («стало на N % дешевле»).
- **Model identity not verified.** `session_model` field в BOOTSTRAP
  REPORT — agent self-report; Devin часто возвращает обобщённое
  «Devin (Cognition AI)». Selection-labels = user-side flag.
- **CoT-overhead structural.** GPT-семейство тратит ощутимый CoT
  cost на самопересчёт tool-calls для report-формата (Session C —
  3 м 40 с). Claude-семейство (A, B, D) — заметно лучше. Это
  свойство report-format, не bootstrap-cost.
- **Self-reported context tokens** — приблизительные оценки агентов,
  не authoritative metering. Real cost будет точнее после landing
  UC5 eval-harness (auto-collected metrics).

## 8. Excluded prior measurement (Session E)

Предшествующая сессия (66 calls, 51 files, ~86.5 K tokens read) была
исключена из baseline-диапазона. Причины: two-message prompt-shape с
«thoroughly analyze» framing'ом сместил агента в режим overview
read-all; второе сообщение запрашивало дополнительную статистику и
удвоило tool-calls vs single-message control. Сохранено здесь как
cautionary datapoint — future measurement prompts должны быть
single-message и без «overview / thorough» framing'а в формулировке.

## 9. Re-measurement trigger

Повторить эксперимент when ANY of:

1. [`knowledge/llms.txt`](../llms.txt) §MUST READ FIRST или
   §TASK ROUTING materially changed (more than typo / link fix).
2. [`HANDOFF.md`](../../HANDOFF.md) §60-second bootstrap rewritten.
3. [`knowledge/adr/DIGEST.md`](../adr/DIGEST.md) restructured or
   becomes stale.
4. A new model tier landed in
   [ADR-2](../adr/ADR-2-llm-tiering.md) — re-measure with the new tier.

В остальных случаях этот baseline остаётся valid reference до landing
UC5 eval-harness, который заменит self-reported метрики на auto-
collected traces.
