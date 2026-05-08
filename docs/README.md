# Документация First-Agent

Небольшая, опрятная вики по работе с devin.ai над проектом First-Agent.
«Less is more» — шесть файлов, каждый со своей ролью.

| Файл | Когда читать |
|---|---|
| [architecture.md](./architecture.md) | Проектируем агента. Трёхслойная модель, паттерны, соображения по LLM/памяти/восстановлению. |
| [workflow.md](./workflow.md) | Наш текущий день-за-днём процесс: **Research → Scaffolding → Module**, с критериями выхода из каждой фазы. |
| [prompting.md](./prompting.md) | Пишем промпт для Devin. Основы, шаблоны T1–T5, anti-patterns. |
| [glossary.md](./glossary.md) | Незнакомый термин. |

Archived 2026-05-08 (in-place stubs, excluded from `knowledge/llms.txt`
routing surface):

- [`devin-reference.md`](./devin-reference.md) — Devin-specific reference;
  restored as a per-host gated entry once `llms.txt` learns to gate by
  agent host. See file header for the archive banner.
- [`agent-creation-github.md`](./agent-creation-github.md) — tutorial
  transcript of `czl9707/build-your-own-openclaw`; live ADR-7 inputs are
  now `knowledge/research/efficient-llm-agent-harness-2026-05.md` and
  `knowledge/research/how-to-build-an-agent-ampcode-2026-04.md`.

Долговременная память проекта (ADR, промпты, обзор) — в [`../knowledge/`](../knowledge/README.md).

## Принципы этой вики

1. Менее — лучше. Если два файла пересекаются — сливаем.
2. Если расходимся с официальными доками Devin — правим у себя.
3. Если нашли что-то, что Devin должен помнить между сессиями — кладём в
   [`../knowledge/`](../knowledge/) **и** создаём Knowledge note в Devin UI.
   (Детали про Knowledge notes / Skills / Playbooks — в архивном
   `devin-reference.md`, баннер в шапке файла.)
