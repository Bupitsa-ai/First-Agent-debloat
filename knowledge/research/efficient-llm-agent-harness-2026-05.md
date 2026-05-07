---
title: "Efficient LLM agent harness — research note for First-Agent ADR-7 prep"
source:
  - "https://arxiv.org/abs/2603.25723"
  - "https://arxiv.org/html/2603.25723"
  - "https://arxiv.org/abs/2603.28052v1"
  - "https://arxiv.org/html/2603.28052v1"
  - "https://platform.claude.com/docs/en/agents-and-tools/tool-use/tool-search-tool"
  - "https://docs.brightdata.com/ai/mcp-server/tools"
  - "https://www.anthropic.com/news/model-context-protocol"
  - "https://www.anthropic.com/engineering/code-execution-with-mcp"
  - "https://modelcontextprotocol.io/specification/2025-11-25"
  - "https://www.anthropic.com/news/donating-the-model-context-protocol-and-establishing-of-the-agentic-ai-foundation"
  - "user attachment: youtube_transcripts.md (Devin attachment 01215dee-a768-4c7b-88a4-fd92b37f52db, 321 lines, 3 videos)"
  - "../adr/ADR-1-v01-use-case-scope.md"
  - "../adr/ADR-2-llm-tiering.md"
  - "../adr/ADR-3-memory-architecture-variant.md"
  - "../adr/ADR-4-storage-backend.md"
  - "../adr/ADR-5-chunker-tool.md"
  - "../adr/ADR-6-tool-sandbox-allow-list.md"
  - "./how-to-build-an-agent-ampcode-2026-04.md"
  - "./cutting-edge-agent-research-radar-2026-05.md"
  - "./semi-autonomous-agents-cross-reference-2026-05.md"
  - "./agent-roles.md"
  - "./latent-verifier-evolve-research-2026-05.md"
