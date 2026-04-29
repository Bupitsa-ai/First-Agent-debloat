---
title: "How to Build an Agent (Thorsten Ball / Amp) — разбор для FA v0.1"
compiled: "2026-04-29"
source:
  - https://ampcode.com/notes/how-to-build-an-agent
  - https://github.com/anthropics/anthropic-sdk-go
  - https://github.com/invopop/jsonschema
chain_of_custody:
  - "Все цитируемые цифры (~400 lines, 90 lines after Run, ~190 lines
    после list_files, 300 lines в финале, 3 tools) — из оригинальной
    статьи Thorsten Ball, опубликованной 2025-04-15 на ampcode.com.
    Прочитан полный текст статьи (HTML → markdown, 940 строк), не
    summary. Цитируемые блоки кода скопированы дословно и
    маркированы английским языком, чтобы не терять провенанс."
  - "Утверждения о Claude 3.7 Sonnet (Latest) и его поведении при
    string-replace edit — из текста статьи (§«edit_file»: «Claude
    3.7 loves replacing strings»), это эмпирическое наблюдение
    автора, не paper-claim. Не реплицировано нами."
  - "Паттерн tool definition (`name`/`description`/`input_schema`/
    `function`) и tool-use loop соответствуют публичному API
    Anthropic Messages (tool_use / tool_result content blocks) —
    не специфичен для Amp."
status: research
supersedes: none
extends: []
related:
  - knowledge/research/agent-roles.md
  - knowledge/research/agent-video-research.md
  - knowledge/adr/ADR-1-v01-use-case-scope.md
  - knowledge/adr/ADR-2-llm-tiering.md
  - knowledge/project-overview.md
  - docs/architecture.md
tier: stable
links:
  - "../adr/ADR-1-v01-use-case-scope.md"
  - "../adr/ADR-2-llm-tiering.md"
  - "../project-overview.md"
  - "./agent-roles.md"
  - "./sliders-structured-reasoning-2026-04.md"
mentions:
  - "Anthropic"
  - "Claude 3.7 Sonnet"
  - "Thorsten Ball"
  - "Amp"
  - "Sourcegraph"
  - "anthropic-sdk-go"
  - "invopop/jsonschema"
confidence: extracted
claims_requiring_verification:
  - "«400 строк хватает на работающий code-editing-agent» — заявлено
    в статье; не реплицировано локально на нашем железе. Перед тем
    как опираться на эту цифру в ADR-формате, нужно собрать пример
    самостоятельно (Go + ANTHROPIC_API_KEY) и измерить."
  - "Утверждение «Claude 3.7 любит string-replace» — может протухнуть
    при смене модели (Claude 4, Sonnet/Opus, Haiku) или провайдера
    (OpenRouter / OSS). Перед тем как фиксировать `edit_file` через
    string-replace в FA, нужно прогнать 5–10 фикстурных правок на
    каждой целевой модели из ADR-2."
  - "Inner-loop из статьи однопоточный и не учитывает (а) parallel
    tool calls, (б) tool-use streaming, (в) лимиты context window.
    Для FA это всё значимо — придётся достраивать."
---

# How to Build an Agent (ampcode.com) — разбор для FA v0.1

