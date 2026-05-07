# Глоссарий

Короткие определения терминов, встречающихся в First-Agent и в этой вики.

| Термин | Значение |
|---|---|
| **ACI** | Agent–Computer Interface. Минимальная поверхность, через которую агент взаимодействует с computational env (tools, files, shell). См. [`research/cutting-edge-agent-research-radar-2026-05.md`](../knowledge/research/cutting-edge-agent-research-radar-2026-05.md). |
| **ADR** | Architecture Decision Record. Каноничное место — [`knowledge/adr/`](../knowledge/adr/); процесс — [`knowledge/adr/README.md`](../knowledge/adr/README.md). Cheat-sheet — [`knowledge/adr/DIGEST.md`](../knowledge/adr/DIGEST.md). |
| **Agent** | Программа, использующая LLM для выбора последовательности действий (обычно через tool-calls) ради цели. |
| **Ask Devin** | Фича Devin: скоуп задачи, поиск по кодовой базе и авто-составление высококонтекстного промпта до открытия сессии. |
| **Auto-Fix** | Фича Devin Review: автоматические реакции на комменты ревью и падения CI. |
| **Axis (project)** | Высокоуровневая ось проекта, синоним *Pillar* в формулировке [`project-overview.md` §1.1](../knowledge/project-overview.md#11-четыре-столпа-цели-project-goal--four-pillars). Используется в фразах вроде «follow project axis». |
| **Axis (PR-checklist A/B/C)** | Критерий evaluation в §0 Decision Briefing per [AGENTS.md PR Checklist rule #8](../AGENTS.md#pr-checklist): (A) reduces session-start noise; (B) helps LLM find context; (C) advances chosen `goal_lens`. (A) и (B) — стабильные axes для всех нот; (C) — per-session. |
| **Devin Review** | Автоматический ревьюер PR от Devin. |
| **Draft PR** | PR в draft-состоянии; CI запускается, но он не помечен как ready-for-review. |
| **Eval / Eval suite** | Воспроизводимый набор входов + ожидаемого поведения для измерения качества агента между изменениями. |
| **Feedback loop** | Цикл «действие → наблюдение → рефлексия → следующее действие» — главный паттерн надёжного агента. |
| **goal_lens** | Frontmatter v2 поле; one-sentence research goal, elicited at session start (Stage 1) per [`prompts/research-briefing.md`](../knowledge/prompts/research-briefing.md). Mandatory для нот, произведённых workflow research-briefing; optional для остальных. Лет агенту фильтровать корпус по текущей задаче без загрузки нот. См. также *Lens*. |
| **Golden set** | Маленький, стабильный, размеченный вручную набор входов для регрессионного eval'а. |
| **Harness** | Control layer вокруг LLM: loop / orchestration, prompts, tool registry, retrieval pipeline, sandbox. Это **не** само ML и не модель; это всё, что нужно агенту между LLM-call'ами. Pillar 3 проекта — построить most efficient open-source harness под UC1+UC3. |
| **Hook** | Pre/post-tool extension point. В v0.1 единственный реализованный hook — sandbox check ([ADR-6](../knowledge/adr/ADR-6-tool-sandbox-allow-list.md)); v0.2+ inner-loop ADR (ADR-7) формализует hook-primitive как контракт. |
| **Knowledge note** | Короткая триггерная заметка, которую Devin автоматически подтягивает в будущие сессии. |
| **Lens** | См. *goal_lens*. Используется в фразах вроде «follow project axis and lens goal» — обычно ссылается на `goal_lens:` frontmatter ноты или сессии. |
| **LLM agent** | См. *Agent*. |
| **Managed Devin** | Дочерняя сессия Devin, порождённая другой — обычно для параллелизма. |
| **MCP** | Model Context Protocol — JSON-RPC-shaped контракт между MCP host (агентом) и MCP server (источником tools / resources / prompts). FA v0.1 фиксирует **convention** (in-process dispatcher mirrors JSON-RPC), не dependency — см. [ADR-2 amendment 2026-05-01](../knowledge/adr/ADR-2-llm-tiering.md#amendment-2026-05-01--mcp-forward-compat-tool-shape-convention). В Devin-контексте — также способ подключения Devin к внешним системам. |
| **Minimalism-first** | Project principle ([`project-overview.md` §1.2](../knowledge/project-overview.md#12-enforceable-principle--minimalism-first)): каждый предлагаемый новый harness-компонент проходит 3-вопросный тест перед добавлением (research-evidence; precedent removal; replacement capability). Prevention-стратегия для greenfield-проекта. Контраст: *Subtraction-first*. |
| **Module** | Самодостаточная единица нашего агентского кода в `src/<name>/` со своими тестами и README. |
| **NLAH** | Natural-Language Agent Harness — externalized, editable natural-language artifact, описывающий harness behavior; runtime — Intelligent Harness Runtime (IHR). Source: Tsinghua paper `arXiv:2603.25723`. First-Agent уже имеет text-half NLAH (AGENTS.md + ADR + research notes); ADR-7 — bridge to code-half. См. [`research/efficient-llm-agent-harness-2026-05.md`](../knowledge/research/efficient-llm-agent-harness-2026-05.md). |
| **Phase R / S / M** | Lifecycle stages из [`docs/workflow.md`](./workflow.md): Phase R (Research) → Phase S (Scaffolding, complete) → Phase M (Module creation, **current**). См. также *R-S-M*. |
| **Pillar** | Один из 4 столпов цели проекта в [`project-overview.md` §1.1](../knowledge/project-overview.md#11-четыре-столпа-цели-project-goal--four-pillars): (1) research-backed implementation-first reference; (2) pragmatic single-user product; (3) most token/tool-call efficient open-source harness; (4) iteration via measurement. Синоним *Project axis*. |
| **Playbook** | Переиспользуемый многошаговый рецепт, который Devin может выполнить по требованию. |
| **PRD** | Product Requirements Document. Для First-Agent — короткий markdown под `docs/prd/`. |
| **Prompt** | Инструкция, которую мы шлём LLM (или Devin). Переиспользуемые — в `knowledge/prompts/`. |
| **R-S-M** | Lifecycle: Research → Scaffolding → Module. См. *Phase R / S / M*. |
| **ReAct** | Reasoning + Acting loop — распространённый паттерн оркестрации агента: мысль → действие → мысль. |
| **Scheduled session** | Cron-подобная рекуррентная сессия Devin — напр. еженедельный eval. |
| **Session Insights** | Пост-сессионная аналитика: таймлайн, расход, советы по улучшению промпта. |
| **Skill** | `SKILL.md` в репо — процедура, которую Devin знает как исполнить. v0.1 commitment: способность агента писать собственные skills под `~/.fa/skills/` или `knowledge/skills/` (Pillar 4 база; ADR-8 TBD). |
| **Subtraction-first** | Anthropic-origin design-rule: каждый harness-компонент encode-ит assumption о limitation модели; assumptions expire когда модели улучшаются → удалять компонент, если измерения показывают неэффективность. **Retrofit**-стратегия для legacy harness. FA выбирает *Minimalism-first* (prevention) над subtraction. |
| **Tool call** | Структурированный запрос LLM на вызов именованного инструмента с типизированными аргументами. Native vs prompt-only — см. [ADR-2 amendment 2026-04-29](../knowledge/adr/ADR-2-llm-tiering.md#amendment-2026-04-29--tool_protocol-field--native-by-default-v01-inner-loop-without-critic). |
| **UC1 — UC5** | Use-case labels из [`project-overview.md` §4](../knowledge/project-overview.md#4-scope) + [ADR-1](../knowledge/adr/ADR-1-v01-use-case-scope.md): UC1 coding + PR; UC2 multi-source research (best-effort в v0.1); UC3 local-docs-to-wiki; UC4 Telegram multi-user (deferred v0.2); UC5 eval-driven harness iteration (deferred v0.2; expanded в [ADR-1 amendment 2026-05-06](../knowledge/adr/ADR-1-v01-use-case-scope.md#amendment-2026-05-06--uc5-expanded-to-eval-driven-harness-iteration)). |
| **Vector DB** | База под similarity-поиск по эмбеддингам (Qdrant, pgvector, Pinecone и т.п.). |
