---
name: stage-bar
description: Shows a stage bar — where a development task is in its pipeline (taken → analysis → design approved → spec & plan → code N/M → review & tests → PR → merge → deploy → measure) and what is waiting on the user. Use it in every reply that reports progress on a multi-step development task, when a task moves to a new stage, when work is blocked on the user (approve the design, merge the PR, run the deploy), and in the final summary of a task — even if the user doesn't ask for a status. Also use it when the user asks "where are we", "what stage", "what's next", "статус", "на каком этапе".
---

# Stage bar

A compact progress card for a development task: which stages are done, which one is
current, which are left, and — most importantly — whether the next move is the user's.

People lose track of long agentic tasks. A wall of text answers "what happened" but not
"where are we and what do you need from me". The bar answers that in one glance, so put
it at the top of the reply, then write the details below it as usual.

## When to show it

- A reply reports progress on a task that has (or will have) several stages.
- The task moves to a new stage (design approved, plan written, PR opened, deployed…).
- You are waiting on the user — the bar makes the ask impossible to miss.
- The final summary of the task.

Skip it for one-off questions, quick fixes that finish in one reply, and replies that
are only a clarifying question in the middle of a stage (no progress to show).

## Stages

Default pipeline — rename, drop or add stages to fit the actual task, but keep the order
honest and keep the list short (6–12 items):

1. Task taken
2. Analysis / data
3. Design approved
4. Spec and plan
5. Code (show `N/M` sub-tasks when there is a plan)
6. Review and tests
7. Docs / wiki updated
8. PR opened
9. CI and merge
10. Deploy and check in prod
11. Measure the result

Each stage has one state:

| State | Meaning | Look |
|---|---|---|
| done | finished and verified | green, check mark |
| now | in progress, owned by you | amber, clock |
| waiting on user | the next move is the user's (approve, merge, deploy, answer) | amber, clock, label says so ("ждёт вас" / "your move") |
| todo | not started | muted |

Exactly one stage is `now` or `waiting on user`. Mark a stage done only when it really is
(tests passed, review approved, PR actually opened) — the bar is a promise, an optimistic
bar is worse than none.

If the user's request is a bigger initiative split into sub-projects, add one row of pills
under the stages: the current sub-project highlighted, the rest muted.

Write the labels in the user's language.

## How to render

### If a widget tool is available (e.g. `show_widget` from the visualize MCP)

1. If the tool asks for it, load its guidance first (e.g. `read_me`) — silently, don't
   mention it.
2. Render `assets/stage-bar.html` with the placeholders filled in (title, counter,
   progress percent, one `<div class="s …">` per stage, sub-project pills). Use only the
   host's CSS variables so it works in light and dark mode.
3. Give the widget a specific snake_case `title` like `checkout_refactor_status`.
4. Don't repeat the bar's content in text right after it — say what changed and what you
   need, then the details.

Do not add buttons that call `sendPrompt`: in Claude Code (CLI and the desktop Code tab)
they do nothing, and a dead button is worse than no button. Write the next-step options
as plain text under the widget ("Reply «PR» to open the pull request…").

### If there is no widget tool — make it a picture or a coloured text card

Sessions differ: the desktop Code tab has the visualize widget, a CLI or a remote/SDK
session usually doesn't. Check with ToolSearch (`visualize show_widget`) before falling
back — don't assume.

**Picture.** If `SendUserFile` works in this session: fill
`assets/stage-bar-standalone.html` (same look, own dark/light variables), screenshot the
`.card` with Playwright (`colorScheme: 'dark'`, `deviceScaleFactor: 2`) and send the PNG
with `display: 'render'`. If delivery fails (e.g. "not on a project thread"), don't retry —
use the coloured text card below.

**Coloured text card** (markdown, renders in any chat). Same information as the widget:

```
**Заказ с недостачей** · 8 из 11 · PR #513
`▰▰▰▰▰▰▰▰▱▱▱` 73 %

🟩 1 Задача взята · 🟩 2 Анализ · 🟩 3 Дизайн · 🟩 4 Спека и план · 🟩 5 Код 11/11
🟩 6 Ревью и тесты · 🟩 7 Вики · 🟩 8 PR открыт · 🟧 **9 CI и мёрж — ждёт вас**
⬜ 10 Выкат и проверка · ⬜ 11 Замер

Вся задача: 🟧 **1. Недостача — PR** · ⬜ 2. Ссылка на корзину · ⬜ 3. Помощь провизора
```

🟩 done, 🟧 current (bold, say "ждёт вас" / "your move" when it is the user's), ⬜ todo.
Progress bar: one `▰` per done stage, `▱` for the rest. Never use a fenced code block for
the card itself — that turns it monochrome.

## Example

Task: "accept orders when stock is short", PR opened, CI running, merge is the user's.

- title: «Заказ с недостачей», counter «8 из 11 · PR #513», progress 73 %
- stages 1–8 done; 9 «CI и мёрж» — waiting on user; 10–11 todo
- pills: «1. Недостача — PR» (on), «2. Ссылка на корзину», «3. Помощь провизора»

Below the bar: one line on what changed, then what you need from the user, then details.
