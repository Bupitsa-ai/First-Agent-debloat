---
title: "SLIDERS — Structured Reasoning for Long Document QA (Stanford OV AL, 2026-04) — разбор для FA v0.1+"
compiled: "2026-04-29"
source:
  - https://arxiv.org/abs/2604.22294
  - https://arxiv.org/pdf/2604.22294v1
  - https://sliders.genie.stanford.edu/
  - https://github.com/stanford-oval/sliders
chain_of_custody:
  - "Все цифры (accuracy, token sizes, costs, latency, row counts)
    — из arXiv:2604.22294v1 (Joshi, Shethia, Dao, Lam; Stanford
    OV AL; preprint, submitted 24 Apr 2026). Прочитан полный текст
    PDF (49 страниц), не abstract / summary. Конкретные значения
    помечены ссылками на §-секции paper'а в тексте ноты."
  - "Авторы используют термин «aggregation bottleneck» как
    самостоятельную концепцию (§1, §2). Это термин paper'а, а не
    наша интерпретация."
  - "Цифры benchmark'ов из Table 3 (FB / Loong / Oolong / WC /
    FQ) скопированы дословно. Сравнения с baselines (RAG /
    LongRAG / GraphRAG / DocETL / RLM / Chain-of-Agents) приведены
    в той же таблице, проверены против оригинала."
  - "Cost ($0.76/question average; $13.10 vs $171.60 для
    WikiCeleb100; $34.63 vs ~$1800 для FinQ100) — §7.4 Cost
    Analysis."
  - "Latency (2.6 min / 3 min end-to-end; 16 min offline + 25 sec
    online; GraphRAG 2.3 hours / $182) — §7.5."
  - "Reconciliation iteration count (1.28 avg; 685 → 105 rows
    ground-truth) — §6, §8."
  - "Ablation drop (FinQ100 55.22 → 35.81 без reconciliation;
    WikiCeleb 78.91 → 60.50) — §6 Observation 1 + Appendix
    (table referenced)."
status: research
supersedes: none
extends:
  - knowledge/research/memory-architecture-design-2026-04-26.md  # §10 chunker, §11 retrieval
  - knowledge/research/agentic-memory-supplement.md  # Mem0 latency context
  - knowledge/research/chunker-design.md  # contextualized chunking
related:
  - knowledge/adr/ADR-3-memory-architecture-variant.md
  - knowledge/adr/ADR-4-storage-backend.md
  - knowledge/adr/ADR-5-chunker-tool.md
  - knowledge/research/llm-wiki-community-batch-2.md  # GraphRAG comparison
  - knowledge/research/llm-wiki-critique.md
  - knowledge/project-overview.md
tier: stable
links:
  - "../adr/ADR-3-memory-architecture-variant.md"
  - "../adr/ADR-4-storage-backend.md"
  - "../adr/ADR-5-chunker-tool.md"
  - "./chunker-design.md"
  - "./memory-architecture-design-2026-04-26.md"
  - "./agentic-memory-supplement.md"
  - "./llm-wiki-community-batch-2.md"
  - "./how-to-build-an-agent-ampcode-2026-04.md"
mentions:
  - "SLIDERS"
  - "Stanford OV AL"
  - "GPT 4.1"
  - "Qwen3.5 122B-A10B"
  - "GraphRAG"
  - "LongRAG"
  - "RAG"
  - "DocETL"
  - "Chain of Agents"
  - "RLM (Recursive Language Model)"
  - "FinanceBench"
  - "Loong"
  - "Oolong"
  - "WikiCeleb100"
  - "FinQ100"
  - "Mem0"
  - "SQL"
  - "SQLite"
confidence: extracted
claims_requiring_verification:
  - "Все benchmark-цифры взяты из preprint v1 (24 Apr 2026); paper
    ещё не прошёл peer-review. На момент публикации v2 / camera-
    ready могут уточниться."
  - "Cost ($) измерены по prices Anthropic / OpenAI на момент
    подготовки paper'а; для FA с другими провайдерами/моделями
    эти числа сместятся. Полезен только относительный порядок."
  - "SLIDERS code (github.com/stanford-oval/sliders) не клонирован
    в этой сессии — анализ идёт по paper'у. Перед тем как
    портировать паттерн в FA, надо взглянуть на code (особенно
    schema-induction prompt'ы и reconciliation operation
    selection)."
  - "Утверждение, что reconciliation drop = 78.91 → 60.50 на
    WikiCeleb и 55.22 → 35.81 на FinQ100 — взято из paper'а;
    конкретный ablation table не выкопирован построчно."
  - "Применимость SLIDERS к нашим UC2 (continuous research) и
    UC3 (local-docs-to-wiki) — наша интерпретация, не утверждение
    авторов. Авторы целятся в QA-over-finance, не в coding-agent."