> **Статус:** research note, 2026-04-29.
> **Что внутри:** короткая, очень концентрированная статья
> Thorsten Ball / Sourcegraph Amp о том, что «code-editing agent»
> — это `LLM + цикл + достаточно токенов» и помещается в ~400
> строк Go. Разбираем micro-архитектуру inner-loop и три базовых
> tool'а (`read_file` / `list_files` / `edit_file`); затем
> мапим на ADR-1/ADR-2 и обсуждаем, что переиспользуем и что
> придётся достроить для FA.
>
> Источник — single-author blog-post, не paper. Цитируется как
> primary для архитектурного паттерна, но без статистики.

## 1. TL;DR

Главный тезис автора: **«It's an LLM, a loop, and enough tokens»**.
Никакого секрета внутри современного coding-agent нет — есть
inference-цикл, регистрируемые tool'ы и feedback от их
исполнения.

Минимальный «working code-editing agent» автор собирает за
~400 строк Go (с учётом импортов и обвязки) на Anthropic SDK.
В нём ровно три инструмента:

1. **`read_file`** — читает файл по относительному пути.
2. **`list_files`** — листает директорию (директории помечаются
   trailing-slash'ом «как мне удобно, формат можно любой»).
3. **`edit_file`** — редактирует файл через строковую подстановку
   `OldStr → NewStr` (если файла нет и `OldStr == ""` — создаёт).

Tool-use loop: ассистент возвращает `content[i].Type == "tool_use"`
→ runtime ищет tool по имени, исполняет, оборачивает результат
в `tool_result` block, добавляет в conversation, дёргает
inference *без* ожидания нового user-input. Если в ответе
нет `tool_use` — управление возвращается пользователю.

**Что важно для FA.** Эта статья — самая дешёвая существующая
демонстрация того, что **UC1 (coding+PR) сводится к тому же
inner-loop'у** независимо от выбора модели/языка/SDK. ADR-2
(static role-routing Planner/Coder/Debug/Eval) уже фиксирует
структуру выше этого слоя, но не описывает сам цикл — статья
закрывает этот пробел концептуально и даёт конкретный
reference-implementation на ~300 строк, который мы можем
скопировать as a starting shape, переписать на Python и
расширить.

## 2. Определение «агента» от Ball

> *«An LLM with access to tools, giving it the ability to modify
> something outside the context window.»*
>
> — Thorsten Ball, [How to Build an Agent](https://ampcode.com/notes/how-to-build-an-agent)

Это определение узкое и операциональное. Из него следует:

- Без tools'ов это просто chat. Tools — то, что превращает
  LLM в агент.
- «Modify something outside the context window» — это и про
  файлы, и про сеть, и про БД, и про чужие процессы. Tool —
  любая функция с описанием, JSON-schema входа и побочным
  эффектом.
- Memory сюда не входит. У Ball'а conversation
  накапливается в локальном слайсе и заново шлётся при каждой
  итерации. Anthropic-сервер stateless.

Это **совместимо** с нашим определением FA в `project-
overview.md` §1: filesystem-canon + tools + LLM-loop. У нас
дополнительно есть memory-уровень (Variant A / Mechanical
Wiki, ADR-3) и role-routing (ADR-2), но **inner-loop сам
по себе совпадает с тем, что описывает Ball**.

## 3. Inner-loop в одну страницу

Псевдокод (адаптация Ball'а на Python-нотации, чтобы
не зависеть от Go-SDK):

```text
conversation = []
read_user_input = True

while True:
    if read_user_input:
        user_input = stdin.readline()
        if not user_input: break
        conversation.append(UserMessage(text=user_input))

    message = client.messages.create(
        model=ROLE_TO_MODEL[role],
        tools=[tool.schema for tool in tools],
        messages=conversation,
    )
    conversation.append(message.as_param())

    tool_results = []
    for block in message.content:
        if block.type == "text":
            print_assistant(block.text)
        elif block.type == "tool_use":
            tool = registry[block.name]
            try:
                output = tool.fn(block.input)
                tool_results.append(ToolResult(block.id, output, ok=True))
            except Exception as e:
                tool_results.append(ToolResult(block.id, str(e), ok=False))

    if not tool_results:
        read_user_input = True
        continue

    # модель попросила tool — НЕ берём новый user input,
    # просто продолжаем цикл с tool_results в conversation
    read_user_input = False
    conversation.append(UserMessage(content=tool_results))
