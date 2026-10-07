# stage-bar — плашка этапов для Claude Code

[English below](#english)

Длинные задачи с агентом теряются в тексте: что уже сделано, что сейчас и — главное —
**ждёт ли что-то от тебя**. `stage-bar` — навык (skill) для Claude Code: в каждом ответе
по задаче Claude показывает карточку этапов:

```
Заказ с недостачей · 8/11 · PR #513
✓ Задача взята → ✓ Анализ → ✓ Дизайн → ✓ Спека+план → ✓ Код 11/11 → ✓ Ревью → ✓ Вики → ✓ PR
→ ● CI и мёрж (ждёт вас) → ○ Выкат → ○ Замер
```

- В десктоп-приложении (вкладка Code, есть виджеты) — цветная карточка прямо в чате:
  зелёные этапы готовы, жёлтый — текущий, серые — впереди.
- В терминале — такая же полоска текстом.
- Этап, где нужен твой шаг (одобрить дизайн, слить PR, запустить выкат), подписан
  «ждёт вас».
- Если задача разбита на подпроекты — под этапами строка подпроектов.

## Установка

### Вариант 1 — плагином (рекомендуется)

В Claude Code:

```
/plugin marketplace add zhaqekzkz/claude-stage-bar
/plugin install stage-bar@stage-bar
```

Навык подключится во всех проектах. Обновление — `/plugin marketplace update stage-bar`.

### Вариант 2 — вручную, одной папкой

Скопируй `skills/stage-bar` в папку навыков пользователя:

- macOS / Linux / WSL: `~/.claude/skills/stage-bar/`
- Windows: `C:\Users\<имя>\.claude\skills\stage-bar\`

```bash
git clone https://github.com/zhaqekzkz/claude-stage-bar.git
cp -r claude-stage-bar/skills/stage-bar ~/.claude/skills/
```

### Вариант 3 — только в одном проекте

Положи папку в `<проект>/.claude/skills/stage-bar/` и закоммить — плашка появится у всех,
кто работает с этим репозиторием через Claude Code.

## Чтобы плашка была всегда

Навык включается сам, когда Claude видит, что задача многоэтапная. Если хочешь, чтобы
он не забывал, добавь строку в `~/.claude/CLAUDE.md` (глобально) или в `CLAUDE.md`
проекта:

```markdown
- В ответах по задачам разработки показывай плашку этапов (навык stage-bar).
```

## Настройка этапов

Список этапов — в `skills/stage-bar/SKILL.md`, раздел «Stages». Переименуй, убери или
добавь свои (например, «Согласование с юристом» или «Публикация в стор»). Claude и так
подстраивает список под задачу, но базовый порядок берёт оттуда.

## Ограничения

- Кнопки в виджете не делаются: в Claude Code они не работают. Варианты следующего шага
  Claude пишет текстом под плашкой.
- Карточка-виджет нужен инструмент `show_widget` (есть в десктоп-приложении). Без него
  Claude идёт по запасным путям, не скатываясь в серый моноширинный текст:
  1. **Картинка.** Если сессия умеет отправлять файлы (`SendUserFile`), Claude заполняет
     `assets/stage-bar-standalone.html`, снимает скриншот и присылает PNG.
  2. **Цветная текстовая карточка** в markdown — работает в любом чате:

     **Заказ с недостачей** · 8 из 11 · PR #513
     `▰▰▰▰▰▰▰▰▱▱▱` 73 %
     🟩 1 Задача взята · 🟩 2 Анализ · … · 🟧 **9 CI и мёрж — ждёт вас** · ⬜ 10 Выкат · ⬜ 11 Замер

---

## English

`stage-bar` is a Claude Code skill that puts a stage card at the top of every progress
reply on a development task: done / current / remaining stages and whether the next move
is yours (approve the design, merge the PR, run the deploy).

- With a widget tool (`show_widget`, Claude desktop Code tab) — a colored inline card.
- Without it — a PNG of the same card (if the session can send files, rendered from
  `assets/stage-bar-standalone.html`), otherwise a coloured markdown card:
  `🟩 done · 🟧 current (your move) · ⬜ todo` with a `▰▰▱▱` progress bar.

### Install

```
/plugin marketplace add zhaqekzkz/claude-stage-bar
/plugin install stage-bar@stage-bar
```

Or copy `skills/stage-bar` to `~/.claude/skills/` (all projects) or
`<project>/.claude/skills/` (one project, shared via git).

To make it sticky, add to `~/.claude/CLAUDE.md`:
`- Show the stage bar (stage-bar skill) in progress replies on development tasks.`

Customize the default stages in `skills/stage-bar/SKILL.md`. Buttons are intentionally
not rendered — they don't work inside Claude Code.

License: MIT