---

# SLIDERS — Structured Reasoning over Long Document Sets (разбор)

> **Статус:** research note, 2026-04-29.
> **Что внутри:** разбор препринта Stanford OV AL «Contexts are
> Never Long Enough: Structured Reasoning for Scalable Question
> Answering over Long Document Sets» (arXiv:2604.22294v1) и
> mapping ключевых паттернов на FA — какие наши ADR (ADR-3 /
> ADR-4 / ADR-5) и use-case (UC2, UC3) затрагиваются.
>
> Это **read-side** нота: paper про то, как агент *читает и
> думает* над большим корпусом. Парная write-side нота —
> [`how-to-build-an-agent-ampcode-2026-04.md`](./how-to-build-an-agent-ampcode-2026-04.md).
>
> Источник — академический препринт. Все ключевые цифры
> приводятся со ссылкой на §-секцию paper'а (chain-of-custody).

## 1. TL;DR

**Тезис paper'а:** даже на корпусах, которые формально
помещаются в context window современных LLM (≤360k токенов на
GPT-4.1), прямой long-context подход проигрывает структурному.
А на корпусах вне окна (3.9M+ токенов) альтернатив структурному
подходу практически нет — большинство baseline'ов либо падают,
либо стоят $1000+ за прогон.

**Решение SLIDERS** (5 этапов; §2):

1. **Contextualized Chunking** — чанки идут с глобальным и
   локальным метаданными (заголовок документа, описание, hierarchy
   секций), чтобы каждый чанк был «locally self-contained».
2. **Schema Induction** — LLM строит реляционную схему `S` из
   вопроса `q` и метаданных документов (имена таблиц, типы полей,
   единицы измерения, нормализация).
3. **Structured Extraction with Relevance Gating** — для каждого
   чанка сначала «relevance gate» (релевантен ли чанк к схеме?),
   и только тогда — extraction. Каждая ячейка хранит `(value,
   provenance, rationale)`.
4. **Data Reconciliation** — отдельный SQL-coding agent
   объединяет извлечения через primary-key partitioning и три
   операции: deduplication / conflict resolution / consolidation.
5. **Question Answering** — финальный answer-synth-агент пишет
   SQL-запросы по очищенной БД, итеративно (schema → запрос →
   результат → уточнение).

**Ключевые цифры (Table 3 + §7):**

| Benchmark | Tokens | Лучший baseline | SLIDERS | Δ |
|---|---|---|---|---|
| FinanceBench (FB) | ≤360k | 84.67 (Qwen base) | 89.33 | +4.7 |
| Loong | ≤360k | 76.74 (GPT-4.1 base) | 78.57 | +1.8 |
| Oolong (256k) | ≤360k | 51.42 (RLM) | 64.67 | +13.3 |
| WikiCeleb100 | 3.9M | 59.80 (RLM) | 78.91 | +19.1 |
| FinQ100 | 36M | 31.41 (RAG) | 55.22 | +23.8 |

Cost: SLIDERS ≈ $0.76 / question avg. Для WikiCeleb100 — $13.10
SLIDERS vs ~$171.60 GPT-4.1 full-context (§7.4). Для FinQ100 —
$34.63 SLIDERS vs ~$1800 estimated GPT-4.1 (RLM на 10 документах
из 100 уже обходился в ту сумму).

Ablation: убери reconciliation — точность падает с 78.91 до 60.50
на WikiCeleb и с 55.22 до 35.81 на FinQ100 (§6 Obs.1).

**Что это для FA.** Самая прямая релевантность — это
формулировка **«aggregation bottleneck»**, которая ровно
описывает, почему naive RAG ломается на больших корпусах. ADR-3
(Variant A — mechanical wiki без embeddings) уже намекает на
тот же вывод эмпирически («grep / FTS5 пока хватает»), но
SLIDERS даёт пейпер-уровень обоснование того, что **на корпусах
свыше ~360k токенов нужна не просто retrieval-надстройка, а
структурное промежуточное состояние**. Это сильный сигнал для
v0.2+ roadmap'а: когда FA-corpus переваливает за low-MB
текстовых файлов, **vector layer не спасёт** — нужен extraction
→ DB-pipeline.