```

**Ключевые свойства:**

- **Conversation накапливается локально**. Ball подчёркивает
  многократно: «The server is stateless. It only sees what's
  in the `conversation` slice. It's up to us to maintain that.»
- **Tool-use отправляется в conversation как user-message с
  `tool_result` блоками** (это требование Anthropic API,
  не выдумка автора).
- **`read_user_input = False` после tool'а** — критично,
  именно эта строчка превращает chat в агент. Иначе после
  каждого tool-call'а агент будет ждать «продолжай» от
  человека.
- **Ошибка инструмента возвращается моделью же** — `is_error: true`
  у `tool_result`. Модель часто умеет восстановиться сама
  (попробовать другой путь, переформулировать запрос).

Этот цикл занимает у Ball'а ~50 строк Go. У нас на Python с
тайпами уйдёт примерно столько же.

## 4. Tool definition: четыре поля

Ball формализует tool как:

```go
type ToolDefinition struct {
    Name        string
    Description string
    InputSchema anthropic.ToolInputSchemaParam
    Function    func(input json.RawMessage) (string, error)
}
```

Соответствие у Anthropic API: эти четыре поля транслируются в
`tool` block:

- `name` — стабильный строковый идентификатор (snake_case).
- `description` — текст на естественном языке: что делает,
  когда вызывать, когда **не** вызывать, что возвращает.
  Это **самый дорогой по elbow grease поле**: качество
  агента сильно зависит от того, насколько модель понимает,
  *когда* tool применим.
- `input_schema` — JSON-Schema (генерируется из Go-структуры
  через `invopop/jsonschema` reflector). Поля помечены
  тегами `jsonschema_description:"..."`.
- `function` — обычная Go-функция: принимает `json.RawMessage`,
  возвращает `(string, error)`. Возврат — *произвольная
  строка*, которую модель будет читать.

**На что обращаем внимание** для FA:

- Имя tool'а **не** определяет поведение. Модель смотрит на
  description.
- Output-format можно выбирать любой: Ball'у нравится JSON
  для `list_files` и просто `"OK"` для `edit_file`. Это
  **не** API-требование, это design choice. Цитата:

  > *«We return a list of strings and we denote directories
  > with a trailing slash. That's not required, it's just
  > something I just decided to do. There's no fixed format.
  > Anything goes as long as Claude can make sense of it
  > and whether it can you need to figure out by experimentation.»*

  Это прямо коррелирует с нашим решением в ADR-3 §Decision
  («filesystem canon → grep/BM25/vectors only when corpus
  justifies it»): output-shape инструментов FA тоже не
  должен пытаться угадать «правильный JSON», достаточно
  читаемого текста с предсказуемой структурой.

## 5. Три инструмента: что делают и зачем именно эти

### 5.1 `read_file`

```go
type ReadFileInput struct {
    Path string `json:"path" jsonschema_description:"The relative path of a file in the working directory."`
}
// Description:
//   "Read the contents of a given relative file path. Use this
//    when you want to see what's inside a file. Do not use this
//    with directory names."
```

Пять строк бизнес-логики (`os.ReadFile` + json-unmarshal).
Из этого автор делает важное наблюдение:

> *«We don't say anything about "if a user asks you about a
> file, read the file". We also don't say "if something looks
> like a filename, figure out how to read it". No, none of
> that. We say "help me solve the thing in this file" and
> Claude realizes that it can read the file to answer that
> and off it goes.»*

Это эмпирическое подтверждение, что **современная (Claude 3.7)
модель не нуждается в hand-coded routing «если в запросе слово
файл — вызови read_file»**. Достаточно описания инструмента —
модель сама решает, когда он нужен.

### 5.2 `list_files`

```go
// Description:
//   "List files and directories at a given path. If no path
//    is provided, lists files in the current directory."
type ListFilesInput struct {
    Path string `json:"path,omitempty" jsonschema_description:"Optional relative path to list files from. Defaults to current directory if not provided."`
}
```

Реализация — `filepath.Walk` с trailing-slash для директорий.
Возвращает JSON-массив строк.

Ball демонстрирует, что **модель сама комбинирует** `list_files`
+ `read_file`: на вопрос «What Go version are we using?» Claude
сначала вызывает `list_files`, видит `go.mod`, дальше делает
`read_file('go.mod')` и отвечает. Никакого orchestration-кода.
Никакого plan-and-execute. Просто описание двух инструментов
и LLM, который умеет ReAct-шаги.

### 5.3 `edit_file`

```go
// Description:
//   `Make edits to a text file.
//
//    Replaces 'old_str' with 'new_str' in the given file.
//    'old_str' and 'new_str' MUST be different from each other.
//
//    If the file specified with path doesn't exist, it will be created.`
type EditFileInput struct {
    Path   string `jsonschema_description:"The path to the file"`
    OldStr string `jsonschema_description:"Text to search for - must match exactly and must only have one match exactly"`
    NewStr string `jsonschema_description:"Text to replace old_str with"`
}
```

Это самый интересный из трёх. Ball выбирает **не** AST-edit,
не line-range-edit, не diff-edit, а **string replacement**.
Объяснение:

> *«Claude 3.7 loves replacing strings (experimentation is how
> you find out what they love or don't), so we're going to
> implement edit_file by telling Claude it can edit files by
> replacing existing text with new text.»*

Ключевое: **формат правки — это design lever, который зависит
от модели**. Перенос на FA:

- ADR-2 (LLM-tiering) предполагает, что Coder может быть
  Anthropic Sonnet, Debug — OSS-модель и т.д. **Один и тот
  же `edit_file` через string-replace может вести себя по-
  разному на разных моделях.** Это явный риск.
- В нашем codebase'е есть похожий тулинг — Devin'овский
  `edit` (str_replace с уникальностью old_string), и более
  гибкий `MultiEdit` (батч). Эти инструменты доказывают,
  что для целей UC1 string-replace — рабочий baseline,
  который при необходимости расширяется до батч-edit'ов.
- На Python имеет смысл реализовать `edit_file` именно по
  Ball'у (минимальный intent), и **сразу** иметь второй
  вариант — `apply_diff` (через unified diff) на случай,
  если OSS-модели плохо попадут в string-replace shape.
  **Эмпирически выбирать** на фикстурных задачах.

**Особенность по Ball'у:** `edit_file` создаёт файл, если его
нет и `OldStr == ""`. Это совмещает «create» и «edit» в один
tool. У Devin'овского `write` это разделено. Не очень важно
для FA на старте, но стоит зафиксировать в нашем дизайне
(одна tool-функция или две).

## 6. «Wink / raise arm» — почему tool-use вообще работает

Самая ценная педагогическая часть статьи — аналогия:

> *«Imagine you're talking to a friend and you tell them: "in
> the following conversation, wink if you want me to raise my
> arm". Weird thing to say, but an easy concept to grasp.»*

Дальше Ball демонстрирует, что **tool-use можно эмулировать
без специального API** — просто prompt'ом «когда хочешь
получить погоду, ответь `get_weather()`, я отвечу тебе
результатом»:

```text
You: You are a weather expert. When I ask you about the weather
     in a given location, I want you to reply with `get_weather()`.
     I will then tell you what the weather in that location is.
     Understood?