compiled: "2026-05-07"
chain_of_custody: |
  Primary-source facts вытащены напрямую из URL-ов выше: arXiv abstract HTML
  для двух papers (Tsinghua NLAH 2603.25723 и Meta-Harness 2603.28052v1, оба
  March 2026), Anthropic engineering blog (code-execution-with-mcp,
  Nov 04 2025), Claude API docs (tool-search-tool с конкретными API
  identifiers tool_search_tool_regex_20251119 / bm25_20251119), Bright Data
  MCP tools reference (Rapid / Pro modes + 11 групп + GROUPS env var),
  Anthropic MCP launch announcement (Nov 25 2024) и Anthropic donation
  announcement (Dec 09 2025) — все три fetched body-целиком на 2026-05-07,
  закрывают honest-gap из ранней редакции. MCP spec page 2025-11-25
  fetched body-целиком — overview, JSON-RPC base, server/client features
  и Security & Trust секция отражены в §4.8. Числа из YouTube-транскриптов
  (Tsinghua: -0.8 SWE-bench, -8.4 OSWorld, -5.6 multi-candidate; OSymphony
  30.4% → 47.2% при code-to-text migration; Meta-Harness 76.4% TerminalBench-2
  + harness-transferability across 5 held-out models) сверены с arxiv
  abstracts, где это возможно: abstract Meta-Harness подтверждает 7.7-point
  gain + 4× fewer context tokens на online text classification и 4.7
  points average на 200 IMO problems across five held-out models.
  Конкретные ablation-numbers Tsinghua (-0.8 / -8.4 / -5.6) видны только
  в видео-нарративе и помечены как inferred-from-secondary; full text
  paper не парсился построчно (HTML truncated в fetch). Все ADR-факты —
  из локального git checkout fork2 main HEAD на 2026-05-07. Эта нота
  объединяет и улучшает две ранние редакции (PR #37 commit d03f7a3 +
  PR #38 commit 1aec3e2 в upstream `GITcrassuskey-shop/First-Agent`) в
  единый source-of-truth для ADR-7 prep; обе ранние PR закрываются
  лидом без merge — см. §7 Methodology за audit-trail. Атрибуция
  Meta-Harness к Stanford в Video 3 — это video-claim; arxiv submitter
  Yoonho Lee (Stanford ML PhD lineage), DSPy reference в нарративе
  совпадает с Khattab, но affiliation в abstract явно не выписан —
  помечен как unresolved attribution в §8.3.
goal_lens: "Подготовить решения для ADR-7 inner-loop / tool-contract: эффективный First-Agent v0.1 harness без расширения v0.1 scope, с явным cross-reference на ADR-1..6 и primary-source-cited evidence base."
tier: stable
links:
  - "../adr/ADR-1-v01-use-case-scope.md"
  - "../adr/ADR-2-llm-tiering.md"
  - "../adr/ADR-3-memory-architecture-variant.md"
  - "../adr/ADR-4-storage-backend.md"
  - "../adr/ADR-5-chunker-tool.md"
  - "../adr/ADR-6-tool-sandbox-allow-list.md"
  - "./how-to-build-an-agent-ampcode-2026-04.md"
  - "./cutting-edge-agent-research-radar-2026-05.md"
  - "./semi-autonomous-agents-cross-reference-2026-05.md"
  - "./agent-roles.md"
  - "./latent-verifier-evolve-research-2026-05.md"
mentions:
  - "Anthropic"
  - "Bright Data"
  - "Cloudflare"
  - "Tsinghua University"
  - "Harbin Institute of Technology Shenzhen"
  - "Stanford / Khattab / DSPy"
  - "MCP Foundation / Agentic AI Foundation"
  - "Linux Foundation"
  - "Claude Opus 4.6 / 4.7"
  - "Cursor / Codex / Cloud Code"
  - "Manus / Vercel"
  - "Cloudflare Code Mode"
confidence: extracted
claims_requiring_verification:
  - "Tsinghua module-ablation deltas (-0.8 SWE-bench, -8.4 OSWorld, -5.6 multi-candidate search) — взяты из YouTube-нарратива Video 3; abstract на arxiv.org/abs/2603.25723 их не печатает (нужно открывать full HTML / PDF). До того как опираться на эти числа в ADR-7 §Decision, их нужно сверить с Tables 4-6 в paper PDF."
  - "OSymphony code→text migration 30.4% → 47.2% (Tsinghua paper) — также video-claim; abstract упоминает «code-to-text harness migration» как experimental section, но без явных чисел в abstract. Нужна проверка по full paper Section 5."
  - "Meta-Harness «harness optimized on one model transferred to 5 other models, improving them all» — abstract говорит +4.7 points average across five held-out models на math reasoning, что подтверждает transfer на one benchmark family, но НЕ глобальную «works across the model landscape» формулировку из видео. Generalization-claim из видео сильнее, чем из abstract."
  - "Атрибуция Meta-Harness к Stanford — video-claim. arxiv submitter Yoonho Lee, упоминание Khattab/DSPy в Video 3 совпадает с MIT/Stanford lineage, но affiliation в abstract HTML, которое мы получили, явно не выписано. Нужна проверка по full paper title page."
  - "«Manus rewrote harness 5× in 6 months», «Vercel removed 80% of agent tools» — video-claims без primary URL. Не цитировать в ADR без независимой проверки (например, через Manus/Vercel public engineering blogs)."
  - "TOON encoding 30-60% reduction vs JSON для flat tabular data — video-claim, отдельной primary-source URL в наборе нет. Не предлагается для FA, поэтому acceptable as backlog-only fact."
  - "Тezis «verifiers actually start hurting» — основан на Tsinghua ablation. Прямые primary numbers нужно достать из full paper до того, как ADR-2 amendment 2026-04-29 §point 5 («v0.1 inner-loop has no Critic / Reflector role») получит цитату из этой работы."
topic: "harness-engineering, tool-disclosure, mcp-forward-compat, adr-7-prep, gap-analysis-pr37"
---

> **Status:** active. Single source-of-truth для harness research под
> ADR-7 prep. Объединяет и улучшает две ранние редакции из upstream
> `GITcrassuskey-shop/First-Agent` (PR #37 — initial draft, commit
> `d03f7a3`; PR #38 — углублённое продолжение с gap analysis, commit
> `1aec3e2`); обе закрываются лидом без merge при cross-fork sync.
> §7 Methodology фиксирует, какие substantive и methodological
> уроки прежних редакций интегрированы здесь. Произведено через
> [`knowledge/prompts/research-briefing.md`](../prompts/research-briefing.md)
> workflow.

## 0. Decision Briefing

Девять рекомендаций (`R-1..R-9`) для ADR-7 prep — все resolved до
verdict, без выживших `UNCERTAIN-ASK`. R-8 и R-9 ранее (в PR #38)
стояли как UNCERTAIN-ASK; project-lead зафиксировал по обеим Option (i)
— rationale интегрирован в §9 R-8 (static layered prompt + migration
trigger через UC5) и §9 R-9 (ADR-2 evidence-base extension с явным
benchmark-family caveat). Goal-lens см. в frontmatter и §2.

### R-1 — Зафиксировать MCP forward-compat для tool-disclosure (3 уровня) на shape-уровне

- **What:** ADR-7 должен описать tool-disclosure как тройную discriminated-union на уровне registry shape, без runtime: `(a)` server-name list, `(b)` per-tool one-line descriptors, `(c)` full JSON Schema. Это shape, который Anthropic документирует под `defer_loading: true` + `tool_search_tool_*_20251119`, и который Bright Data реализует как `groups` / `tools` env-vars. Цель — чтобы v0.2-переход на BM25-based tool-search или MCP-server distribution был config-only.
- **Project-axis fit (stable across notes):**
  - (A) reduces session-start noise: YES (в первом prompt только descriptors, не full schemas — экономия порядка размера типичного `~55k`-tools-definitions baseline из Anthropic docs).
  - (B) helps LLM find context when needed: YES (pointer-shape: descriptor → schema → tool-call dispatch — три hop вместо одного gigantic upfront blob).
- **Goal-lens fit (per session, dynamic):**
  - (C) advances chosen goal_lens: YES (это **прямое** дополнение к ADR-2 §Amendment 2026-05-01 §point 4 — «ADR-7 inherits convention; MAY add fields but MUST NOT change»; PR #37 §6 это inheritance-constraint не вытащил как hard-rule).
- **Cost:** cheap (это shape-decision на бумаге, ~0 LoC сверх того, что ADR-2 §Amendment уже фиксирует).
- **Verdict:** TAKE
- **If UNCERTAIN-ASK:** n/a
- **Alternative-if-rejected:** ADR-7 фиксирует одну форму (`name + full schema upfront`) и при v0.2 catalog-роста нужно будет ломать `models.yaml` и `~/.fa/sandbox.toml` consumers. Cost-of-rejection: 1-2 дня на v0.2 cleanup vs 0 дней сейчас.
- **Concrete first step (if TAKE):** В ADR-7 §Decision добавить блок `### Tool disclosure tiers` с тремя shape-таблицами и явной ссылкой на ADR-2 §Amendment 2026-05-01 §point 4 («inherits convention»).

### R-2 — Разделить `events.jsonl` (raw machine trace) и `hot.md` (human/agent summary) как два разных артефакта с anti-summary-rot инвариантом

- **What:** ADR-7 должен зафиксировать два независимых на disk-layer артефакта одного session: `~/.fa/state/runs/<run_id>/events.jsonl` (append-only, raw, every tool request/response/permission/decision) и `hot.md` (LLM-readable summary, перезаписываемый). Инвариант: `hot.md` **никогда** не источник для self-evolution; replay/eval/Meta-Harness-style посмертный анализ читают только `events.jsonl`. Это прямой ответ на abstract Meta-Harness paper: «existing text optimizers compress feedback too aggressively».
- **Project-axis fit (stable across notes):**
  - (A) reduces session-start noise: YES (новая сессия читает small `hot.md` для context; raw trace доступен по path, не по pre-load).
  - (B) helps LLM find context when needed: YES (pointer-shape — `hot.md` cite-ит `events.jsonl` paths/byte-offsets; selective `grep`/replay возможны).
- **Goal-lens fit (per session, dynamic):**
  - (C) advances chosen goal_lens: YES (закрывает gap PR #37 §5.3, где raw-traces-vs-summaries упомянуты, но invariant «summary-MUST-NOT-replace-trace» не вытащен как hard-rule на уровне ADR-3 amendment surface).
- **Cost:** medium (trace schema + writer + retention rule + `hot.md` ↔ `events.jsonl` cite-format в существующем ADR-3 hot.md spec).
- **Verdict:** TAKE
- **If UNCERTAIN-ASK:** n/a
- **Alternative-if-rejected:** Хранить только summaries (`hot.md` + handoff); accept Meta-Harness paper-warning о signal loss и допустить, что v0.2 self-evolution / failure-diagnosis работа будет упираться в перепрогон сессий с нуля.
- **Concrete first step (if TAKE):** В ADR-7 §Decision добавить `### Trace separation invariant` с JSON-схемой `events.jsonl` event и формальной формулировкой инварианта; в ADR-3 §Decision добавить one-line amendment-stub: «`hot.md` cite-ит paths в `events.jsonl`; `events.jsonl` — canonical source-of-truth для replay/eval» (или оставить amendment в follow-up ADR).

### R-3 — Reuse SQLite FTS5 (ADR-4) как BM25-движок для будущего tool-search

- **What:** Зафиксировать в ADR-7, что когда v0.1 tool catalog перерастёт ~10 tools, BM25 tool-search реализуется поверх **существующего** `~/.fa/index.sqlite` через тот же FTS5 virtual-table-механизм, который ADR-4 уже выбрал для chunk-retrieval. Описать это явно как «extension point», не как v0.1 feature. PR #37 этот мост не построил (ADR-4 указан в §6 cross-reference, но без явного утверждения «BM25 tool-search reuses ADR-4 SQLite FTS5 без новых deps»).
- **Project-axis fit (stable across notes):**
  - (A) reduces session-start noise: PARTIAL (ADR-7 не реализует tool-search в v0.1; benefit виртуальный, материализуется при росте catalog).
  - (B) helps LLM find context when needed: YES (когда сработает, agent ищет tool по natural-language query через тот же primitive, что и chunks).
- **Goal-lens fit (per session, dynamic):**
  - (C) advances chosen goal_lens: YES (ADR-7 inheritance pattern — пере-использовать существующее ADR-4 решение, а не вводить новую dependency; это subtraction-первый принцип в чистом виде).
- **Cost:** cheap (decision-only; реальная реализация — v0.2 при росте catalog, отдельный PR).
- **Verdict:** TAKE
- **If UNCERTAIN-ASK:** n/a
- **Alternative-if-rejected:** Когда tool-search станет нужен, ввести `rank-bm25` или внешний embedding-сервис как новую dependency; нарушить «zero new deps» обещание ADR-4 §Option B.
- **Concrete first step (if TAKE):** В ADR-7 §Notes добавить one-paragraph ссылку: «If/when tool-search lands, use ADR-4 FTS5 index with `tools` virtual table — no new dependency».

### R-4 — Расширить ADR-6 sandbox: добавить `tool_groups` allow-list по shape Bright Data `GROUPS` env-var

- **What:** Ввести в ADR-6 §Policy file одно дополнительное TOML-секцию `[tool_groups]`, аналогичную `[read]/[write]`, но с allow-list **тегов** на тулзы (`coding`, `git`, `web_search`, `memory`, …). Регистрация тула в ADR-7 registry требует один или больше тегов; ADR-6 sandbox блокирует tool-call, если ни один из тегов тула не входит в `tool_groups.allow`. Это даёт session-level scoping shape, аналогичный Bright Data `GROUPS=ecommerce,finance` env-var, но per-session, не per-server. PR #37 §5.4 упомянул Bright Data только как «design analogy», без конкретного TOML-shape.
- **Project-axis fit (stable across notes):**
  - (A) reduces session-start noise: YES (агент в session-режиме «code-only» не видит описаний `web_search` тулов вообще).
  - (B) helps LLM find context when needed: YES (descriptor-tier registry фильтруется до session-relevant subset на этапе sandbox-check, не на этапе LLM choice).
- **Goal-lens fit (per session, dynamic):**
  - (C) advances chosen goal_lens: YES (это прямое расширение ADR-6 без изменения существующего `[read]/[write]` semantics; ADR-6 §Re-evaluation triggers явно оставляет место для дополнительных allow-lists).
- **Cost:** medium (TOML schema + dispatcher hook + миграция всех future tools на обязательный `tags` field).
- **Verdict:** TAKE
- **If UNCERTAIN-ASK:** n/a
- **Alternative-if-rejected:** Reuse `[read]/[write]` allow-list для tool-scoping (не подходит: filesystem path и tool-name — разные namespaces). Или вводить session-scope в самом ADR-7 registry (получится дубль allow-list-механизма с ADR-6).
- **Concrete first step (if TAKE):** В ADR-7 §Decision добавить «каждый ToolSpec обязан декларировать `tags: list[str]`»; в ADR-6 (или follow-up amendment ADR-6.1) добавить `[tool_groups]` секцию с примером.

### R-5 — Прибить «no Critic / verifier loops в v0.1» с primary-source numbers, а не video-claims

- **What:** ADR-2 §Amendment 2026-04-29 §point 5 уже говорит «v0.1 inner-loop has no Critic / Reflector role». ADR-7 должен сослаться **именно на эту строку** + добавить primary-source numerical evidence из Tsinghua paper (`arXiv:2603.25723`): module-ablation показала, что верификаторы и multi-candidate search активно ухудшают результаты на ряде benchmarks. PR #37 §8 R-5 зафиксировал решение, но опирался на video-нарратив; primary-source citation усиливает аргумент и страхует от reflection-loop fashion в v0.2 review-cycle.
- **Project-axis fit (stable across notes):**
  - (A) reduces session-start noise: YES (no Critic role → no extra system prompt + role config block).
  - (B) helps LLM find context when needed: PARTIAL (меньше LLM-generated critique для search; больше зависимость от deterministic logs).
- **Goal-lens fit (per session, dynamic):**
  - (C) advances chosen goal_lens: YES (закрывает аргументационный gap PR #37 §8 R-5, который cite-ит транскрипт но не arxiv).
- **Cost:** cheap (это формулировка цитаты; numbers нужно сверить с full paper PDF — см. `claims_requiring_verification`).
- **Verdict:** TAKE
- **If UNCERTAIN-ASK:** n/a
- **Alternative-if-rejected:** Ввести always-on Reflector/Critic role в v0.1, измерить (что требует eval-harness, который сам по себе deferred — UC5).
- **Concrete first step (if TAKE):** В ADR-7 §Notes добавить subsection «Why no Critic / verifier in v0.1» со ссылкой на ADR-2 amendment + arxiv link + caveat про verification-of-numbers (см. `claims_requiring_verification` пункт #1).

### R-6 — Defer code-execution-over-MCP в v0.1, **но** оставить registry-shape, который сделает migration config-only

- **What:** Anthropic blog (Nov 2025) показывает 98.7% reduction (150k → 2k tokens) для Google Drive → Salesforce example при code-execution-over-MCP. Это самый сильный efficiency-lever в наборе sources. **Но** он требует sandbox с CPU/time/network limits, redaction для intermediate data, и trustworthy code-execution surface — всё это explicit out-of-scope ADR-6 §Re-evaluation triggers («`run_command` lands → re-evaluate Option C OS-level sandbox»). ADR-7 должен: (a) зафиксировать defer, (b) описать `tools/as_files/<server>/<tool>.ts` export-shape, который будет совместим, когда v0.2 откроет code-execution. PR #37 R-3 это сделал на уровне prose, но без конкретного export-path.
- **Project-axis fit (stable across notes):**
  - (A) reduces session-start noise: YES (v0.2-прибыль; v0.1 nothing changes).
  - (B) helps LLM find context when needed: YES (когда сработает — file-system discovery вместо upfront tool definitions).
- **Goal-lens fit (per session, dynamic):**
  - (C) advances chosen goal_lens: YES (защита forward-compat, как и R-1).
- **Cost:** cheap (decision + 1 abstract directory layout); expensive если строить (~3-5 ADRs: code-execution, sandbox-OS-level, redaction policy, MCP-server-distribution, Skills format). v0.1 не строит.
- **Verdict:** DEFER
- **If UNCERTAIN-ASK:** n/a
- **Alternative-if-rejected:** Строить code-execution-over-MCP сейчас (требует ADR-6 §Option C OS-level sandbox, что по самой ADR-6 «cross-platform cost is high, friction defeats use»; нарушает ADR-1 «pragmatic medium-weight hybrid»).
- **Concrete first step (if DEFER):** В ADR-7 §Notes добавить one-paragraph: «Future code-execution exposes tool registry as filesystem at `~/.fa/state/tools/<server>/<tool>.<ext>`; v0.1 registry produces this layout on demand from ToolSpec descriptors but does not execute. Triggers: `run_command` lands; OS-level sandbox lands; network-redaction lands.»

### R-7 — Зафиксировать subtraction-first ревизионный рубрик ADR-7 как 4-вопросный self-audit

- **What:** Перевести Anthropic «subtraction principle» из видео-нарратива в **executable acceptance-criterion** для будущего ADR-7 review. ADR-7 §Acceptance должен потребовать от реализатора ответить на 4 вопроса (явно из Video 3, последний абзац): (1) что в context-window, что there не нужно? (2) какие tools agent редко использует? (3) есть ли verification/search loops, которые могут вредить? (4) control logic в коде или в тексте? Это превращает design-philosophy в check-list. PR #37 R-1 mentions «subtraction-first» как label, но не как тестируемый рубрик.
- **Project-axis fit (stable across notes):**
  - (A) reduces session-start noise: YES (рубрик прямо штрафует context-bloat).
  - (B) helps LLM find context when needed: PARTIAL (вторичный эффект — меньше шума, лучше подбор; первичный эффект на A).
- **Goal-lens fit (per session, dynamic):**
  - (C) advances chosen goal_lens: YES (это процедурный механизм; работает как `claims_requiring_verification` для дизайн-решений).
- **Cost:** cheap (это §Acceptance block в ADR-7).
- **Verdict:** TAKE
- **If UNCERTAIN-ASK:** n/a
- **Alternative-if-rejected:** Полагаться на review-PR culture, что reviewer сам спросит «зачем эта компонента». Рискованно — Tsinghua/Stanford evidence в abstract Meta-Harness прямо говорит, что harness-design-by-default-add превращается в 14× compute waste без user-visible benefit (16.3M vs 1.2M tokens per sample на одинаковом результате SWE-bench Verified, see §4.1).
- **Concrete first step (if TAKE):** В ADR-7 §Acceptance добавить четыре пункта чек-листа дословно из Video 3.

### R-8 — Стратегия system-prompt assembly + prefix-cache invariant: static layered prompt (Option (i))

- **What:** Video 2 (component #7 «System prompt assembly») явно предупреждает: dynamic compose system-prompt из `agents.md` / `cloud.md` / `hot.md` каждый turn ломает prefix-caching на стороне provider. ADR-7 фиксирует **static layered prompt**: один system-prompt файл собирается один раз при старте session, замораживается на всю session, провайдер кэширует префикс; dynamic state (`hot.md`, current task, files of interest, handoff cite) идёт в первом user-сообщении (Anthropic-side это тоже кэшируется до `user_turn[1]`, но это ортогонально выбору (i)).
- **Project-axis fit (stable across notes):**
  - (A) reduces session-start noise: YES (static prefix cache-hit'ится; dynamic-state однократно в первом user-msg).
  - (B) helps LLM find context when needed: YES (frozen prompt всегда содержит правильный набор anchors).
- **Goal-lens fit (per session, dynamic):**
  - (C) advances chosen goal_lens: YES (закрывает prompt-assembly + prefix-cache invariant как hard rule для ADR-7).
- **Cost:** cheap (это shape-decision на бумаге для v0.1; runtime cost — выигрыш от prefix-cache).
- **Verdict:** TAKE — Option (i) static layered prompt
- **If UNCERTAIN-ASK:** n/a (project-lead резолвил 2026-05-07)
- **Alternative-if-rejected:** Option (ii) two-segment assembly (provider-зависимый partial prefix-cache, Anthropic-yes / OpenRouter-variable / vLLM-yes); Option (iii) no assembly + retrieval-only (риск default conventions не загружены).
- **Concrete first step (if TAKE):** В ADR-7 §Decision добавить subsection `### Prompt assembly` с формальным invariant: «system-prompt собирается один раз при старте session и не пересобирается в ходе session; при изменении AGENTS.md или ADR amendments — новая session» + migration trigger для v0.2: UC5 metrics покажут ≥ N% degradation на staleness-sensitive tasks.

### R-9 — Harness-transferability claim как evidence-base extension для ADR-2 (Option (i))

- **What:** Abstract Meta-Harness paper говорит: «retrieval-augmented math reasoning, single discovered harness improves accuracy on 200 IMO-level problems by 4.7 points on average across five held-out models». ADR-2 §Decision выбирает static role routing (один harness, разные модели per role); harness-transferability фиксируется как одна из причин этого выбора через future ADR-2 «evidence base extended» amendment-stub. С явным caveat: «verified for one benchmark family per Meta-Harness abstract».
- **Project-axis fit (stable across notes):**
  - (A) reduces session-start noise: NO (это design-decision evidence-base, не context-noise).
  - (B) helps LLM find context when needed: NO (не про context).
- **Goal-lens fit (per session, dynamic):**
  - (C) advances chosen goal_lens: PARTIAL (это про ADR-2 evidence-base, не напрямую ADR-7; но укрепляет cross-reference quality, чего ранние редакции не делали).
- **Cost:** cheap (research-only; one-paragraph amendment-stub в ADR-2).
- **Verdict:** TAKE — Option (i) ADR-2 evidence-base extension с caveat
- **If UNCERTAIN-ASK:** n/a (project-lead резолвил 2026-05-07)
- **Alternative-if-rejected:** Option (ii) — только пометить в `claims_requiring_verification` без ADR-цитаты (cost: zero, но слабее обосновывает ADR-2 §Decision-form). Option (iii) — trigger для нового research note о cross-model harness стабильности (cost: medium, deferred).
- **Concrete first step (if TAKE):** При ближайшем amendment ADR-2 добавить one-paragraph «Evidence base extended (2026-05-07)» со ссылкой на Meta-Harness abstract + caveat «verified for one benchmark family» + ссылкой на эту ноту §4.2.

### Сводная таблица

| R-N | Verdict | Project-fit (A / B) | Goal-fit (C) | Cost | Alternative-if-rejected | User decision needed? |
|-----|---------|---------------------|--------------|------|--------------------------|------------------------|
| R-1 | TAKE | YES / YES | YES (ADR-7 inherits ADR-2) | cheap | Re-design at v0.2 | No (TAKE) |
| R-2 | TAKE | YES / YES | YES (anti-summary-rot) | medium | Summary-only memory | No (TAKE) |
| R-3 | TAKE | PARTIAL / YES | YES (ADR-4 reuse) | cheap | New BM25 dependency | No (TAKE) |
| R-4 | TAKE | YES / YES | YES (ADR-6 extension) | medium | Reuse path-allow-list (mismatch) | No (TAKE) |
| R-5 | TAKE | YES / PARTIAL | YES (primary-cite hardening) | cheap | Always-on Critic | No (TAKE) |
| R-6 | DEFER | YES / YES | YES (forward-compat) | cheap now / expensive if built | Build now (violates ADR-1, ADR-6) | No (DEFER) |
| R-7 | TAKE | YES / PARTIAL | YES (procedural) | cheap | Rely on review culture | No (TAKE) |
| R-8 | TAKE Option (i) | YES / YES | YES | cheap | Option (ii) two-segment / (iii) no-assembly | No (resolved 2026-05-07) |
| R-9 | TAKE Option (i) | NO / NO | PARTIAL | cheap | Option (ii) `claims_requiring_verification`-only | No (resolved 2026-05-07) |

## 1. TL;DR

- Эта нота — single source-of-truth для harness research под ADR-7 prep, объединяющая две ранние редакции (PR #37 + PR #38) в единый cross-reference на ADR-1..6 с 9 резолвленными рекомендациями (R-1..R-9; 8 TAKE + 1 DEFER, ни одного выжившего UNCERTAIN-ASK).
- Tsinghua paper (`arXiv:2603.25723`, *Natural-Language Agent Harnesses*, March 2026) и Meta-Harness paper (`arXiv:2603.28052v1`, March 2026) **резолвятся**; abstract обоих парсится. Атрибуция Meta-Harness к Stanford из Video 3 — partial: arxiv submitter Yoonho Lee, нарратив указывает на Khattab/DSPy lineage, но affiliation в abstract HTML явно не выписан (см. §8.3).
- Anthropic engineering blog «Code execution with MCP» (Nov 04, 2025) даёт 98.7% reduction (150k → 2k tokens) на конкретном Google-Drive-→-Salesforce example. Это самый сильный efficiency-lever в наборе и одновременно самый рискованный для FA v0.1 (требует sandbox, redaction, OS-level isolation — всё out-of-scope per ADR-6 §Re-evaluation triggers). R-6 = DEFER, но добавлен явный export-path forward-compat shape.
- Tool-search API в Claude API docs (`tool_search_tool_regex_20251119` / `_bm25_20251119`) — это November 2025 stable shape, не proposal; FA имеет zero-new-deps мост к нему через ADR-4 SQLite FTS5 (R-3).
- Bright Data MCP `GROUPS` env-var + `tools` env-var — конкретная workable форма для ADR-6 расширения на tool-tag allow-list (R-4); design analogy из ранней редакции конкретизирована до TOML-shape.
- **MCP-compat ≠ context-efficiency.** MCP задаёт ecosystem standard boundary (hosts/clients/servers; resources/prompts/tools; JSON-RPC; capability negotiation; user consent + tool safety, см. §4.7–4.8); но следование MCP-shape не гарантирует token-efficient context. ADR-2 amendment 2026-05-01 правильно выбрал *MCP shape, not MCP transport*; ADR-7 должен сохранить это разделение и добавить progressive disclosure (R-1) поверх.
- Subtraction-principle (Anthropic) переведён в R-7 как 4-вопросный self-audit acceptance-block для ADR-7. Это разница между «design-philosophy в ноте» и «testable рубрик в ADR».
- R-8 (system-prompt assembly + prefix-cache) и R-9 (harness-transferability claim) резолвлены project-leadом как TAKE Option (i): static layered prompt (frozen at session start) + ADR-2 evidence-base extension с caveat «verified for one benchmark family». ADR-7 готов к written drafting (§10 contains contract sketch).
- ADR-7 contract sketch (§10) — фактическая выжатие всех R-1..R-9 в single read-friendly artifact: ToolSpec / ToolResult / Trace pseudo-schema + prompt-assembly invariant + acceptance-block.

## 2. Scope, метод, goal_lens (verbatim)

**Goal-lens (verbatim из frontmatter):**

> «Подготовить решения для ADR-7 inner-loop / tool-contract: эффективный First-Agent v0.1 harness без расширения v0.1 scope, с явным cross-reference на ADR-1..6 и primary-source-cited evidence base.»

Это вариант (c) из default options в `knowledge/prompts/research-briefing.md` Stage 1 (cross-reference vs accepted ADR-1..6), расширенный явным требованием primary-source-cited evidence base. Эта нота интегрирует две ранние редакции в единый source-of-truth (§7 «Методология / lessons from prior drafts» фиксирует audit-trail).

**Метод.** Five-stage workflow из `knowledge/prompts/research-briefing.md`:

1. **Stage 1 — Goal-lens elicitation.** В предыдущем сообщении пользователь подтвердил вариант (c) implicitly («cross-reference new note with existing adr's» + «find that is not researched deeply enough or skipped»). Уточнили формулировку до form в frontmatter; пользователь не блокировал.
2. **Stage 2 — Source ingestion.** Прочитаны: full attachment `youtube_transcripts.md` (3 видео, 321 line); arxiv abstract HTML обоих papers; полный текст Anthropic `code-execution-with-mcp` blog; Claude API tool-search docs (через quick-start + how-it-works section); Bright Data tools reference (Rapid/Pro/groups + 11 групп); Anthropic MCP launch announcement (Nov 25 2024); Anthropic donation announcement (Dec 09 2025); MCP spec page (2025-11-25, fetched body-целиком на 2026-05-07); полные тексты обеих ранних редакций (PR #37 commit `d03f7a3` + PR #38 commit `1aec3e2`); review-комментарии PR #37; ADR-1..6 с amendments; adjacent research notes (`how-to-build-an-agent-ampcode-2026-04.md` head + frontmatter; `cutting-edge-agent-research-radar-2026-05.md` head; `semi-autonomous-agents-cross-reference-2026-05.md`).
3. **Stage 3 — Relevance gate.** Соответствие goal_lens проверено явно: каждый источник ≥ один ADR cross-reference либо одна R-N рекомендация.
4. **Stage 4 — Deep-dive note + Decision Briefing.** Этот файл. Объединение PR #37 + PR #38 выполнено по PR #38 базе (9 рекомендаций + cross-reference depth) + 3 инъекции из PR #37 (ADR-7 contract sketch §10, NLAH text-half synthesis §3, MCP-boundary nuance §6.2); §7 переписан как «Методология / lessons from prior drafts» (без ghost line-references на never-merged file).
5. **Stage 5 — Chat handover.** §0 Сводная таблица постится verbatim в чате при создании PR. UNCERTAIN-ASK нет (R-8 и R-9 резолвлены project-leadом).

**Не-делается этой нотой.** Не пишется сам ADR-7. Не реализуется inner-loop. Не модифицируются PR #37 и PR #38 (обе закрываются лидом без merge при cross-fork sync). Не изменяются существующие ADRs (только cross-reference).

## 3. Key concepts (one-line definitions, source-language terms preserved)

- **Harness** — fixed architecture, превращающая raw LLM в agent; while-loop + tool-registry + permission-layer + state. Source: Video 2 определение, согласовано с Tsinghua paper Introduction («the surrounding harness: the control stack…»).
- **Framework** (vs harness) — даёт абстракции (chains, retrievers, state-graphs), требует assembly от человека-архитектора. Harness — *уже собранный* агент, человек предоставляет goal. Source: Video 2.
- **Natural-Language Agent Harness (NLAH)** — externalized, editable, executable natural-language artifact, описывающий harness behavior; runtime — Intelligent Harness Runtime (IHR). Source: Tsinghua paper abstract + Section 1.
- **Meta-Harness** — outer-loop система, оптимизирующая harness-код через agentic-proposer, читающий source/scores/traces всех prior candidates через filesystem. Source: Meta-Harness abstract.
- **Subtraction principle** — design-rule: каждая harness-component encode-ит assumption о том, чего модель не может сама; assumptions expire когда модели улучшаются. Не добавлять компоненты по умолчанию; удалять, когда измерения показывают неэффективность. Source: Anthropic, через Video 3 нарратив.
- **MCP (Model Context Protocol)** — JSON-RPC-shaped open standard для agent ↔ tool/resource/prompt boundary; запущен Anthropic Nov 2024, по состоянию на 2025-11-25 spec — текущая stable. Source: Anthropic announcement + spec URL.
- **Tool descriptor (tier 2 disclosure)** — short metadata о тулзе (name + 1-line description + permissions/tags) без full JSON Schema; Anthropic API представляет это через `tool_reference` blocks. Source: Claude tool-search docs.
- **`defer_loading`** — флаг в Claude API tool list, помечающий тулз как «не загружать в context до явного запроса через tool-search». Source: tool-search docs (config example).
- **Tool group (Bright Data)** — pre-configured collection тулзов под domain (`ecommerce`, `finance`, `social_media`, …); 11 групп в Bright Data MCP server; селекция через `groups=` URL param или `GROUPS=` env-var. Source: Bright Data tools docs §«Modes: Rapid (Free), Pro and 11 tool groups».
- **Custom tool list (Bright Data)** — `tools` env-var, перечисляющий точные tool-имена; load-list ровно эти, ничего другое. Source: Video 1 + Bright Data docs.
- **Code execution with MCP / Code Mode (Cloudflare)** — паттерн «MCP server as filesystem of tool-files, agent reads only what needed, intermediate data в execution env, model видит только final output». 98.7% reduction в Anthropic example. Source: Anthropic engineering blog Nov 04 2025.
- **Programmatic tool calling** — Anthropic API feature, ортогональная к code-execution-over-MCP: Claude пишет Python, вызывающий тулзы напрямую как функции; intermediate-результаты не входят в context. Caveat: MCP-connector tools currently не могут быть programmatically called. Source: Video 1 + Anthropic docs.
- **Prefix caching** — провайдер-side оптимизация: если префикс system-prompt идентичен между вызовами, сервер переиспользует KV-cache. Динамическая компоновка system-prompt (file-injection из ancestor dirs) ломает кэш. Source: Video 2 component #7 explicit warning.
- **Context compaction** — harness-механизм: при достижении ~80-90% context-limit harness summarize-ит старые сообщения, оставляя последние verbatim. Cloud Code: 200k → 1M token window с compaction триггером ~80%. Source: Video 2 component #2.
- **Lifecycle hooks (pre-tool / post-tool)** — extensibility-точки в harness: pre-tool fires до execution, может allow/deny/modify; post-tool fires после, для audit/logging. JSON exit-code-protocol. Source: Video 2 component #8 + Anthropic Cloud Code prior art.

**NLAH ↔ code-half synthesis (project-specific).** First-Agent уже использует
`AGENTS.md`, prompts, ADRs, research notes и `HANDOFF.md` как исполнимый для
agents natural-language слой — то есть **text half of NLAH у нас уже есть**.
Из этого следует: ADR-7 не должен вводить generic IHR runtime (отвлекло бы
от Phase M), а должен стать **мостом к code-half** через минимальный inner-loop
contract: tool-registry shape, sandbox pre-tool hook, trace folder shape,
pre-tool validation gates. Generic NLAH-runtime — out-of-scope для v0.1; v0.1
inner-loop исполняет текущий natural-language harness layer через простой
deterministic Python harness вокруг провайдер-API. Этот synthesis перенесён
из ранней редакции (PR #37 §5.2), потому что прямо влияет на scope ADR-7.

## 4. Primary-source numbers (sweep)

Числа из ранних редакций часто помечались как «from transcript, secondary». Ниже sweep по primary URL-ам, которые **резолвятся** на 2026-05-07.

### 4.1 Tsinghua NLAH paper (`arXiv:2603.25723`)

Прямо из abstract:

> «Across coding and computer-use benchmarks, we conduct controlled evaluations of operational viability, module ablation, and code-to-text harness migration.»

Из Section 1 (Introduction):

- Постановка: «Modern agents increasingly succeed or fail because of the surrounding harness: the control stack that structures multi-step reasoning, tool use, memory, delegation, and stopping beyond any single model call.»
- Cite-ит ReAct (Yao 2023), RAG (Lewis 2021), Reflexion (Shinn 2023), память + self-evolution (Zhang 2026), workflow-generation, multi-agent orchestration (Magentic-One Fourney 2024 / Wang 2025b / другие 2026).

Числа из Video 3, **которые НЕ видны в abstract** (нужна верификация по full paper PDF — см. `claims_requiring_verification`):

- SWE-bench Verified, GPT-5.4 max reasoning: full harness ≈ **74-76%** result rate, **16.3M prompt tokens / sample**, **600+ tool calls**, **32 min runtime**.
- Stripped-down: **1.2M tokens, 51 tool calls, <7 min**, same destination → **14× compute waste без user-visible benefit**.
- Module-ablation deltas: self-evolution = consistently helpful; verifiers ≈ **−0.8 SWE-bench, −8.4 OSWorld**; multi-candidate search ≈ **−5.6**.
- OSymphony native-code → NL-representation migration: **30.4% → 47.2%** (+16.8 pp); runtime **361 min → 41 min** (−9×); LLM calls **1200 → 34** (−97%).

**Эпистемический статус.** Эти числа цитируются в Video 3 нарративом, согласуются с abstract («module ablation, code-to-text harness migration» — explicit experimental sections), но конкретные delta-цифры — secondary до проверки paper PDF. Меня **тем не менее** интересует общий signal-direction (verifiers harm, NL-representation helps), который даже в weak форме поддерживает R-5 и R-7.

### 4.2 Meta-Harness paper (`arXiv:2603.28052v1`)

Прямо из abstract (verbatim):

- «Meta-Harness improves over a state-of-the-art context management system **by 7.7 points** while using **4x fewer context tokens**» — на online text classification.
- «A single discovered harness improves accuracy on 200 IMO-level problems **by 4.7 points on average across five held-out models**» — это primary-source поддержка transferability-claim в R-9.
- «On agentic coding, discovered harnesses surpass the best hand-engineered baselines on TerminalBench-2.» — без конкретного числа в abstract.
- «existing text optimizers are poorly matched to this setting because they **compress feedback too aggressively**» — **прямая** primary-source опора для R-2 (raw `events.jsonl` ≠ `hot.md` summary).

Числа из Video 3, которые **не** в abstract:

- 76.4% на TerminalBench-2.
- 10M tokens/iteration, 400× больше feedback чем prior methods.
- ≈82 файлов читается per round.
- Без raw traces: accuracy 50% → 34%; с summaries вместо traces: 34.9%.
- 76.4% — best-in-class; +7.7 points над SOTA на 215 text classification tasks (примечание: цифра 215 vs «text classification system» в abstract — расхождение, см. §8.4).

### 4.3 Anthropic Code execution with MCP (Nov 04, 2025)

Прямо из blog:

- Google-Drive-→-Salesforce example: **150,000 tokens → 2,000 tokens (98.7% reduction)**.
- Cloudflare публиковала аналогичный паттерн под именем «Code Mode» — **«weeks earlier»** (Video 1 нарратив; primary-source confirmation в самом блоге: «Cloudflare published similar findings, referring to code execution with MCP as 'Code Mode'»).
- Tool-as-file layout: `servers/<server>/<tool>.ts` + `index.ts`.
- Filtering 10,000-row spreadsheet в execution-env → агент видит 5 rows вместо 10,000.
- Privacy: «intermediate results stay in the execution environment by default».
- Каюат: **«tools provided through an MCP connectors cannot currently be called programmatically»** (Video 1) — ortogonal к code-execution feature, но релевантно для R-6 design-shape.

### 4.4 Tool search tool docs (Claude API)

Прямо из docs:

- API identifiers: `tool_search_tool_regex_20251119` (regex variant) и `tool_search_tool_bm25_20251119` (BM25 natural-language variant). **Ноябрь 19, 2025** — это API-version date, что подтверждает, что фича GA с ноября 2025.
- Multi-server setup baseline: **«can consume ~55k tokens in definitions before Claude does any actual work»**.
- Reduction: **«typically reduces this by over 85%»**, остается «3-5 tools Claude actually needs for a given request».
- Tool selection accuracy degradation **«once you exceed 30-50 available tools»**.
- Mechanism: `defer_loading: true` per-tool; Claude видит только tool-search-tool + non-deferred tools initially.
- Возвращает `tool_reference` blocks, которые автоматически expand-ятся в full tool definitions при использовании.
- Compatibility: ZDR-eligible. Bedrock — invoke API only, **не** converse.

### 4.5 Bright Data MCP tools docs

Прямо из docs:

- **2 modes**: Rapid (Free) и Pro.
- **11 групп**: e-commerce, social_media, browser_automation, business_intelligence, finance, research, app_stores, travel, advanced_scraping, geo_llm_visibility, code.
- **>60 tools** total в pro-mode + groups.
- Configuration shape:
  - Remote MCP: `&pro=1` URL param + `&groups=<list>` URL param.
  - Local MCP: `PRO_MODE=true` env-var + `GROUPS=<list>` env-var.
- Custom tool list: `tools` env-var (Video 1; в видимой части docs не явно, но Video 1 цитирует докуу).
- 5000 requests/month free tier; OSS под MIT license.

### 4.6 MCP donation / Agentic AI Foundation (`anthropic.com/news/donating-the-model-context-protocol-and-establishing-of-the-agentic-ai-foundation`)

Fetched body-целиком на 2026-05-07. Дата публикации — December 09, 2025.

Прямые governance-факты из страницы:

- MCP переходит под **Agentic AI Foundation (AAIF)** — directed fund под Linux Foundation; co-founded Anthropic, Block, OpenAI; «vendor-neutral home for the Model Context Protocol and other open standards in agentic AI».
- Adoption-numbers (Anthropic-cited, на дату публикации): **>10 000** active public MCP servers; **75+** Claude connectors built на MCP; **97M+** monthly SDK downloads across Python / TypeScript / C# / Java / Kotlin / Ruby / Go / Rust / Swift / PHP.
- Tool Search Tool + Programmatic Tool Calling — **production-API features в Claude API** (не proposal); это та же shape, что в §4.4 tool-search docs.
- Spec `2025-11-25` — referenced как latest stable spec on launch of foundation.

**FA-relevance.** Foundation transition снижает риск Anthropic-side breaking-changes на JSON-RPC-shape; ADR-2 §Amendment 2026-05-01 (MCP forward-compat tool-shape) — это правильная ставка с governance-перспективы. Adoption-числа дают **forward-compat обоснование** R-1 progressive disclosure: tool-disclosure pattern уже принят >10k серверами, plug-in shape стабилен.

### 4.7 MCP launch announcement (`anthropic.com/news/model-context-protocol`)

Fetched body-целиком на 2026-05-07. Дата публикации — November 25, 2024 (исходный launch).

Ключевые primary-source факты:

- Постановка проблемы: **«models trapped behind information silos and legacy systems»**; интеграция тулзов до MCP — fragmented; каждая новая интеграция — custom code.
- MCP как universal standard, **JSON-RPC-shaped**, с тремя ролями: hosts (Claude Desktop, IDEs), clients (host-side connectors), servers (tool/resource/prompt providers).
- Early adopters: **Block, Apollo**.
- Dev-tool adoption: **Zed, Replit, Codeium, Sourcegraph** — все интегрировали MCP в свои продукты на launch.

**FA-relevance.** Это foundational primary-source для термина MCP в §3 Key concepts; resolves «что такое MCP» от video-narrative до Anthropic-cited shape. ADR-2 §Amendment 2026-05-01 правильно опирается на JSON-RPC shape (точное определение из launch).

### 4.8 MCP spec `2025-11-25` (`modelcontextprotocol.io/specification/2025-11-25`)

Fetched body-целиком на 2026-05-07. Дата spec — November 25, 2025 — **позже** API tool-search idents (`_20251119`), что нормально (доки могут отставать на дни).

Spec overview-факты, которые прямо влияют на ADR-7:

- **Base protocol:** JSON-RPC; stateful connections; capability negotiation между сервером и клиентом.
- **Server features:** Resources (context/data для LLM); Prompts (templated messages/workflows для users); Tools (functions LLM может выполнить).
- **Client features:** Sampling (server-initiated agentic поведение); Roots (server query-у-client о filesystem boundaries); Elicitation (server request-у-user о additional information).
- **Security & Trust principles** (явно перечислены в spec): User Consent and Control, Data Privacy, Tool Safety, LLM Sampling Controls.

**FA-relevance.** Server-features triplet (Resources / Prompts / Tools) — это **более широкий surface**, чем то, что ADR-2 §Amendment покрывает (только Tools). Resources и Prompts — это extension point, который ADR-7 может optionally упомянуть как future-compat shape, не реализуя их в v0.1. Client-features (Sampling, Roots, Elicitation) — все out-of-scope для v0.1, но Roots интересен для ADR-6 (path allow-list) — это shape, в которой server queries client о allowed filesystem boundaries; First-Agent's `~/.fa/sandbox.toml` — это в принципе same shape inverted (client tells server). Security & Trust **точно совпадает** с ADR-6 deny-by-default principle.

## 5. Tool-disclosure design space — пять паттернов и их ADR-fit

Этот раздел расширяет PR #37 §5.4 mapping-таблицу до **shape-уровня** и явного ADR-фит-вердикта. PR #37 предложил «descriptor → schema» split в одном предложении (§5.4 row «Dynamic context loading»), но не разобрал каждый pattern против каждого ADR.

### 5.1 Pattern A — Group/scope loading (Bright Data shape)

**Mechanism.** Tools регистрируются с tag/group. Сессия указывает один или больше groups; runtime фильтрует registry до tools, чьи tags пересекаются с whitelist. Никакой LLM-side дискавери.

**FA-fit:**

- **ADR-6 (sandbox).** Прямо подходит как extension: новая `[tool_groups]` секция в `~/.fa/sandbox.toml`. Cost: medium. См. R-4.
- **ADR-2 (MCP shape).** Совместимо. ADR-2 §Amendment 2026-05-01 §point 1 фиксирует JSON-RPC shape; tags — это metadata-поле, ортогональное к request/response shape.
- **ADR-7 (future).** Регистрационная форма ToolSpec обязана включать `tags: list[str]` field.

**Вердикт.** Take в R-4. Это самая cheap-дешёвая форма tool-scoping; работает на startup time, не на per-turn LLM cost.

### 5.2 Pattern B — Custom tool list (Bright Data `tools` env)

**Mechanism.** Жёсткая spec точных tool-names. Production-mode «I know exactly what I want».

**FA-fit:**

- Подходит для repeatable recipe / Skills / sub-agent ролей.
- Pattern A (groups) и B (custom tools) **композируются**: групповой allow-list плюс session-override на список имён.

**Вердикт.** Subset of Pattern A; не самостоятельная рекомендация. Реализуется через комбинацию `tool_groups.allow` + per-session command-line override (analog to ADR-6 «`fa --sandbox-allow-once`»).

### 5.3 Pattern C — Tool-search (BM25/regex, Anthropic shape)

**Mechanism.** Few core тулзы + dedicated search-tool. Catalog индексирован (BM25 или regex). LLM запрашивает search; runtime возвращает 3-5 `tool_reference`; full schemas разворачиваются on demand.

**FA-fit:**

- **ADR-4 (storage).** SQLite FTS5 уже выбран и реализует BM25 native. Tool-search reuse-ит тот же primitive. Cost: cheap при v0.2 росте catalog. См. R-3.
- **ADR-2 (MCP shape).** ADR-2 §Amendment 2026-05-01 §point 4 разрешает «MAY add fields» — добавление `tool_reference`-shape результата как новой response-form **не** ломает MUST-NOT-change clause.
- **ADR-7 (future).** В v0.1 — out-of-scope (catalog маленький, ~5-10 tools); shape-плейсхолдер описать в ADR-7 §Notes как extension point.

**Вердикт.** TAKE shape, не реализация. См. R-3.

### 5.4 Pattern D — Dynamic 3-level disclosure

**Mechanism.** Уровни (1) server-name list, (2) per-tool 1-line descriptors, (3) full schema. Combinable с Pattern A / C.

**FA-fit:**

- Это **самый общий** shape; A/C — частные случаи.
- Прямое выражение: registry экспонирует три API endpoint'а `list_servers()` / `list_tools(server)` / `describe_tool(server, tool)`. Все три — JSON-RPC-shaped per ADR-2.
- v0.1 может экспонировать только descriptor-tier (~5-10 tools, маленький), без LLM-search; full schema — on-demand при `dispatch(name, params)`.

**Вердикт.** TAKE как **canonical** shape. R-1 в §0.

### 5.5 Pattern E — Code-execution-over-MCP / Programmatic tool calling

**Mechanism.** Две подкатегории. (E1) Tools-as-files: `servers/<name>/<tool>.ts`; agent читает только нужные file-ы (filesystem-discovery). (E2) Programmatic tool calling: agent пишет Python/JS код, вызывающий tools; intermediate-результаты в execution env, не в context.

**FA-fit:**

- v0.1 — out-of-scope. Требует:
  - OS-level sandbox (ADR-6 §Option C, текущая acceptance: «`run_command` lands → re-evaluate»).
  - Network/CPU/time limits (вне scope ADR-6, потребует new ADR).
  - Redaction policy (intermediate данные не должны попадать в model context — explicit policy required).
  - Allow-list для shells/binaries.
- v0.2-shape: каждый ToolSpec может **автоматически** генерировать `tools/as_files/<server>/<tool>.<ext>` адаптер. v0.1 не реализует генерацию, но **может** зарезервировать filename-pattern.

**Вердикт.** DEFER в R-6, но с явным forward-compat shape.

### 5.6 Свод «pattern × ADR» (расширенный vs PR #37 §5.4)

| Pattern | A (noise reduction) | B (find context) | ADR-1 fit | ADR-2 fit | ADR-3 fit | ADR-4 fit | ADR-6 fit | v0.1 in-scope? |
|---------|---------------------|-------------------|-----------|-----------|-----------|-----------|-----------|----------------|
| A — Groups | YES | YES | YES (UC1/UC3) | YES (orth.) | n/a | n/a | **EXTEND** (R-4) | YES (R-4) |
| B — Custom list | YES | NO | YES | YES | n/a | n/a | YES (--once flag) | YES |
| C — Tool-search BM25 | YES | YES | NO (catalog small) | YES | n/a | **REUSE** (R-3) | YES | NO; v0.2 |
| D — 3-level disclosure | YES | YES | YES | YES (canon shape) | n/a | n/a | YES | YES (R-1) |
| E1 — Tools-as-files | YES | YES | NO (sandbox req.) | YES | n/a | n/a | NO (Option C) | NO; v0.2 (R-6) |
| E2 — Programmatic call | YES | YES | NO | YES | n/a | n/a | NO | NO; v0.2 (R-6) |

## 6. Cross-reference: ADR-1..6 — глубокий проход

PR #37 §6 присутствует и cite-ит ADR-1..6, но в pointer-shape («совместимо», «не нарушает»). Этот раздел нацелен на каждый ADR с явным указанием: какой пункт ADR'а **прямо** требует/разрешает/запрещает что-то относительно findings из §4-§5.

### 6.1 ADR-1 (v0.1 use-case scope)

Релевантные пункты:

- **§Decision: «UC1 + UC3 in-scope; UC2 best-effort; UC4/UC5 deferred.»** — ограничивает scope harness'а до coding+PR + docs-to-wiki. Это объясняет, почему R-6 (code-execution) DEFERRED: code-execution feature не нужна для UC1 (где edit_file + git + gh достаточно) и не нужна для UC3 (где chunker + retrieval + LLM Q&A достаточно).
- **§Amendment 2026-05-01 — UC5 deferred.** UC5 = multi-LLM eval-harness — близко к Meta-Harness paper (выбор harness across N models). Это объясняет, почему R-9 (transferability claim citation) — TAKE Option (i) с caveat: pro-цитата помогает обосновать ADR-2 single-static-routing для v0.1, а контр-evidence будет пересобрана при v0.2 ADR-N для UC5.
- **§Concrete v0.1 deferred list.** Code-execution / programmatic-tool-calling **не** в этом списке явно. ADR-7 должен это **добавить** косвенно через DEFER (R-6).

**Lesson from prior drafts.** Ранние редакции цитировали ADR-1 generally, но не указывали, что *расширение* deferred-list (UC4/UC5) — потенциальная responsibility ADR-7 (через ADR-6 §Re-evaluation triggers порядок). R-6 в этой ноте делает это explicit.

### 6.2 ADR-2 (LLM tiering & access)

Релевантные пункты:

- **§Decision: 4-role static routing (Planner/Coder/Debug/Eval).** Подтверждается transferability-claim Meta-Harness abstract (один harness, разные модели per role). См. R-9.
- **§Amendment 2026-04-29 §point 5: «v0.1 inner-loop has no Critic / Reflector role.»** Прямо подтверждается Tsinghua module-ablation evidence (verifiers harm, multi-candidate search harm). См. R-5.
- **§Amendment 2026-04-29 §point 1-3: `tool_protocol: native | prompt-only` per-role.** Ортогонально нашим findings; tool-disclosure (R-1) живёт в registry shape, не в `models.yaml`.
- **§Amendment 2026-05-01 §point 1: «MCP-shaped tool signatures».** Это **главная** anchor для R-1. Все патrены (A-D) §5 — JSON-RPC-совместимые.
- **§Amendment 2026-05-01 §point 4: «Inner-loop ADR (future ADR-7) inherits the convention. The ADR-7 author MAY add fields … but MUST NOT change the existing two fields (`name`, `params` for request; `result`, `error` for response) without a separate amendment to this ADR-2.»** Это **критическая** строка. ADR-7 может добавить metadata-поля (`tags`, `tool_reference` response-form), но не может переопределить request/response. R-1, R-3, R-4 — все совместимы с этой clause.

**Lesson from prior drafts.** Ранние редакции цитировали «совместимо с ADR-2 MCP-shape», но **не вытаскивали** ADR-2 §Amendment 2026-05-01 §point 4 как hard constraint surface. Это критическая разница: ADR-7 author, читая only «MCP-shaped», может неосознанно нарушить point 4 (изменить `name`/`params`/`result`/`error` без отдельного ADR-2 amendment). Эта нота §0 R-1 явно цитирует point 4 как «existing fields MUST NOT change».

**MCP-compat ≠ context-efficiency (boundary nuance).** Это **отдельный** урок из ранней редакции (PR #37 §5.5), сохранённый явно: ADR-2 §Amendment 2026-05-01 правильно выбрал *MCP shape, not MCP transport* — но naive MCP-server клиент может всё ещё загрузить все schemas в system-prompt одним blob-ом (как в Anthropic engineering blog §4.3 multi-server example: 55k tokens на tool-definitions до tool-search). MCP-shape compatibility **не гарантирует** token-efficient context. ADR-7 должен сохранить это разделение: соответствие MCP shape (per ADR-2 §Amendment §point 4) — обязательно; progressive disclosure tool-loading (R-1) — поверх и ортогонально, не «free» из MCP.

### 6.3 ADR-3 (memory architecture variant — Mechanical Wiki)

Релевантные пункты:

- **§Decision: «Filesystem-canonical Markdown + frontmatter; deterministic write-time chunker; read = grep → SQLite FTS5 BM25; no embeddings/graph/Mem0 в v0.1.»** Подтверждает R-2: `events.jsonl` живёт на filesystem, как canon. `hot.md` — Markdown summary. Read-side для traces — `grep` (через path) + опционально BM25 (через FTS5, см. R-3 reuse).
- **§Decision: «`hot.md` session summary, auto-archived to `notes/sessions/<date>.md` at session end.»** — это **именно** место, где R-2 invariant нужен: `hot.md` cite-ит paths в `events.jsonl`, а не **заменяет** их. PR #37 §5.3 и §8 R-4 говорят про raw traces, но не явно соединяют это с ADR-3 hot.md mechanism.
- **§Decision: «Volatile-store hooks: `src/fa/memory/volatile/` exists as empty namespace.»** — Meta-Harness style self-evolution в v0.2 будет писать **в этот namespace**, читая raw `events.jsonl`. R-2 защищает forward-compat.

**Lesson from prior drafts.** Ранние редакции говорили про raw traces vs summaries, но не строили явный мост к ADR-3 hot.md mechanism (тот же файл, который user обозревает после сессии). R-2 в этой ноте делает это explicit: `hot.md` cite-ит paths в `events.jsonl`, не заменяет их.

### 6.4 ADR-4 (storage backend — SQLite FTS5)

Релевантные пункты:

- **§Decision: «SQLite FTS5 with `MATCH` queries; BM25-ranked out of the box; index в `~/.fa/index.sqlite`.»** Это прямой match для Pattern C tool-search BM25 variant из §5.3. См. R-3.
- **§Decision: «Pure stdlib `sqlite3` module; no extra dependency at storage layer.»** — это **именно** zero-new-deps обещание, которое R-3 защищает.
- **§Option B (chosen): «One row per chunk; MATCH queries.»** — ровно та же модель работает для tools: одна row per ToolSpec descriptor; MATCH query на `description` поле.

**Lesson from prior drafts.** Ранние редакции упоминали ADR-4, но не явно выписывали «BM25 tool-search reuses ADR-4 SQLite FTS5 без новых deps». Это zero-cost extension, не маркированный ранее; R-3 явно фиксирует.

### 6.5 ADR-5 (chunker tool — universal-ctags + markdown-it-py)

Релевантные пункты:

- **§Decision: универсальный chunker covering MD/text/Python/Go/PowerShell/TS/JS/YAML/TOML/JSON.** Это chunker для **корпуса** (UC3 ingestion + UC1 code retrieval), **не** для tool descriptors.
- ADR-5 ortогонален harness design в части tool-disclosure: tool descriptors — короткие structured documents, не chunked-источники. Однако ADR-5 **может** быть переиспользован для chunking trace files (`events.jsonl` events можно интерпретировать как text chunks для retrieval, если v0.2 self-evolution пожелает).

**Lesson from prior drafts.** Ранние редакции не цитировали ADR-5 в cross-reference вообще. Это формально gap, но minor — ADR-5 ортогонален harness design в v0.1.

### 6.6 ADR-6 (tool sandbox & path allow-list)

Релевантные пункты:

- **§Decision: deny-by-default path allow-list; `~/.fa/sandbox.toml`; `[read]` + `[write]` секции.** — текущий shape. R-4 предлагает добавить `[tool_groups]` секцию.
- **§Re-evaluation triggers: «`run_command` lands → re-evaluate Option C OS-level sandbox.»** — ADR-6 уже зарезервировал триггер для OS-level escalation. Code-execution-over-MCP (R-6) активирует **именно** этот trigger. R-6 этой ноты **не** активирует trigger в v0.1, защищает forward-compat.
- **§Tool wiring table (`read_file`, `list_files`, `edit_file`, `write_file`, `grep`).** — текущий v0.1 tool-set, который sandbox прикрывает. R-7 §Acceptance check «which tools agent rarely uses?» применяется к этой таблице.
- **§Audit log: `~/.fa/state/sandbox.jsonl`.** — это **уже** raw-events JSONL trace, прецедент для R-2 `events.jsonl`. R-2 предлагает обобщить shape с `~/.fa/state/sandbox.jsonl` на `~/.fa/state/runs/<run_id>/events.jsonl` (sandbox decisions — один из event-types в общем trace).

**Lesson from prior drafts.** Ранние редакции цитировали ADR-6 generally, но не вытягивали `~/.fa/state/sandbox.jsonl` как прецедент для R-2 shape (FA **уже** делает append-only JSONL trace на одном under-system; общий trace — обобщение). R-2 в этой ноте делает это explicit.

### 6.7 Свод cross-reference (расширенный vs PR #37 §6)

| ADR | PR #37 cite | Этот файл cite | Углубление | Hard-constraint cite |
|-----|-------------|----------------|------------|----------------------|
| ADR-1 §Decision | yes | yes | + UC5 connection (R-9) | — |
| ADR-1 §Amendment 2026-05-01 (UC5) | partial | yes | new (R-9) | — |
| ADR-2 §Decision (4 roles) | yes | yes | + transferability (R-9) | — |
| ADR-2 §Amendment 2026-04-29 §point 5 (no Critic) | yes | yes | + primary-source numbers (R-5) | YES |
| ADR-2 §Amendment 2026-05-01 §point 1 (MCP-shape) | partial | yes | + 3-tier disclosure mapping (R-1) | — |
| ADR-2 §Amendment 2026-05-01 §point 4 (inheritance) | **NO** | yes | new — это hard constraint для ADR-7 | **YES** |
| ADR-3 §Decision (Mechanical Wiki) | yes | yes | + hot.md ↔ events.jsonl invariant (R-2) | — |
| ADR-3 §Decision (volatile-store hooks empty) | NO | yes | new — protected forward-compat (R-2) | — |
| ADR-4 §Decision (SQLite FTS5) | partial | yes | + BM25 tool-search reuse (R-3) | — |
| ADR-5 §Decision | NO | partial | minor (orthogonal, future trace chunking) | — |
| ADR-6 §Decision (path allow-list) | yes | yes | + tool_groups extension (R-4) | — |
| ADR-6 §Re-evaluation triggers | partial | yes | + code-execution trigger (R-6) | — |
| ADR-6 §Audit log JSONL | NO | yes | new — precedent для R-2 events.jsonl shape | — |

## 7. Методология / lessons from prior drafts

Эта нота — результат объединения двух ранних редакций (PR #37 + PR #38 в
upstream `GITcrassuskey-shop/First-Agent`), которые **не сливаются в main**;
обе закрываются project-leadом без merge при cross-fork sync. Эта секция
сохраняет audit-trail substantive- и methodological-уроков из тех редакций
**без ghost line-references** на never-merged file paths. Все ссылки ниже —
концептуальные («что прежняя редакция упустила»), а не line-numbered cite в
отсутствующий blob.

### 7.1 Substantive lessons (какие cross-reference-связи раньше были упущены и теперь интегрированы)

1. **ADR-2 §Amendment 2026-05-01 §point 4 («inherits convention; MAY add fields but MUST NOT change»)** — ранние редакции цитировали ADR-2 как «MCP-shaped» generally, но не выписывали point 4 как hard-constraint surface для ADR-7. Now anchored: см. R-1 + §6.2. Risk-if-skipped: ADR-7-author может неосознанно нарушить point 4 (изменить `name`/`params`/`result`/`error` без отдельного ADR-2 amendment).
2. **Bright Data конкретный shape** (`GROUPS=` env-var; `tools=` env-var; 11 групп) — ранние редакции упоминали Bright Data как «design analogy», но не предлагали конкретный TOML-shape расширения ADR-6. Now concrete: см. R-4 + §6.6. Risk-if-skipped: «design analogy» без shape — пустой указатель.
3. **ADR-4 = BM25 tool-search reuse**. Ранние редакции указывали «совместимо с ADR-4», но не выписывали «zero-new-deps tool-search reuse» как отдельную R. Now explicit: см. R-3 + §6.4. Risk-if-skipped: при v0.2 росте catalog implementer может ввести `rank-bm25` или внешний embedding-сервис как новую dependency.
4. **ADR-6 `~/.fa/state/sandbox.jsonl` как precedent для R-2 trace shape** — ранние редакции обсуждали trace shape абстрактно, не упоминая, что append-only JSONL precedent уже **есть** в ADR-6. Now linked: см. R-2 + §6.6. Risk-if-skipped: дублирующая раскладка trace files под разные subsystems.
5. **`hot.md` ↔ `events.jsonl` invariant** — ранние редакции говорили «raw trace files eval substrate, summaries — index», но не выписывали invariant **как форма ADR-7-rule**. Now form-rule: см. R-2 + §6.3. Risk-if-skipped: при v0.2 self-evolution implementer может «срезать» — пробовать запустить eval над `hot.md` (compressed).
6. **System-prompt assembly + prefix-cache invariant** (Video 2 component #7) — **полностью отсутствовал** в самой ранней редакции; PR #38 поднял как UNCERTAIN-ASK. Now resolved: см. R-8 (TAKE Option (i): static layered prompt + UC5 migration trigger). Risk-if-skipped: каждый PR будет переизобретать assembly, cache-hits случайны.
7. **ADR-1 §Amendment 2026-05-01 (UC5) ↔ Meta-Harness transferability** — ранние редакции не явно соединяли UC5 (multi-LLM eval-harness deferred) с Meta-Harness paper. Now connected: см. R-9 (TAKE Option (i): ADR-2 evidence-base extension с caveat «verified for one benchmark family»).
8. **NLAH text-half ↔ code-half synthesis** — ранние редакции описывали NLAH paper, но не явно фиксировали, что у First-Agent **уже есть** text-half (AGENTS.md + HANDOFF.md + ADRs + research notes), и ADR-7 — bridge to code-half. Now anchored: см. §3 «NLAH ↔ code-half synthesis».
9. **MCP-compat ≠ context-efficiency** — ранние редакции упоминали MCP boundary, но не явно выделяли, что соответствие MCP shape **не гарантирует** token-efficient context. Now explicit: см. §6.2 «MCP-compat ≠ context-efficiency (boundary nuance)».
10. **ADR-7 contract sketch как single read-friendly artifact** — ранние редакции имели разные R-recommendations, но не сводили их в один read-friendly contract sketch. Now in §10: ToolSpec / ToolResult / Trace pseudo-schema + prompt-assembly invariant + acceptance-block.

### 7.2 Methodological lessons (как написана нота)

1. **`UNCERTAIN-ASK` верdict как expected escalation механизм.** Per `knowledge/prompts/research-briefing.md` Stage 5, UNCERTAIN-ASK — это **ожидаемый** механизм для эскалации design-вопросов пользователю. Самая ранняя редакция содержала zero UNCERTAIN-ASK (5 рекомендаций: 4× TAKE + 1× DEFER) — premature TAKE без user-input. Followup-редакция escalated 2× UNCERTAIN-ASK (R-8, R-9), что project-lead резолвил 2026-05-07 (см. §0 R-8/R-9). Эта нота фиксирует: ноль UNCERTAIN-ASK — методологическое предупреждение, а не достижение.
2. **Secondary→primary upgrade.** Самая ранняя редакция помечала Tsinghua/Meta-Harness numbers как «from transcript, secondary». Эта нота §4.1-§4.2 показывает, что abstract обоих papers резолвится, и ряд numbers (Meta-Harness 7.7 / 4× / 4.7 + 5 held-out) — primary. Урок: всегда пытаться upgrade secondary → primary до закрытия note.
3. **Attribution caveat должен быть помечен.** Video 3 называет Meta-Harness «Stanford» paper. arxiv submitter Yoonho Lee, narrative cite-ит Khattab/DSPy. abstract HTML явно не выписывает affiliation. Самая ранняя редакция приняла Video-атрибуцию без caveat. Эта нота §8.3 явно помечает unresolved attribution.
4. **`claims_requiring_verification` ≥ 5 для primary-paper coverage.** Самая ранняя редакция содержала 3 пункта; для derivation из непрочитанных primary papers ожидается ≥5. Эта нота — 7 пунктов.
5. **Honest gap с pre-fetch follow-up.** PR #38 §8.5 признал «not-fetched» honest gap для трёх MCP URL-ов (donation news, launch announcement, spec). Эта нота закрыла gap pre-fetch-ем (см. §4.6, §4.7, §4.8 + §8.5 closure note). Урок: честно фиксировать gap → пытаться закрыть его в следующем passe → если закрыт, обновить §8.5 формулировку.
6. **Single source-of-truth supersession без silent overwrite.** Эта нота объединяет PR #37 + PR #38 в один файл и не использует ghost line-references на never-merged blobs. Audit-trail сохранён через эту секцию (lessons learned), а не через line-numbered cite в отсутствующий source. Per AGENTS.md PR Checklist rule #5 «supersession, not overwrite»: ранние редакции остаются в git-истории upstream PR #37/PR #38 (closed-without-merge), эта нота — single canonical artifact.

## 8. Risks and caveats

### 8.1 Video-claims vs primary papers

Часть наиболее ярких numbers (Tsinghua module-ablation deltas; OSymphony migration delta) видны только в видео-нарративе Video 3, не в abstract. До того как ADR-7 будет цитировать их в §Decision, нужна сверка по full paper PDF. См. `claims_requiring_verification` пункты 1-2.

### 8.2 «Subtraction can go too far»

PR #37 §7 пункт 5 (file lines ~462+) уже это поднял: убирать verifiers безопасно только если deterministic checks покрывают risk. Эта нота **усиливает** caveat: ADR-6 sandbox + Makefile/CI/pre-commit — это deterministic gates, которые **должны** оставаться независимо от R-5 «no Critic». R-7 4-вопросный self-audit включает «verification/search loops hurting?» именно для того, чтобы review-PR культура **не** срезала deterministic checks.

### 8.3 Meta-Harness Stanford attribution — unresolved

Atomic facts:

- arxiv `2603.28052v1` submitter (publish-time): Yoonho Lee.
- Video 3 narrative: «paper was released by Omar Khattab who built DSPy» (verbatim transcript).
- Video 3 narrative title: «Orchestration Over Architecture: What Stanford Found».
- Khattab — formerly Stanford ML PhD (DSPy origin), сейчас (2026 vintage) часто связан с MIT.
- Yoonho Lee — Stanford ML PhD (lineage).
- Affiliation в abstract HTML, который мы получили, **не выписан**.

**Эпистемический статус.** Stanford lineage **присутствует** (через Yoonho Lee), но называть paper «Stanford paper» без affiliation-цитаты — **video-claim**. Не цитировать в ADR без проверки title page в paper PDF.

### 8.4 Meta-Harness number-разнобой

abstract: «improves over a state-of-the-art context management system **by 7.7 points** while using **4x fewer context tokens**» — без указания «215 text classification tasks».

Video 3 narrative: «It was scoring 7.7 points above state-of-the-art using four times fewer tokens» **+** «215 text classification» (verbatim).

Расхождение нечастое — abstract less specific. Скорее всего, 215 — экспериментальный setup detail из paper Section, не abstract. Helpful, но **не cite-ить как primary** до проверки.

### 8.5 Honest gap closure (бывший «не-fetch-нутые источники»)

Прошлая редакция содержала честное признание, что три URL-а из source list
fetched only as URL-record. Эта консолидированная нота **закрыла этот gap**
pre-fetch-ем body-целиком на 2026-05-07:

- **`https://www.anthropic.com/news/donating-the-model-context-protocol-and-establishing-of-the-agentic-ai-foundation`** — fetched 2026-05-07; см. §4.6 за governance-факты (Agentic AI Foundation co-founded Anthropic / Block / OpenAI; >10k MCP servers; 75+ Claude connectors; 97M+ monthly SDK downloads).
- **`https://modelcontextprotocol.io/specification/2025-11-25`** — fetched 2026-05-07; см. §4.8 за overview-факты (Resources / Prompts / Tools server-features triplet; Sampling / Roots / Elicitation client-features; Security & Trust principles).
- **`https://www.anthropic.com/news/model-context-protocol`** — fetched 2026-05-07; см. §4.7 за launch-announcement (Nov 25, 2024; JSON-RPC; hosts/clients/servers; early adopters Block / Apollo + Zed / Replit / Codeium / Sourcegraph).

**Residual gap (не закрыт этой нотой).** Spec changes между 2024 launch и
spec `2025-11-25` не разобраны построчно (point-by-point diff). ADR-2
§Amendment 2026-05-01 §point 5 уже признаёт «transport layer changes between
2024 and 2025-2026»; для ADR-7 §Decision этого достаточно — точные diff
deferred to ADR author при необходимости.

### 8.6 «Harness-engineering» как label

Tsinghua paper formalizes «harness engineering» как discipline. У FA нет соответствующей роли — это работа project-owner (`0oi9z7m1z8`) + agent (Devin / другие). Если в v0.2 появится «harness eval» tier, понадобится отдельный ADR. Это явно out-of-scope для текущей ноты, но фиксирую как concept-anchor для будущих research notes.

## 9. Numbered recommendations (long-form)

### R-1 — MCP forward-compat для tool-disclosure (3 уровня) на shape-уровне

Предлагается, чтобы ADR-7 §Decision содержал блок `### Tool disclosure tiers` с тремя shape-таблицами:

```text
Tier 1 — Server list (zero-tool registration cost)
  GET /servers           → [{name, description}, ...]

Tier 2 — Tool descriptors (per-server)
  GET /servers/<server>/tools
                         → [{name, summary, tags, permissions}, ...]

Tier 3 — Full schema (per-tool, on demand)
  GET /servers/<server>/tools/<tool>
                         → {name, description, input_schema, output_schema, ...}
```

Implementation в v0.1 — pure-Python in-process dispatcher (per ADR-2 §Amendment 2026-05-01 §point 3 «no `mcp` package dependency in v0.1»). Все три endpoint — JSON-RPC-shaped. ToolSpec обязан декларировать `tags: list[str]` (см. R-4) и `permissions: dict[Literal['read','write'], list[str]]` (см. ADR-6).

ADR-7 author **должен** explicit cite ADR-2 §Amendment 2026-05-01 §point 4 как hard constraint surface. Это закрывает gap §7.1 пункт 1.

### R-2 — Trace separation invariant: events.jsonl ≠ hot.md

Предлагаемый формат `events.jsonl` event (pseudo-schema; `|` обозначает discriminated-union `kind`/`actor`, `<…>` — template placeholders, не валидный JSON):

```text
{
  "ts": "2026-05-06T13:42:17.123Z",
  "run_id": "<uuid>",
  "turn": 17,
  "kind": "tool_request" | "tool_response" | "permission_decision" | "model_call" | "compaction" | "stop",
  "actor": "planner" | "coder" | "debug" | "judge" | "sandbox" | "harness",
  "payload": { ... kind-specific ... },
  "artifact_path": "<optional: filesystem path with raw blob>",
  "model": "<optional: model slug>",
  "tokens_in": <int|null>,
  "tokens_out": <int|null>,
  "cost_usd": <float|null>
}
```

Инвариант (ADR-7 §Decision):

> «`hot.md` cite-ит paths и/или byte-offsets в `~/.fa/state/runs/<run_id>/events.jsonl`. `hot.md` **не** канонический источник для replay/eval/Meta-Harness-style self-evolution. При конфликте между `hot.md` и соответствующими событиями в `events.jsonl`, `events.jsonl` побеждает.»

Это **прямая** primary-source поддержка из abstract Meta-Harness paper («existing text optimizers compress feedback too aggressively»). См. §4.2.

ADR-3 §Decision уже мандат-ит filesystem-canon. R-2 расширяет canon на trace-уровень.

### R-3 — SQLite FTS5 reuse для tool-search BM25

ADR-4 §Option B (chosen): «SQLite FTS5; one row per chunk; `MATCH` queries return BM25-ranked results out of the box.»

Для tools — точно та же модель:

```sql
-- Новая virtual-table в ~/.fa/index.sqlite
CREATE VIRTUAL TABLE tools USING fts5(
    name UNINDEXED,
    server UNINDEXED,
    description,
    summary,
    tags
);
-- BM25 tool-search query:
SELECT name, server FROM tools WHERE tools MATCH ? ORDER BY rank LIMIT 5;
```

Zero new dependency. Trigger (когда реализовать): catalog > ~10 tools или selection-accuracy degradation, наблюдаемая на eval-harness (когда тот появится).

### R-4 — `[tool_groups]` в `~/.fa/sandbox.toml`

Предлагаемое расширение ADR-6 §Policy file:

```toml
# Existing sections:
[read]
allow = [...]
deny  = [...]

[write]
allow = [...]
deny  = [...]

# New section (R-4):
[tool_groups]
# Allow-list тегов; tool-call блокируется sandbox-ом, если ни один tag тула
# не входит в этот список. Empty list = no tools allowed (deny-all).
# Default policy ships `["coding", "git", "memory"]` для UC1 + UC3.
allow = ["coding", "git", "memory", "search"]
# Block-list overrides allow.
deny  = []
```

Каждый ToolSpec обязан декларировать `tags: list[str]` (R-1). Sandbox-check запускается на dispatch:

```python
class Sandbox:
    def check_tool_call(self, tool_name: str, tags: list[str]) -> None:
        """Raise SandboxError if tool's tags don't intersect [tool_groups].allow."""
```

Cost: medium (TOML schema + dispatcher hook + ToolSpec migration).

### R-5 — «No Critic / verifier loops в v0.1» — primary-source-cited

ADR-7 §Notes должен содержать subsection:

```text
### Why no Critic / verifier role in v0.1

ADR-2 §Amendment 2026-04-29 §point 5 already states: "v0.1 inner-loop has
no Critic / Reflector role." This subsection adds primary-source evidence
from research note knowledge/research/efficient-llm-agent-harness-2026-05.md §4.1:

- Tsinghua arXiv:2603.25723 (NLAH paper, March 2026) module-ablation
  reportedly shows verifiers harm performance (-0.8 SWE-bench Verified,
  -8.4 OSWorld); multi-candidate search harms by ~5.6.
  CAVEAT: numbers from Video 3 narrative; pending verification against
  full paper PDF (see deep-dive §claims_requiring_verification #1).

Deterministic checks (sandbox denial per ADR-6, schema validation,
linters/tests, `git status`, CI) remain in scope and are not Critic loops.
```

### R-6 — Code-execution-over-MCP DEFER + forward-compat shape

ADR-7 §Notes:

```text
### Future code-execution: forward-compat shape

Anthropic engineering blog (2025-11-04) reports 98.7% token reduction
(150k → 2k) for Google-Drive-→-Salesforce example using
code-execution-over-MCP. This is the strongest efficiency lever in the
current source set, and simultaneously the most expensive to ship safely.

v0.1 does not implement code-execution. v0.1 registry MAY produce a
filesystem layout `~/.fa/state/tools/<server>/<tool>.<ext>` on demand
(from ToolSpec descriptors) but does not execute. Triggers for v0.2:
- ADR-6 §Re-evaluation triggers: `run_command` lands → re-evaluate
  Option C OS-level sandbox.
- New ADR required for: redaction policy (intermediate data MUST NOT
  enter model context); CPU/time/network limits; allow-list for shells.
```

### R-7 — 4-вопросный subtraction-first self-audit

ADR-7 §Acceptance:

```text
### Acceptance: subtraction-first self-audit

Before merging ADR-7, the implementer answers (in PR description):

1. What is in the agent's context window that does not need to be there?
2. Which tools does the agent rarely use (over the latest N traces)?
3. Are there verification or search loops that might be hurting performance?
4. Is the control logic written in code, or in language (AGENTS.md /
   research notes / prompts), and which would be cheaper to change?

Each "yes / unclear" answer requires either (a) removal of the named
component, or (b) a one-paragraph justification cited in the ADR-7
§Notes.
```

Это превращает Anthropic «subtraction principle» из noted-philosophy в **testable рубрик**.

### R-8 — TAKE Option (i): static layered prompt + UC5 migration trigger

См. §0 R-8 за full 8-field decision-briefing. Project-lead резолвил
2026-05-07 как **Option (i) static layered prompt** для v0.1.

**Формальный invariant** для ADR-7 §Decision `### Prompt assembly`:

```text
Invariant: system-prompt is assembled exactly once at session start
and frozen for the duration of the session. The assembly composes
static layers in deterministic order:

  1. AGENTS.md (project conventions)
  2. ADR table-of-contents (decision pointers)
  3. Tool-registry descriptors (tier-2: name + 1-line description
     + tags; per R-1 progressive disclosure)

The provider caches the full system-prompt prefix; cache key changes
only when AGENTS.md, ADRs, or tool-registry change between sessions.

Dynamic state — `hot.md` body, current task, files-of-interest,
HANDOFF.md cite — is delivered in the first user message, not in
the system-prompt. On Anthropic API the first user message is
prefix-cacheable up to `user_turn[1]` (vendor-specific, orthogonal
to the (i) shape).

Non-invariants: this rule does not preclude per-turn tool-result
messages, retrieval results, or compaction events; those are normal
mid-session traffic and do not invalidate the system-prompt cache.
```

**Migration path к Option (ii) two-segment assembly (deferred).**
v0.1 ставит static layered prompt. Migration trigger зафиксирован
как UC5 measurement: если UC5 (multi-LLM eval-harness, deferred per
ADR-1 §Amendment 2026-05-01) покажет ≥ N% degradation на staleness-
sensitive tasks (например, agent неправильно обрабатывает task под
изменённый AGENTS-rule, который ещё не подгружен в session, потому
что session началась до изменения), v0.2 amendment может ввести
two-segment assembly: layer 0 (static) → префикс с `cache_control:
ephemeral`; layer 1+2 (dynamic) → suffix без cache; `hot.md` →
отдельный dynamic-suffix. Это **отдельный** ADR (ADR-N+1 или
ADR-7-amendment), не v0.1 scope. Migration — non-breaking config
change, не code rewrite, при условии что layered shape сохранён.

**Why Option (i), not Option (ii) или (iii) на v0.1.**

- Option (i) даёт самый предсказуемый token-cost (provider caches
  full prefix; one-time assembly cost), стабильное качество, и
  full eval-reproducibility (same prefix → same model behavior за
  равной randomness).
- Option (ii) требует, чтобы провайдер поддерживал partial prefix-
  cache (Anthropic API через `cache_control` блоки умеет;
  OpenRouter — variable per upstream provider; vLLM yes через
  `--enable-prefix-caching`). Для v0.1 (single Anthropic-tier
  provider per ADR-2 §Decision) это работало бы, но добавляет
  config-complexity без measurable benefit до того как есть UC5
  measurements.
- Option (iii) (no assembly в v0.1, retrieval-only) — radically
  simpler, но agent теряет default conventions без вызова
  `read_file(AGENTS.md)` каждую сессию; это противоречит R-1
  progressive disclosure principle (default conventions — это
  tier-1 always-loaded context, не tier-3 on-demand).

### R-9 — TAKE Option (i): ADR-2 evidence-base extension с caveat

См. §0 R-9 за full 8-field decision-briefing. Project-lead резолвил
2026-05-07 как **Option (i) ADR-2 evidence-base extension с caveat
«verified for one benchmark family per Meta-Harness abstract»**.

**Формальный one-paragraph amendment-stub** для ADR-2 (при
ближайшем amendment, не часть этой ноты scope):

```text
### Amendment 2026-05-07 — Evidence base extended

Meta-Harness paper (arXiv:2603.28052v1, March 2026) reports that a
single discovered harness improved accuracy on 200 IMO-level math
problems by 4.7 points on average across five held-out models.
This is one piece of evidence for the §Decision-form choice of a
single harness with role-based model routing rather than per-model
harness. Caveat: transferability is verified for one benchmark
family per the abstract; broader transferability across coding,
computer-use, or agentic-ops benchmarks remains future work.

See: knowledge/research/efficient-llm-agent-harness-2026-05.md §4.2.
```

**Caveat rationale.** Meta-Harness abstract говорит про math
reasoning benchmark family (IMO-level + retrieval-augmented); Video 3
narrative расширяет до «coding agents», но primary-source это **не
подтверждает**. ADR-2 amendment должен зафиксировать caveat явно,
чтобы будущий ADR-N (UC5 multi-LLM eval-harness) знал, что
transferability claim — strong-for-math, not-yet-verified-for-other.

**Why Option (i), not Option (ii) или (iii).**

- Option (i) (ADR-2 evidence-base extension) — cheap, one-paragraph,
  делает evidence-base ADR-2 сильнее для будущих audits. Caveat
  защищает от ошибочной экстраполяции.
- Option (ii) (только пометить в `claims_requiring_verification`) —
  cost: zero, но evidence-base ADR-2 §Decision-form останется
  слабее, чем возможно. ADR-2 не получит evidence-extension даже
  при том, что primary-source зафиксирован.
- Option (iii) (отдельный research note про cross-model harness
  стабильность) — cost: medium, deferred; полезно при v0.2 ADR-N
  для UC5, но избыточно для текущего ADR-7 prep cycle.

## 10. ADR-7 contract sketch — выжатие R-1..R-9 в single read-friendly artifact

Этот pseudo-schema собирает invariants из R-1..R-9 в одну place-to-look-at form.
Это **draft shape**, не сам ADR-7; цель — дать ADR-7 author готовый starting
point с уже зафиксированными hard constraints.

```text
ADR-7 Inner-loop / tool contract (candidate shape v0.1)

Scope:
  - v0.1 UC1/UC3 single-agent loop only
  - no Critic, no multi-agent orchestration, no external MCP server dependency
    (R-5 + ADR-2 §Amendment 2026-04-29 §point 5)

Runtime loop:
  1. assemble static prompt prefix once at session start (R-8 invariant);
     freeze for the duration of the session
  2. load dynamic repo/session context by pointers in the first user message
     (R-1 progressive disclosure tier-1; not in system-prompt per R-8)
  3. expose compact tool descriptors (R-1 progressive disclosure tier-2:
     name + 1-line description + tags); full JSON Schema loaded on demand
     (tier-3, per R-1 + R-3 BM25 lookup if catalog > ~10 tools)
  4. receive model response
  5. if tool call: validate schema → sandbox pre_tool (ADR-6 deny-by-default,
     extended with [tool_groups] per R-4) → execute → post_tool audit
  6. append JSONL event to ~/.fa/state/runs/<run_id>/events.jsonl;
     artifacts to ~/.fa/state/runs/<run_id>/artifacts/* (R-2 raw trace)
  7. return compact result summary + artifact paths to model;
     full payloads stay on filesystem (R-2 hot.md cites, не заменяет)
  8. stop on explicit final answer, max iterations, hard error,
     or user approval gate

ToolSpec (registry entry, MCP-shape per ADR-2 §Amendment 2026-05-01 §point 4):
  name: stable dotted string                         # MUST NOT change without ADR-2 amendment
  description: one-line model-facing summary
  input_schema: JSON Schema, loaded on demand        # tier-3 per R-1
  output_schema: JSON Schema (optional)
  permission: read | workspace | full                # ADR-6 path-allow-list scope
  tags: list[str]                                    # for [tool_groups] allow-list per R-4
  handler: deterministic callable hidden behind dispatcher
  defer_loading: bool                                # Anthropic API tool-search hint (R-3)

ToolResult (response, MCP-shape per ADR-2 §Amendment 2026-05-01 §point 4):
  result: compact structured payload | null          # MUST NOT change shape without ADR-2 amendment
  error: { code: int, message: str, retryable: bool } | null
  summary: short model-facing text                   # tier-1 visible to model
  artifacts: list[path]                              # raw output / diff / logs on filesystem

Trace (R-2 invariant):
  ~/.fa/state/runs/<run_id>/events.jsonl             # append-only, JSONL-shaped
  ~/.fa/state/runs/<run_id>/artifacts/*              # raw payloads, hot.md cites paths
  Each event has: ts, actor, kind, content, tool_name?, tool_call_id?,
                  parent_event_id?, harness_id (cf. Q-6 v0.2 amendment)

Acceptance (R-7 subtraction-first self-audit, in PR description):
  Q1. What is in the agent's context window that does not need to be there?
  Q2. Which tools does the agent rarely use (over the latest N traces)?
  Q3. Are there verification or search loops that might be hurting performance?
  Q4. Is the control logic written in code, or in language (AGENTS.md /
      research notes / prompts), and which would be cheaper to change?
  Each "yes / unclear" → either remove the named component, or write a
  one-paragraph justification cited in ADR-7 §Notes.

Forward-compat (deferred per R-6):
  - Code-execution-over-MCP: ADR-7 §Notes flags export-path shape
    (Resources / Prompts MCP-server-features per spec 2025-11-25 §4.8)
    but does NOT implement in v0.1.
  - Two-segment prompt assembly: R-8 migration trigger on UC5 metrics.
  - Cross-model harness transferability: R-9 ADR-2 amendment-stub at
    next ADR-2 amendment.
  - Harness version field in events.jsonl: Q-6 v0.2 amendment.
```

Этот sketch намеренно переиспользует ADR-2 MCP-shaped convention и ADR-6
sandbox precedent, а не создаёт параллельный tool protocol. Это shape-
synthesis перенесена из ранней редакции (PR #37 §6.1) и расширена R-2 trace
shape, R-4 tool-groups, R-7 acceptance-block, R-8 invariant и R-9 caveat.

## 11. Open questions

### Q-1 — Какой именно provider lineup tested ADR-2 amendment 2026-04-29 «native tool calling»?

Amendment cites «Verified model coverage (user, Apr 2026): Qwen 3.6, Kimi 2.6, GLM 5.1, Claude latest, Nemotron 3 Super». Нужна ли отдельная test-fixture, проходящая на каждой из них перед ADR-7 finalization? Релевантно: tool-disclosure tier 3 (full JSON schema) может быть native-API-specific (например, Anthropic tool-use vs OpenAI tool-calls vs vLLM tool-calls — JSON-shape совместимый, но field-names разные).

### Q-2 — Когда v0.1 catalog перерастёт ~10 tools?

R-3 (BM25 tool-search reuse) — это extension point. Но при каком фактическом размере catalog? Nuance: 10 tools — это рекомендация Anthropic docs «degrades after 30-50»; для v0.1 (UC1 + UC3) ожидается ≤10 tools. Trigger needs метрик: либо token-count в первом prompt > X% context-budget, либо measured selection-accuracy < Y% на trace-replay.

### Q-3 — Trace retention policy

`events.jsonl` (R-2) может содержать private code, tool outputs, model responses. ADR-7 должен выбрать default local retention (постоянно? N дней? зависит от диск-объёма?) и redaction policy. Это, возможно, отдельный ADR-7-amendment, не точно ADR-7-core.

### Q-4 — Где заканчивается hot.md и начинается events.jsonl?

R-2 invariant фиксирует «hot.md cite-ит, не заменяет», но не определяет, **что** идёт в `hot.md`. Концептуально: `hot.md` — pinned current state + last-N-decisions LLM-friendly summary. `events.jsonl` — full trace. Граница — не binary, скорее функциональная. ADR-7 должен дать примеры разделения.

### Q-5 — Как sandbox future code-execution mode?

Покрыто в PR #37 §9 Q-5; здесь дублирую как unresolved для R-6 trigger. Прямого ответа эта нота не даёт.

### Q-6 — Нужен ли явный «harness version» field в `events.jsonl`?

Meta-Harness paper хранит «source code, scores, and execution traces of all prior candidates through a filesystem». Если v0.2 будет делать meta-harness-light, нужно знать, какая версия harness произвела trace. Один-line addition: ToolSpec + Loop-spec → SHA → append в каждое event как `harness_id`. Cheap; решение позже.

## 12. Files used

- User-provided attachment `youtube_transcripts.md` (Devin attachment 01215dee-a768-4c7b-88a4-fd92b37f52db; downloaded in-session to `/home/ubuntu/attachments/f5e10d80-ff4a-4c9d-822f-7ddf4b683ff5/youtube_transcripts.md`; 321 lines; 3 videos)
- <https://arxiv.org/abs/2603.25723> — Tsinghua NLAH paper abstract HTML, fetched
- <https://arxiv.org/abs/2603.28052v1> — Meta-Harness paper abstract HTML, fetched
- <https://platform.claude.com/docs/en/agents-and-tools/tool-use/tool-search-tool> — Claude API tool-search docs, fetched body
- <https://docs.brightdata.com/ai/mcp-server/tools> — Bright Data MCP tools docs, fetched body
- <https://www.anthropic.com/engineering/code-execution-with-mcp> — Anthropic engineering blog, fetched body
- <https://modelcontextprotocol.io/specification/2025-11-25> — fetched body, see §4.8
- <https://www.anthropic.com/news/model-context-protocol> — fetched body, see §4.7
- <https://www.anthropic.com/news/donating-the-model-context-protocol-and-establishing-of-the-agentic-ai-foundation> — fetched body, see §4.6
- <https://github.com/GITcrassuskey-shop/First-Agent/pull/37> — PR review-комментарии + head-branch ноты (commit `d03f7a3`); closed-without-merge per cross-fork sync
- <https://github.com/GITcrassuskey-shop/First-Agent/pull/38> — PR review-комментарии + head-branch ноты (commit `1aec3e2`); closed-without-merge per cross-fork sync
- [`../adr/ADR-1-v01-use-case-scope.md`](../adr/ADR-1-v01-use-case-scope.md)
- [`../adr/ADR-2-llm-tiering.md`](../adr/ADR-2-llm-tiering.md) (особенно §Amendment 2026-04-29 + §Amendment 2026-05-01)
- [`../adr/ADR-3-memory-architecture-variant.md`](../adr/ADR-3-memory-architecture-variant.md)
- [`../adr/ADR-4-storage-backend.md`](../adr/ADR-4-storage-backend.md)
- [`../adr/ADR-5-chunker-tool.md`](../adr/ADR-5-chunker-tool.md) (head only — orthogonal)
- [`../adr/ADR-6-tool-sandbox-allow-list.md`](../adr/ADR-6-tool-sandbox-allow-list.md)
- [`./how-to-build-an-agent-ampcode-2026-04.md`](./how-to-build-an-agent-ampcode-2026-04.md) (frontmatter + relevant sections)
- [`./cutting-edge-agent-research-radar-2026-05.md`](./cutting-edge-agent-research-radar-2026-05.md) (head, MCP/tool-registry section reference)
- [`./semi-autonomous-agents-cross-reference-2026-05.md`](./semi-autonomous-agents-cross-reference-2026-05.md) (referenced for ADR-2 amendment 2026-05-01 lineage)
- [`./agent-roles.md`](./agent-roles.md) (referenced for Critic/no-Critic lineage)
- [`./latent-verifier-evolve-research-2026-05.md`](./latent-verifier-evolve-research-2026-05.md) (referenced for verifier-evolve lineage)

## 13. Out of scope

- Написание самого ADR-7 (это PR этого файла — research-only).
- Реализация inner-loop, registry, sandbox extension, trace writer.
- Построение реальных MCP servers / clients или установка `mcp` Python package.
- Реализация code-execution / programmatic-tool-calling.
- Decision о финальных model/provider slugs (ADR-2 §Decision уже purposely не фиксирует — see §Decision «Note on model slugs»).
- Retrofit старых research notes под §0 Decision Briefing format (per AGENTS.md PR Checklist rule #8 forward-only clause).
- Point-by-point парсинг MCP spec change-log между 2024 launch и spec `2025-11-25` (residual gap; см. §8.5).
- Модификация или закрытие PR #37 или PR #38 в upstream `GITcrassuskey-shop/First-Agent` — сделает project-lead при cross-fork sync.
- Verification by full paper PDF read (см. `claims_requiring_verification` 1-7 — это работа для следующей сессии).