В рамках v0.1 SLIDERS **не противоречит ADR-3** (Variant A) и
**не отменяет ADR-4** (SQLite FTS5). Variant A осознанно
останавливается на «mechanical wiki + grep/BM25»; SLIDERS — это
**Variant D**, которого пока в плане нет, но он логично
дописывается поверх ADR-4 (та же SQLite-инстанция, новый набор
таблиц с `entities` / `facts` / `provenance`).

## 2. Что такое «aggregation bottleneck» и почему он важен

Авторы вводят термин (§1, см. также Figure 1 paper'а):

> *«[…] as the number of chunks increases, systems must aggregate
> and reason over evidence extracted from all chunk outputs,
> ultimately recreating the very long-context problem it was
> designed to avoid, a fundamental limitation we refer to as the
> "Aggregation Bottleneck".»*

Цепочка рассуждений:

1. Корпус большой — модель не вмещает его целиком в context.
2. Делим на чанки и извлекаем «evidence» из каждого.
3. На финальном aggregation step'е нужно **снова собрать всю
   evidence** в один промпт.
4. Чем больше чанков — тем больше evidence — тем ближе мы к
   исходной проблеме.
5. И ещё хуже: evidence обычно **дубликаты, конфликты,
   incomplete** (один документ говорит «date of birth: 1988-01-
   01», другой — «born late January 1988», третий — даёт только
   возраст). Naive concat теряет информацию или плодит
   противоречия.

**Почему это важно для FA.**

ADR-3 §Decision гарантирует Variant A: «filesystem canon +
grep / FTS5 / future-vectors». В режиме «один пользователь, один
workstation» это работает, пока корпус ≤ ~10 MB текста. Как
только мы прицепим UC2 (continuous multi-source research) или
UC3 при росте `notes/inbox/`, мы упрёмся в ровно тот же
aggregation bottleneck — даже при идеальном чанкере и идеальном
BM25.

`memory-architecture-design-2026-04-26.md` §11 уже обсуждал «когда
включать vector layer». Эта paper показывает, что **vector layer
не закроет вопрос, если задача — aggregation, а не lookup**.
Нужно структурированное промежуточное состояние — реляционная
БД с явными колонками, нормализацией и primary keys.

Связь с тем, что у нас есть в `agentic-memory-supplement.md` §1
(Mem0): Mem0 решает другую задачу — *update-time* память
(extract-then-update facts). SLIDERS — *read-time* extraction.
Они не взаимоисключающие; в идеальном будущем они дополняют
друг друга (Mem0-стиль extraction в hot.md → SLIDERS-стиль
extraction over inbox/ for QA).

## 3. Пять этапов SLIDERS — детально

### 3.1 Contextualized Chunking

Каждому документу `d` назначаются метаданные `m_d = (m_G, m_L)`:

- **`m_G` (global)** — сгенерированный заголовок документа +
  краткое описание; делятся между всеми чанками этого документа.
- **`m_L` (local)** — структурные сигналы: section headers,
  таблицы, captions. Используются для границ чанков.

Чанки идут «locally self-contained» — нельзя резать поперёк
параграфа, таблицы, code-block'а. Каждый чанк содержит:

- doc identifier
- page indices
- structural tags (section path)

**Связь с ADR-5 (chunker-design.md).** Наш ADR-5 уже принял
universal-ctags + markdown-it-py — то есть «structural-aware
chunker, не наивный sliding window». Это **совпадает по
философии с SLIDERS §3.1**, но мы у себя пока **не генерируем
`m_G`** (LLM-generated title + description). Зато имеем
`metadata.json`-frontmatter в самих файлах (knowledge/, docs/),
что эквивалентно для нашей семантической области.

**Open question для FA:** надо ли поднимать `m_G`-генерацию
(LLM-title + description) для файлов, которые user'ом не
аннотированы (например, отнесённые в `notes/inbox/` PDF'ы)?
SLIDERS отвечает: «да, иначе чанки теряют context». Для v0.1
это вне scope (UC3 ограничен «text-format files»), но для v0.2
с PDF/web-docs — это явное design-decision, которое нужно ADR.

### 3.2 Schema Induction

> *«Unlike free-form extraction, where an LLM generates
> unstructured natural language responses for each document
> chunk, relational databases fundamentally differ in their
> requirement for a rigid schema.»*  (§3.2)

Schema = набор таблиц `S = {S_1, …, S_k}`, каждая таблица —
имя + поля. Каждое поле:

```text
f = ⟨ field_name, description, type, unit, scale, normalization ⟩
```

Например, для FinanceBench-вопроса «accounts payable BIOLARGO?»:

```yaml
table: AccountsPayable
fields:
  - company_name (str)
  - reporting_period (date, normalize: mm-dd-yyyy)
  - accounts_payable (float, scale: thousands)
  - currency (str)
```

Авторы ведут **schema-guidelines library** — LLM сначала
классифицирует тип вопроса (Ordering / MultipleChoice / Other) и
тип документа (Narration / Policy / Dataset / Other), потом
извлекает гайдлайны и строит схему по ним.

**Стабильность.** §7.2 показывает: при подмене schema-induction
модели (GPT-4.1 / GPT-4.1-mini / GPT-5) средняя точность Loong
сдвигается на ≤ 2.1 points; FinanceBench — на ≤ 3.3 points.
Несмотря на 4× разницу в сложности схем (1.0 таблица × 3.3 поля
у GPT-4.1 vs 1.54 × 13.3 у GPT-5). Это значит, что
**schema-induction — не fragile bottleneck**.

**Что это для FA.** В Variant A у нас нет schema-induction — мы
канонизируем тексты «as is» через filesystem + frontmatter. Если
в v0.2 мы добавим SLIDERS-style extraction layer, то имеем
свободу: brittle-but-precise схема (минимум полей, максимум
гарантий) vs verbose-but-redundant. Стабильность результата к
выбору модели — хорошая новость для нашего ADR-2 tiering'а:
schema-induction можно отдать дешёвой Planner-роли.

### 3.3 Structured Extraction with Relevance Gating

Hallucination — главный враг strict-schema extraction. Авторы
наблюдают (§3.3): когда чанк не содержит информации для
полей схемы, модель часто **придумывает** значения, чтобы
«заполнить таблицу». Тренинг bias'ы поощряют structural
compliance over factual abstinence.

**Решение** — `relevance gate` `R(q_e, c, m_c, S)`:

```text
SE(q_e, c, m, S) =
    SE_LLM(q_e, c, m, S),  if R(q_e, c, m, S)
    ∅,                      otherwise
```

То есть: сначала отдельный prompt спрашивает у LLM «есть ли в
этом чанке хоть какая-то evidence по полям схемы?» — и только
при «да» делается extraction.

**Эффективность gate'а** (§3.3 last paragraph; цитата):

> *«We evaluate the relevance gate on 20 sampled incorrect
> predictions across benchmarks. Across 516 chunks, 282 were
> rejected by the relevance gate, with only 1 false negative,
> yielding a false-negative rate of 0.4%.»*

То есть gate отбраковал ~55% чанков с false-negative rate 0.4%.
Это **очень дешёвый способ срезать половину extraction-cost'а**
без потерь точности.

**Что это для FA.** Чисто extraction-уровень, в Variant A не
нужен. Но идея «двух промптов: gate → action» применима шире:
например, **в Coder-роли (UC1)** перед редактированием файла —
дешёвый gate-prompt «нужно ли вообще менять этот файл для решения
задачи?» вместо дорогого «придумай patch». Это тривиальный
паттерн, но paper его явно валидирует на цифрах.

### 3.4 Data Reconciliation

Эта часть paper'а — самое интересное. Reconciliation =
**превращение N независимых extraction-результатов в одну
согласованную таблицу**.

Naive подход — pairwise comparison всех записей — O(N²). Авторы
обходят это через **primary-key partitioning** (Algorithm 1):

```text
1. Адаптировать вопрос q для reconciliation → q_r
2. Для каждой таблицы T_i:
   a. SELECT_PRIMARY_KEY(T_i, q_r)  # doc-level, потом table-level
   b. RESOLVE_PRIMARY_KEY_ENTITIES(T_i, pk_i)  # "J. Smith" = "John Smith"
   c. GROUP_BY_PRIMARY_KEY(T_i, pk_i)
3. Для каждой группы g параллельно:
   loop:
       op = SELECT_RECON_OP(g, q_r)  # одна из {dedup, conflict, consolidate}
       if op == None: break
       sql = RECONCILE_GROUP(g, op)  # LLM пишет SQL
       g = APPLY(sql, g)
4. RESOLVE_NON_PRIMARY_KEY_ENTITIES(...)
```

**Три операции** (Table 1 paper'а):

| Operation | Need | Decision Making | Action |
|---|---|---|---|
| Deduplication | Семантически идентичные / почти идентичные строки в разных формулировках | Выбрать самое точное / explicit значение по provenance | Schon canonical-row, остальные удалить |
| Conflict Resolution | Конкурирующие значения для того же атрибута | Изучить provenance / rationale, выбрать наиболее обоснованное | Оставить best-supported, остальные удалить |
| Consolidation | Partial-rows с комплементарными атрибутами | Можно ли объединить без противоречий? | Слить в одну запись, propagate shared values |

**Почему primary-key partitioning работает.** Цитата (§4):

> *«our key observation is that reconciliation can exploit the
> structure of text itself: although information is distributed
> across sections, statements about the same entity or claim
> are typically anchored by a common identifying attribute or
> set of attributes.»*

То есть: у статей про одного человека primary key — имя; у 10-Q
filings — `(company_name, fiscal_period)`; у research-papers —
title. Группа по PK — это «всё, что мы извлекли про эту
сущность», и она маленькая (часто 1–5 строк). На маленькой группе
LLM прекрасно справляется с reconciliation.

**Effectiveness** (§6, §8):

- Среднее число итераций reconciliation per PK group — **1.28**
  (т.е. почти всегда одна операция и стоп).
- На FinQ100: 685 extracted rows → ~105 ground-truth rows
  (≈84% сжатия). Без reconciliation — точность падает на 19+
  и 23+ пункта на двух больших benchmark'ах.
- Reconciliation **критична** на ultra-long; на коротких
  (Loong Legal) почти ничего не делает (документы коротче
  чанка, дублей нет).

**Что это для FA.**

В Variant A у нас нет reconciliation, потому что нет extraction
layer'а. Но **в существующем `knowledge/` есть `supersedes:`
field**, который ровно про это: «эта старая нота `superseded_by`
новой; не трогать, оставить для audit». Это **manual
reconciliation** для knowledge-folder'а.

Если в v0.2 добавится auto-extraction (например, FA сам начнёт
извлекать `(date, person, claim)` triples из `notes/inbox/`),
**нам понадобится автоматическая reconciliation**. Идея «PK-
partitioning + три операции» — готовый pattern.

### 3.5 Question Answering — SQL Loop

Финальный QA-агент (§2, task 5):

1. Дана reconciled DB и schema.
2. Сгенерировать SQL.
3. Выполнить.
4. Если результат не удовлетворяет — refine SQL и повторить.

Это **тот же inner-loop, что у Ball'а** (см. парную ноту), но
вместо `read_file` / `edit_file` инструмент — `execute_sql`.
Аналогично с tool-use по Ball'у: модель пишет SQL, runtime
исполняет, возвращает результат, модель решает что дальше.

**Что это для FA.** Если/когда мы добавим extraction-layer (v0.2),
нам **тривиально** добавить SQL-tool в Coder/Planner — у нас уже
SQLite (ADR-4), у нас уже tool-loop (после implementation
chunker+agent-loop). SLIDERS подтверждает, что эта связка работает
end-to-end на реальных задачах.

## 4. Сравнение с baselines (Table 3 paper'а)

Цифры — ровно как в Table 3 (выровнено для читаемости):

```text
                          FB      Loong    Oolong  Avg.
RAG (Qwen3-4B+GPT4.1)     62.67   54.35    11.32   42.77
LongRAG                   72.00   59.10    22.00   51.03
GraphRAG                  75.33   61.28    22.00   52.87
GPT-4.1 base              82.00   76.74    45.56   68.69
Qwen3.5 122B-A10B base    84.67   74.78    24.89   61.44
DocETL                    63.33   75.03    49.00   62.44
Chain-of-Agents (GPT5)    71.30   54.46    17.11   47.62
RLM (GPT5+GPT5-mini)      75.33   72.64    51.42   66.46
SLIDERS (GPT4.1)          89.33   78.57    64.67   75.56
SLIDERS (Qwen3.5)         82.10   75.70    68.00   75.26
```

И на ультра-длинных (3.9M / 36M токенов):

```text
                          WikiCeleb100  FinQ100
RAG                       31.41         5.00
LongRAG                   43.20         28.87
GraphRAG                  48.59         $$ (too expensive)
DocETL                    54.26         $$
RLM                       59.80         (estimated $2000 to run; ran on 10/100)
SLIDERS (GPT4.1)          78.91         55.22
SLIDERS (Qwen3.5)         76.92         60.18
```

`$$` = prohibitively expensive (paper's notation).

**Наблюдения автора (§5.3):**

- Obs.1: SLIDERS обходит **все** baseline'ы даже когда вход
  помещается в context window. Paired t-test против сильнейшего
  baseline (GPT-4.1) — `p < 0.005` на всех benchmarks.
- Obs.2: provenance / extraction rationale в SLIDERS делают
  ошибки **аудируемыми** — можно ткнуть в строку БД и увидеть,
  откуда пришло значение.
- Obs.3: SLIDERS работает и на open-source LM (Qwen 122B). Это
  валидирует, что выгода — от framework'а, не от GPT-4.1.

**Что это для FA.** Самые два важные takeaway'а:

1. **«Long-context работает плохо даже когда работает.»**
   GPT-4.1 на FB / Loong / Oolong имеет 1M context — и всё
   равно проигрывает SLIDERS на 6.6 points в среднем. То есть
   «давайте просто увеличим context» — **не** долгосрочная
   стратегия.
2. **OSS-параллель.** SLIDERS на Qwen 122B держит примерно тот
   же уровень, что на GPT-4.1. Это совместимо с ADR-2: мы
   планируем mix локальных и облачных моделей. Если в v0.2 мы
   решим вкатить SLIDERS-style extraction, ADR-2 не нужно
   переписывать — Qwen-class модели в нашем local-tier
   справляются.

## 5. Cost / Latency / Auditability

### 5.1 Cost (§7.4)

- Среднее по бенчмарках — **$0.76 / question** для SLIDERS.
- ~40% этого — Entity Resolution (всю таблицу сканить).
- Comparison: WikiCeleb100 — $13.10 SLIDERS vs $171.60 GPT-4.1
  hypothetical full-context. FinQ100 — $34.63 vs ~$1800.
- **Amortization key**: extraction + reconciliation делается
  один раз; следующие вопросы по тому же корпусу — только
  schema-guided SQL (≈25 sec online).

### 5.2 Latency (§7.5)

End-to-end (single query, no reuse):

- **Loong**: 2.6 min average per question.
- **FinanceBench**: 3.0 min average per question.

Amortized (offline + online):

- WikiCeleb100 offline: ~16 min (schema 20s + extraction 6 min
  parallel + reconciliation 9.7 min).
- WikiCeleb100 online: **~25 sec / question**.
- Comparison GraphRAG indexing: **2.3 hours, $182** для того
  же corpus'а; и при этом всего **48.59% accuracy** против
  78.91% у SLIDERS.

### 5.3 Auditability (§5.3 Obs.2, §6 Obs.2)

Каждая extracted-row хранит:

- `value` (нормализованное)
- `provenance` (минимальный текстовый span из исходного
  документа, поддерживающий значение)
- `rationale` (LLM-generated «почему я считаю, что value
  соответствует quote»)

При ошибке reviewer быстро находит её причину: либо provenance
неверен (chunking-issue), либо rationale нелогичен (extraction-
issue), либо reconciliation пошла не туда. Авторы пишут:

> *«SLIDERS can significantly streamline the manual workflow:
> a reviewer can use the tracked provenance to validate and
> correct each document's extractions much faster than working
> from scratch.»* (§6 Obs.2)

**Что это для FA.** Это **прямо** то, что наш `chain_of_custody`
field в frontmatter (см. `knowledge/README.md` §«Provenance-
frontmatter») пытается обеспечить — но на ручном уровне. SLIDERS
показывает, как тот же принцип масштабируется на автоматическое
извлечение. Хорошее доказательство, что наш design choice
(provenance-frontmatter — обязательная часть схемы) **right
direction** для extraction-layer'а v0.2.

## 6. Маппинг на ADR FA

Сводка по нашим текущим ADR:

| ADR | Тема | SLIDERS говорит | Действие |
|---|---|---|---|
| ADR-1 | UC1 (coding+PR) и UC3 (docs-to-wiki) in scope, UC2 best-effort | Не противоречит. UC2 — ровно та задача, на которой SLIDERS бьёт baseline'ы | Подтверждает: UC2 полноценно в v0.2 потребует SLIDERS-style |
| ADR-2 | Static role routing | Schema-induction стабильна к выбору модели (§7.2). OSS-LM (Qwen) держит уровень | Совместимо. В будущем добавить роль `Extractor` (cheap-tier) и `SQLAgent` (mid-tier) |
| ADR-3 | Variant A (mechanical wiki, no embeddings) | Подтверждает: для маленьких corpora vector layer — overkill. Но при росте corpus — нужна **другая** надстройка (Variant D = extraction layer), а не vectors | Не отменяет ADR-3. Но добавляет в roadmap «когда корпус выйдет за hot threshold — extraction-layer» |
| ADR-4 | SQLite FTS5 | SQLite — отличный subbase. SLIDERS использует SQL поверх реляционной БД; SQLite это умеет нативно | Не меняет ADR-4. В v0.2 расширить схему: рядом с `chunks` появятся `entities`, `facts`, `provenance`, `rationale` |
| ADR-5 | universal-ctags + markdown-it-py | Совместимо. SLIDERS подчёркивает важность layout-aware chunking — то, что мы и так делаем | Подтверждает ADR-5. Дополнительно: для UC3-PDF (v0.2) понадобится heading+table-aware chunker, ctags не справится |

**Главный вывод:** SLIDERS **не требует** изменений ни в одном
из принятых ADR. Но он рисует чёткую картину **что добавляется**,
когда v0.1 закроется и начнётся v0.2:

1. Extraction layer (новый модуль `src/fa/extract/`).
2. Schema-induction prompt + library (новый `knowledge/schemas/`).
3. Reconciliation agent (новый модуль `src/fa/reconcile/`).
4. SQL-tool в registry (`src/fa/tools/execute_sql.py`).
5. Возможно — provenance-обогащение чанков уже в v0.1, чтобы
   v0.2 было легче.

## 7. Тонкие места и risks

### 7.1 Schema-induction зависит от вопроса

SLIDERS строит схему **на основе вопроса** `q`. То есть для
каждого нового вопроса — новая схема и новая extraction. Это
нормально для one-shot QA, но **не масштабируется** для FA-
сценария «пользователь приходит с десятками разных вопросов
по тому же корпусу».

Авторы это обходят (§6) через **amortization mode**: для
WikiCeleb100 они генерируют **одну** схему по «representative
question» («Who debuted at the youngest age across the following
industries…»), извлекают по ней — и потом эта схема
поддерживает ещё 21 вопрос на ту же тему. То есть вопросы
**топик-кластеризованы** заранее.

**Что это для FA.** Если делать SLIDERS-style в FA, нужно
**заранее определить топики корпуса** (или дать пользователю
команду «пересоздать схему под новый топик»). Pipeline:

```text
ingest --topic finance ./reports/    # extract + reconcile
ingest --topic tasks   ./inbox/      # отдельная схема
ingest --topic wiki    ./notes/      # ещё отдельная
ask --topic finance "lowest long-term borrowing?"
```

Это **дополнительная UX-сложность** для UC3. Не блокер для
v0.2, но дизайн-точка.

### 7.2 Cost frontier'а сжимается

Все cost-цифры paper'а — на frontier-моделях (GPT-4.1,
GPT-5, Qwen 122B). Для домашнего FA с локальной 14B-моделью
относительные цены и абсолютная latency будут другими. Но
структурное преимущество (amortization, parallel extraction,
SQL-aggregation) **не зависит от размера модели**.

### 7.3 Errors that SLIDERS makes (§5.3 Obs.2)

Авторы честно перечисляют классы ошибок:

- Subjective-judgement questions (модель не справляется с
  «которая компания больше пострадает от рецессии»).
- Terminology mismatch (fiscal vs calendar year).
- Errors in **gold answers** — иногда ground-truth неполный
  или с неверными units.

Третье — отдельная боль для FA: когда мы построим eval-набор
для UC3, нам нужно **либо очень аккуратно его аннотировать,
либо мириться с тем, что ground-truth часто неполный**. См.
ADR / project-overview §3 «no labelled gold-set yet».

### 7.4 Reconciliation operations — три класса хватает?

Авторы фиксируют три (dedup / conflict / consolidate). На
большем разнообразии корпусов возможно понадобятся:

- **Temporal supersession** — «10-Q filing 2024-Q3 заменяет
  10-Q 2024-Q2 для same company».
- **Aggregation** — несколько partial-numbers → sum.
- **Translation** — multilingual reconciliation.

Paper об этом не пишет, но Loong-Chinese benchmark'и косвенно
это поднимают.

### 7.5 Privacy / data-leak

Прямо в paper'е не обсуждается. В FA-контексте: **schema-induction
+ extraction отправляют сэмплы документа в LLM**. Если
пользователь хочет UC3 над **личными заметками**, схема может
включать имена / адреса / etc. ADR-2 (LLM-tiering) должен
учитывать «чувствительный корпус → только локальная модель».

## 8. Конкретные действия для FA (не утверждённые)

Не ADR, но шорт-лист входных пунктов на следующие итерации:

1. **В `chunker-design.md` §… — добавить ссылку на SLIDERS
   §3.1** как paper-level подтверждение «layout-aware chunking
   с document-level metadata». **Не блокер для chunker-PR**,
   просто связать chunker-design.md и эту ноту.
2. **В ADR-3 §Consequences (или ADR-3 §Future Variants) —
   добавить «Variant D: extraction-layer (SLIDERS-style)»**
   как явный кандидат на v0.2, чтобы при росте corpus'а у нас
   был сигнал, куда двигаться.
3. **Сохранять provenance в чанках уже в v0.1.** Это дешёвое
   расширение схемы `chunks` (добавить колонки `source_path`,
   `byte_offset`, `parent_doc_title`). Эти поля **не использу-
   ются** в Variant A, но без них v0.2 extraction-layer
   придётся переиндексировать с нуля.
4. **Eval gold-set: phrase-level gold, не just answer-level.**
   SLIDERS-paper предупреждает: ошибки часто в gold. Если мы
   делаем gold-набор, делаем его с провенансом — где в
   документе живёт правильный ответ.
5. **Topic-aware ingest** (на v0.2-горизонте). Команда `fa
   ingest --topic <name> <path>` уже сейчас полезна — она
   позволяет хранить provenance-источники по топикам и
   амортизировать схему extraction'а.

## 9. Цитаты, имеющие смысл повторить дословно

> *«[…] the very long-context problem it was designed to avoid,
> a fundamental limitation we refer to as the "Aggregation
> Bottleneck".»* (§1)

> *«Our key observation is that reconciliation can exploit the
> structure of text itself: although information is distributed
> across sections, statements about the same entity or claim
> are typically anchored by a common identifying attribute or
> set of attributes.»* (§4)

> *«SLIDERS achieves 78.91% on WikiCeleb100, an accuracy similar
> to the "long-context" benchmark despite the 3.9M token input
> size. It improves over the best baseline RLM by 19.1%, while
> being 13× more cost efficient.»* (§6 Obs.1)

> *«Although SLIDERS achieves state-of-the-art accuracy, it is
> not reliable enough for end-to-end automation. Nevertheless,
> SLIDERS can significantly streamline the manual workflow.»*
> (§6 Obs.2)

Третья — особенно полезная для FA: SLIDERS **не утверждает**,
что заменяет аналитика. Он встраивается в human-in-the-loop
workflow. Это совпадает с нашей ставкой в ADR-1: UC1 пишет PR,
который пользователь читает; UC3 синтезирует ответ, который
пользователь проверяет. **Авто-mode без human review — не цель.**

## 10. Связь с другими нашими нотами

- [`agentic-memory-supplement.md` §1](./agentic-memory-supplement.md)
  Mem0 — пишущая часть памяти (ADD/UPDATE/DELETE/NOOP). SLIDERS
  — читающая часть памяти на больших корпусах. Они дополняют
  друг друга на разных горизонтах: Mem0 — для conversation-state,
  SLIDERS — для document-corpus.
- [`memory-architecture-design-2026-04-26.md` §11](./memory-architecture-design-2026-04-26.md)
  обсуждал «когда vectors». SLIDERS даёт аргумент: для
  aggregation — не vectors, а structured extraction.
- [`chunker-design.md` §3](./chunker-design.md) уже принимает
  layout-aware chunker. SLIDERS подтверждает, что это
  правильный путь.
- [`llm-wiki-community-batch-2.md` §GraphRAG](./llm-wiki-community-batch-2.md)
  обсуждал GraphRAG. SLIDERS-paper напрямую сравнивает с
  GraphRAG (Table 3) — и обходит его на 18+ points в среднем
  при 13× меньшей стоимости. Это релевантный сигнал, если мы
  думали в сторону Graph-вариантов в v0.2+.
- [`how-to-build-an-agent-ampcode-2026-04.md`](./how-to-build-an-agent-ampcode-2026-04.md)
  — парная нота, write-side. Вместе они закрывают write
  (агент-loop) и read (extraction-layer).

## 11. Ограничения этой ноты

- **Один paper.** Cross-validation против independent
  re-implementations не было. Ждём peer-review / replications.
- **Финансово-ориентированные benchmarks.** 4 из 5 benchmark'ов
  — финансовые / wiki-like. Применимость к code-corpus'у (наш
  главный кейс UC1) **не валидирована**. Code имеет другую
  структуру (call-graphs, type-hierarchies); reconciliation
  через primary-key partitioning может работать иначе.
- **Не клонировали upstream.** github.com/stanford-oval/sliders
  не разбирали построчно. Перед серьёзной попыткой портировать
  паттерн — посмотреть на конкретные prompt-template'ы и
  schema-guideline-library.
- **Нет наших собственных measurements.** Все cost / latency —
  по paper'у, не воспроизведено.
- **Дата.** Paper свежий (24 Apr 2026); v2 может уточнить
  цифры или таблицы. Перепроверить ссылки и числа перед тем,
  как опираться на них в ADR-формате.