Claude: I understand your instructions. ...
You: Hey, what's the weather in Munich?
Claude: get_weather(Munich)
```

Это **базовый, до-API уровень** того, что современные provider'ы
оборачивают в native tool-calling (Anthropic `tool_use` blocks,
OpenAI `function_call` / `tools`). API ничего магического не
добавляет — он стандартизует синтаксис ответа модели и парсит
JSON-Schema на вход, чтобы валидировать.

**Что это значит для FA:**

- Если выбранный провайдер из ADR-2 (например, OSS-модель
  через OpenRouter) **не умеет native tool-calling** — fallback
  через prompt-only «wink-protocol» работает. Не идеально, но
  работает.
- ADR-2 пока не различает «модели с native tools» и «без них».
  Стоит зафиксировать в `~/.fa/models.yaml` поле типа
  `tool_protocol: native | prompt`, чтобы Coder-роль могла
  работать на обоих.

## 7. Маппинг на FA: что переиспользуем

Соотнесение статьи и текущих ADR / `project-overview.md`:

| Уровень FA | Что у Ball'а | Что в FA уже есть | Что достроить |
|---|---|---|---|
| Inner-loop (Coder/Debug-исполнитель) | ~50 строк, описан в §3 | ADR-2 указывает роли, но не цикл | Добавить ровно этот цикл в `src/fa/agent/loop.py` (Phase Coder PR) |
| Tool registry | Go-struct + jsonschema reflector | нет | Добавить `Tool`-Protocol с `name/description/input_schema/fn`, JSON-Schema через `pydantic` или `jsonschema-rs` |
| Tools `read_file` / `list_files` / `edit_file` | в статье | нет | Реализовать. Это базовый набор для UC1 |
| Conversation accumulator | локальный slice | implicit, не описан | Добавить `Session.conversation: list[Message]`, hot.md persistence не отменяет (см. project-overview §4) |
| Memory (Variant A / Mechanical Wiki) | отсутствует | ADR-3 | **дополнительно к Ball'у**. Inner-loop сам по себе stateless |
| Role-routing | один Claude на всё | ADR-2 | **дополнительно к Ball'у**. Каждая роль — свой client + tools |
| Eval | нет | gstack-style baseline в project-overview §3 | **дополнительно к Ball'у** |

Главный вывод: **архитектурный слой Ball'а — это нижний этаж
FA**. Над ним надстраивается всё, что описано в ADR-1..ADR-5.
Ничего из ADR не противоречит этому слою; всё расширяет.

## 8. Что в статье намеренно опущено

Это важно: автор честно говорит, что строит **минимально-
функциональный** агент, а не production. Опущены:

1. **Безопасность тулов.** `edit_file` без подтверждения
   перезаписывает любой файл. У нас в FA должна быть
   политика «edit можно, но только в `notes/` / в репо
   пользователя; запрет на `~/.ssh`, `/etc`, чужие репо».
   Это не часть статьи, но обязательная часть FA.
2. **Параллельные tool calls.** Anthropic API позволяет
   модели запросить несколько `tool_use` в одном response.
   Ball'у достаточно последовательного исполнения. У нас
   с UC1 (вычитка нескольких файлов перед правкой) часто
   нужен parallel — добавим в loop.
3. **Streaming.** Ball ждёт полный ответ. Для UX (CLI/Telegram)
   FA streaming желателен.
4. **Контроль контекста.** `conversation` у Ball'а просто
   растёт. У нас есть лимиты (особенно на дешёвых моделях).
   Нужно truncate-стратегию (срезать ранние tool_result'ы,
   оставить summary'и) — задача памяти, не inner-loop.
5. **Validation схемы.** В Go `invopop/jsonschema` отдаёт
   схему модели, но при получении `block.input` Ball
   делает только `json.Unmarshal` — нет проверки, что
   модель не наврала с типами. Это редко падает у Sonnet,
   но на OSS-моделях бывает. Добавим `pydantic.validate_call`.
6. **Логирование / audit.** Tool-use без логирования. У нас
   есть требование «session log → `notes/sessions/<date>.md`»
   из project-overview §4 — нужен hook.
7. **Cancellation.** Ctrl-C ломает поток conversation; на
   серьёзном сервисе нужен `context.Context` + clean shutdown.

Все эти пункты — не критика статьи, а **список инженерных
задач между «нашли inner-loop» и «закрыли UC1»**.

## 9. Конкретные действия для FA (предлагаемые, не утверждённые)

Это не ADR. Это input для будущих ADR / PR:

1. **Implementation PR — `src/fa/agent/loop.py`** с inner-loop
   из §3 этой ноты. Покрытие ≥ 80% по unit-тестам с
   mock-Anthropic. Размер — целиться в ~150 строк Python.
2. **Implementation PR — `src/fa/tools/`** с тремя tool'ами
   `read_file` / `list_files` / `edit_file` по §5. Каждый
   tool — pydantic-модель ввода + функция + декларативный
   `Tool`-объект. Регистрация через явный список в
   `src/fa/agent/registry.py`.
3. **ADR-N** на тему «edit-file shape: string-replace vs
   diff-apply vs both» с фикстурными прогонами на каждой
   модели из ADR-2. Hard-gate перед стартом этого ADR — у
   нас должна быть рабочая `~/.fa/models.yaml` хотя бы с
   двумя моделями.
4. **`models.yaml` schema field `tool_protocol`** —
   `native` для Anthropic / GPT, `prompt` для тех OSS-моделей,
   что не умеют native tools. Этот ADR в фоне расширяет
   ADR-2.
5. **Sandbox-политика для tool'ов**. Статья ничего не говорит
   про sandboxing. Добавим в ADR (отдельный) — что-то типа
   «edit_file проверяет, что path лежит в `allowed_roots`
   из конфига, иначе возвращает error».

Эти пять пунктов — между Phase S (текущее scaffolding) и
chunker-implementation (next-step из HANDOFF.md). По сути
chunker — read-side, а агент-loop — write-side; они
ортогональны и могут идти параллельно или последовательно
в зависимости от ёмкости проекта.

## 10. Цитаты, которые имеет смысл повторить дословно

Эти вырезки — для будущих ноут / промптов. Цитируются для
сохранения интонации и упомянутых deltas, не для статистики.

> *«It's an LLM, a loop, and enough tokens.»*

> *«An LLM with access to tools, giving it the ability to
> modify something outside the context window.»*

> *«These models are trained and fine-tuned to use "tools"
> and they're very eager to do so.»*

> *«There's no fixed format. Anything goes as long as Claude
> can make sense of it and whether it can you need to figure
> out by experimentation.»*

> *«300 lines of code and three tools and now you're to be
> able to talk to an alien intelligence that edits your code.»*

## 11. Связь со следующей нотой

Ball описывает **inner-loop** и **edit-side**. Вторая
исследовательская нота этой PR
([`sliders-structured-reasoning-2026-04.md`](./sliders-structured-reasoning-2026-04.md))
разбирает SLIDERS (Stanford OV AL) — как **read-side**
масштабируется на 3.9M+ токенов через структурированное
извлечение в реляционную БД с reconciliation. Эти две статьи
вместе закрывают вилку «как FA думает» (read-side, SLIDERS) и
«как FA действует» (write-side, Ball). Чтение в порядке,
указанном в `knowledge/llms.txt`.

## 12. Ограничения этой ноты

- **Один источник.** Статья Ball'а — single-author blog-post,
  не peer-reviewed paper. Цифры («400 строк») приведены как
  факт автора, а не как воспроизведённый результат.
- **Привязка к Anthropic SDK.** Если FA пойдёт через
  OpenRouter / OpenAI / OSS-модели, конкретные имена типов
  (`anthropic.MessageParam`, `tool_use` block) изменятся —
  но **архитектурный паттерн остаётся**.
- **Не покрывает: память, eval, многоагентность, безопасность.**
  Эти области закрываются другими нотами и ADR.
- **Дата проверки модели** (Claude 3.7 Sonnet, апрель 2025).
  К моменту start of implementation (2026-Q2) frontier-модель
  будет другая; «string-replace» как любимый формат может
  смещаться. Перепроверить.
