# Plugin Packaging and Publication Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Привести репозиторий к состоянию, в котором prompt-writer устанавливается как плагин Claude Code (`/plugin install`), не тащит в установку пользователя внутреннюю кухню разработки и не ссылается на несуществующие инструменты, и готов к подаче в community-каталог Anthropic.

**Architecture:** Репозиторий становится маркетплейсом с одним плагином внутри. `.claude-plugin/marketplace.json` остается в корне и указывает относительным путем на `plugins/prompt-writer/` — это и есть директория плагина, только она копируется в кеш пользователя при установке. Все, что относится к разработке скилла (`CLAUDE.md`, `docs/`, `.superpowers/`, `tmp/`), остается в корне и не едет. Параллельно из текста скилла убираются вызовы инструментов, которых нет в Claude Code.

**Tech Stack:** Markdown, JSON-манифесты, git, `claude plugin` CLI (Claude Code 2.1.224+).

**Источники истины по формату** (сверять при любом сомнении, не по памяти):
- Манифест плагина: https://code.claude.com/docs/en/plugins-reference#plugin-manifest-schema
- Манифест маркетплейса и источники плагинов: https://code.claude.com/docs/en/plugin-marketplaces
- Подача в community-каталог: https://code.claude.com/docs/en/plugins#submit-your-plugin-to-the-community-marketplace

---

## Состояние на момент написания плана

В подготовительной сессии уже созданы и **не закоммичены** два файла:

- `.claude-plugin/plugin.json` — манифест плагина, версия `0.1.0`, поле `keywords` содержит `"russian"`
- `.claude-plugin/marketplace.json` — манифест маркетплейса, имя `londeren-plugins`, `"source": "./"`

Оба прошли `claude plugin validate`, загрузка проверена смоук-тестом (`prompt-writer:prompt-writer`). План опирается на их существование: Task 2 их перемещает и правит, Task 4 доводит до релизного состояния. Если к моменту выполнения их нет — создать по содержимому, приведенному в Task 2 Step 4 и Task 4 Step 1, и только потом продолжать.

---

## Global Constraints

- **Зависимость от плана перевода.** Этот план выполняется ПОСЛЕ того, как `docs/superpowers/plans/2026-08-09-english-translation.md` доведен до Task 9 включительно и влит. Причина: перевод переписывает целиком `SKILL.md`, `README.md`, `CLAUDE.md` и все ассеты — то есть ровно те файлы, которые правит и перемещает этот план. Запуск раньше гарантирует конфликты.
- **Проверка зависимости первым шагом Task 1.** Если перевод еще не влит, работа не начинается.
- **Язык нового текста.** Ожидаемое состояние на момент выполнения — репозиторий на английском. Весь новый и измененный текст пишется по-английски. Для каждой правки ниже дан EN-вариант (основной) и RU-вариант (фолбэк — использовать только если файл на момент выполнения все еще русский).
- **EN-стиль наследуется от плана перевода:** в прозе нет em dashes, нет Title Case в заголовках, нет softening («try to», «ideally», «where possible»), straight quotes.
- **Никаких правок содержания методологии.** Правила, их нумерация, регистры модальности, примеры не меняются. Единственное содержательное изменение — формулировки шагов выдачи результата и вопроса о типе (Task 1), и они меняют только описание механики, а не методологию.
- **`git mv`, а не delete+create.** Историю файлов надо сохранить.
- **Коммит после каждой задачи**, префикс `chore(plugin):` для упаковки, `fix(skill):` для правок текста скилла, `docs:` для витрин.
- **Автотестов в репозитории нет.** Роль тестов выполняют команды верификации, приведенные в каждой задаче. Шаг не считается выполненным, пока команда не отработала с ожидаемым выводом.
- **Один запрет на глобальную замену.** Строка `ask_user_input_v0` встречается в `templates/agentic-task.md` как содержательный ПРИМЕР хорошо написанного description инструмента. Она остается. Любой `sed -i` по всему дереву запрещен.

---

### Task 1: Платформо-нейтральные формулировки инструментов в SKILL.md

**Files:**
- Modify: `SKILL.md` (пять мест; точные строки на момент написания плана — 39, 60, 478, 489, 508 и 493, но после перевода они сдвинутся, поэтому ищем грепом)
- Не трогать: `templates/agentic-task.md`

**Interfaces:**
- Produces: `SKILL.md`, в котором нет вызовов `present_files`, `ask_user_input_v0`, `view tool`. Task 3 ссылается на этот факт при переписывании раздела блокеров в CLAUDE.md.

- [ ] **Step 1: Проверить, что перевод влит**

```bash
git log --oneline -15
grep -c 'Master rules' SKILL.md
head -8 README.md
```

Ожидается: в истории есть коммиты Task 9 плана перевода, `SKILL.md` и `README.md` на английском. Если нет — остановиться и сообщить, план выполняется позже.

- [ ] **Step 2: Зафиксировать базовые метрики**

```bash
grep -n 'present_files' SKILL.md            # ожидается 3 вхождения
grep -n 'ask_user_input_v0' SKILL.md        # ожидается 1 вхождение
grep -nE '\bview\b' SKILL.md                # ожидается 2 вхождения (Шаг 3 и раздел файлов скилла)
grep -c 'ask_user_input_v0' templates/agentic-task.md   # ожидается 1 - это то, что НЕЛЬЗЯ трогать
```

Записать фактические числа. Если они отличаются от ожидаемых, перевод мог переформулировать эти места. Тогда прочитать каждое вхождение целиком и адаптировать замены ниже по смыслу, сохранив намерение.

- [ ] **Step 3: Заменить вхождение в шаге «Применить шаблон»**

Найти строку про загрузку шаблона в разделе Шага 3.

Было (RU-оригинал): `Подгрузить соответствующий template из \`templates/\` через view. Заполнить содержанием задачи.`

Стало (EN):

```
Read the matching template from `templates/`. Fill it with the task content.
```

Стало (RU-фолбэк):

```
Прочитать соответствующий template из `templates/`. Заполнить содержанием задачи.
```

Смысл правки: имя инструмента убирается, действие остается. Модель сама выберет `Read` или его аналог в своей среде.

- [ ] **Step 4: Заменить вхождение в шаге «Выдать результат»**

Это главное место: финальная выдача всего процесса.

Было (RU-оригинал): `Готовый промпт сохраняется как отдельный .md файл и презентуется через present_files.`

Стало (EN):

```
The finished prompt is saved as a separate .md file, and the reply gives the path to it. When writing files is not available in the environment, the prompt goes into the chat as one block.
```

Стало (RU-фолбэк):

```
Готовый промпт сохраняется отдельным .md файлом, в ответе дается путь к нему. Если запись файлов в среде недоступна, промпт выдается в чат одним блоком.
```

Остаток абзаца (про короткое описание решений и сводку аудита) не меняется.

- [ ] **Step 5: Заменить пункт 7 в сценарии «новый промпт с нуля»**

Было (RU-оригинал): `7. Презентовать через present_files, в чате - решения и сводка аудита`

Стало (EN):

```
7. Deliver as described in Step 5: the prompt as a file, the chat gets the key decisions and the audit summary
```

Стало (RU-фолбэк):

```
7. Выдать результат как описано в Шаге 5: промпт файлом, в чате - ключевые решения и сводка аудита
```

- [ ] **Step 6: Заменить пункт 8 в сценарии «улучшить существующий промпт»**

Было (RU-оригинал): `8. Презентовать через present_files`

Стало (EN):

```
8. Deliver as described in Step 5
```

Стало (RU-фолбэк):

```
8. Выдать результат как описано в Шаге 5
```

- [ ] **Step 7: Заменить вхождение в сценарии «пользователь не уверен какой тип нужен»**

Здесь имена инструментов не убираются, а называются оба — именно этого требует правило 7 самого скилла (правило рядом с моментом применения) и это единственный способ заставить модель использовать структурированный пикер там, где он есть.

Было (RU-оригинал): `Не догадываться - спросить через ask_user_input_v0 с 2-4 опциями типов. Описать каждый тип одной строкой.`

Стало (EN):

```
Do not guess. Ask a question with 2-4 type options, one line per type. Use the structured-choice tool when the environment has one (AskUserQuestion in Claude Code, ask_user_input_v0 in claude.ai); otherwise ask in plain text with the options as a numbered list.
```

Стало (RU-фолбэк):

```
Не догадываться - задать вопрос с 2-4 вариантами типа, каждый тип одной строкой. Если в среде есть инструмент структурированного выбора (AskUserQuestion в Claude Code, ask_user_input_v0 в claude.ai), использовать его; иначе спросить текстом, варианты нумерованным списком.
```

- [ ] **Step 8: Заменить вхождение в конце раздела «Файлы скилла»**

Было (RU-оригинал): `Подгружать файлы через view tool по необходимости, не все сразу.`

Стало (EN):

```
Load these files on demand, not all at once.
```

Стало (RU-фолбэк):

```
Подгружать файлы по необходимости, не все сразу.
```

- [ ] **Step 9: Проверить результат грепом**

```bash
grep -c 'present_files' SKILL.md                        # ожидается 0
grep -c 'ask_user_input_v0' SKILL.md                    # ожидается 1 (только в паре с AskUserQuestion)
grep -nE '\bview tool\b' SKILL.md                       # ожидается пусто
grep -c 'ask_user_input_v0' templates/agentic-task.md   # ожидается 1 - НЕ изменилось
grep -c 'AskUserQuestion' SKILL.md                      # ожидается 1
```

Все пять ожиданий должны совпасть. Если `templates/agentic-task.md` показал не 1 — правка утекла туда, откатить.

- [ ] **Step 10: Функциональный смоук-тест выдачи**

```bash
cd "$(mktemp -d)" && claude --plugin-dir ~/work/GrowGlobal/prompt-writer-skill \
  -p "Write a prompt for a support bot for a SaaS analytics product. Audience: existing customers. Tone: concise." \
  --max-turns 30 2>&1 | tail -30
ls -la
```

Ожидается: скилл активировался, в текущей директории появился .md файл с промптом, в ответе есть путь к нему и сводка аудита. Модель не пыталась звать несуществующий инструмент.

- [ ] **Step 11: Коммит**

```bash
git add SKILL.md
git commit -m "fix(skill): platform-neutral delivery and question steps

present_files, ask_user_input_v0 and the view tool exist only in the
claude.ai environment. The delivery step now describes intent with a
fallback, and the type question names the structured-choice tool of both
environments. The ask_user_input_v0 mention in templates/agentic-task.md
is an example of tool-description writing and stays unchanged."
```

---

### Task 2: Перенос плагина в plugins/prompt-writer/

**Files:**
- Move: `SKILL.md`, `reference/`, `templates/`, `checklists/` → `plugins/prompt-writer/`
- Move: `.claude-plugin/plugin.json` → `plugins/prompt-writer/.claude-plugin/plugin.json`
- Modify: `.claude-plugin/marketplace.json` (поле `source`)
- Create: `plugins/prompt-writer/LICENSE` (копия корневой), `plugins/prompt-writer/README.md`
- Не трогать: `README.md`, `CLAUDE.md`, `LICENSE`, `docs/`, `.superpowers/`, `tmp/` в корне

**Interfaces:**
- Consumes: `SKILL.md` из Task 1.
- Produces: раскладку, на которую опирается Task 3 (пути в витринах) и Task 4 (установка из маркетплейса). Имя маркетплейса `londeren-plugins`, имя плагина `prompt-writer`, установочная строка `prompt-writer@londeren-plugins`.

- [ ] **Step 1: Убедиться, что в дереве нет ничего, кроме ожидаемого**

```bash
git status --porcelain
```

Ожидается ровно две строки — неотслеживаемые `.claude-plugin/plugin.json` и `.claude-plugin/marketplace.json` из подготовительной сессии (см. раздел «Состояние на момент написания плана»), либо пусто, если их успели закоммитить раньше. Любые другие незакоммиченные изменения — источник потерь при `git mv`, разобраться с ними до начала.

- [ ] **Step 2: Создать директорию плагина и перенести ассеты**

```bash
mkdir -p plugins/prompt-writer
git mv SKILL.md reference templates checklists plugins/prompt-writer/
mkdir -p plugins/prompt-writer/.claude-plugin
git mv .claude-plugin/plugin.json plugins/prompt-writer/.claude-plugin/plugin.json
```

- [ ] **Step 3: Проверить, что внутренние относительные ссылки не сломались**

Ассеты переехали одним блоком, поэтому пути вида `reference/full-rules.md` внутри скилла остаются валидными. Проверить это, а не поверить:

```bash
cd plugins/prompt-writer
grep -ohE '(reference|templates|checklists)/[a-z0-9-]+\.md' SKILL.md reference/*.md templates/*.md checklists/*.md \
  | sort -u \
  | while read -r p; do [ -f "$p" ] || echo "MISSING: $p"; done
cd -
```

Ожидается: ни одной строки `MISSING`.

- [ ] **Step 4: Обновить source в манифесте маркетплейса**

В `.claude-plugin/marketplace.json` заменить `"source": "./"` на путь к подкаталогу. Файл целиком после правки:

```json
{
  "name": "londeren-plugins",
  "owner": {
    "name": "Sergey Lebedev",
    "url": "https://github.com/Londeren"
  },
  "description": "Plugins by Londeren. Currently ships prompt-writer, a methodology skill for writing LLM prompts.",
  "plugins": [
    {
      "name": "prompt-writer",
      "source": "./plugins/prompt-writer",
      "category": "productivity",
      "tags": [
        "prompt-engineering",
        "system-prompt",
        "writing"
      ]
    }
  ]
}
```

- [ ] **Step 5: Положить лицензию рядом с плагином**

Плагин уезжает к пользователю отдельно от корня репозитория, поэтому своя копия лицензии ему нужна.

```bash
cp LICENSE plugins/prompt-writer/LICENSE
```

- [ ] **Step 6: Создать README плагина**

Короткий, не копия корневого. Файл `plugins/prompt-writer/README.md` целиком (EN):

````markdown
# prompt-writer

Claude Code plugin. A skill that turns "write me a prompt" into an engineered prompt: routing by task type, modality registers, XML-tagged examples, and a draft to audit to final loop.

## Install

```
/plugin marketplace add Londeren/prompt-writer-skill
/plugin install prompt-writer@londeren-plugins
```

## Use

The skill triggers on its own when you ask for a prompt, a system prompt, an agent instruction, or an improvement of an existing prompt. A detailed request goes straight to a draft. A vague one gets 2-3 clarifying questions first.

## Layout

`SKILL.md` is the entry point and the only file loaded on activation. `templates/`, `reference/` and `checklists/` are loaded on demand.

Full documentation, methodology background and the development notes: https://github.com/Londeren/prompt-writer-skill
````

- [ ] **Step 7: Валидировать оба манифеста**

```bash
claude plugin validate .                                  # маркетплейс
claude plugin validate ./plugins/prompt-writer --strict   # плагин
```

Ожидается: оба `✔ Validation passed`. Ключевая разница с текущим состоянием — у плагина больше не должно быть warning `CLAUDE.md at the plugin root is not loaded as project context`, потому что `CLAUDE.md` остался в корне репозитория и в директорию плагина не входит. Если warning остался, значит что-то лишнее переехало внутрь.

- [ ] **Step 8: Проверить, что в плагин не уехал мусор**

```bash
find plugins/prompt-writer -type f | sort
```

Ожидается ровно: `.claude-plugin/plugin.json`, `LICENSE`, `README.md`, `SKILL.md`, два файла в `checklists/`, два в `reference/`, пять в `templates/`. Ничего из `docs/`, `.superpowers/`, `tmp/`, никакого `CLAUDE.md`, никакого `.DS_Store`.

- [ ] **Step 9: Смоук-тест загрузки**

```bash
claude --plugin-dir ./plugins/prompt-writer \
  -p "List the exact names of all skills available to you that contain 'prompt' in the name. Output names only." \
  --max-turns 1
```

Ожидается: в выводе есть `prompt-writer:prompt-writer`. Это подтверждает, что `SKILL.md` в корне плагина подхватывается как single-skill плагин и что `name` из frontmatter работает как имя вызова.

- [ ] **Step 10: Коммит**

```bash
git add -A
git commit -m "chore(plugin): move the skill into plugins/prompt-writer

The repository is now a marketplace with one plugin inside it. Only
plugins/prompt-writer is copied into the user's plugin cache on install,
so CLAUDE.md, docs/ and .superpowers/ no longer ship to users."
```

---

### Task 3: Синхронизация витрин - README.md и CLAUDE.md

**Files:**
- Modify: `README.md` (раздел установки, дерево файлов, ссылки на reference)
- Modify: `CLAUDE.md` (описание репозитория, пути архитектуры, раздел блокеров, раздел проверки)

**Interfaces:**
- Consumes: раскладку из Task 2 и факт устранения блокера из Task 1.

- [ ] **Step 1: README - переписать раздел установки**

Текущий раздел говорит `git clone ... ~/.claude/skills/prompt-writer` и содержит строку «Установка из плагин-маркетплейса — в планах». После Task 2 первое ломается (склонируется корень репозитория, а не скилл), второе становится неправдой.

Новый раздел установки целиком (EN):

`````markdown
## Install

### Claude Code, from the marketplace

```
/plugin marketplace add Londeren/prompt-writer-skill
/plugin install prompt-writer@londeren-plugins
```

If the install summary says `Run /reload-plugins to activate.`, run that command. The skill becomes available as `prompt-writer:prompt-writer` and triggers on its own.

### Claude Code, manual

Copy the plugin directory into your personal skills directory. It carries its own `plugin.json`, so Claude Code loads it as a skills-directory plugin with no marketplace involved:

```bash
git clone https://github.com/Londeren/prompt-writer-skill.git /tmp/prompt-writer-skill
cp -r /tmp/prompt-writer-skill/plugins/prompt-writer ~/.claude/skills/prompt-writer
```

### claude.ai

Works on every plan including Free. Turn on "Code execution and file creation" in Settings, Capabilities first.

1. Build a zip whose root contains a `prompt-writer/` folder with `SKILL.md` inside. Files placed directly at the archive root fail validation:

   ```bash
   mkdir prompt-writer
   cp -r plugins/prompt-writer/SKILL.md plugins/prompt-writer/templates \
         plugins/prompt-writer/reference plugins/prompt-writer/checklists prompt-writer/
   zip -r prompt-writer.zip prompt-writer
   ```

2. Upload it at [Customize, Skills](https://claude.ai/customize/skills): "Create skill", then "Upload a skill".
`````

RU-фолбэк (нужен, только если перевод по какой-то причине не влит): перевести блок выше дословно, сохранив структуру трех подразделов; все команды, имена (`londeren-plugins`, `prompt-writer@londeren-plugins`) и ссылки оставить символ в символ.

- [ ] **Step 2: README - обновить quick start в шапке**

В блокквоте шапки строка про Claude Code сейчас говорит `git clone this repo into ~/.claude/skills/prompt-writer`. Заменить на:

```
**Quick start:** for Claude Code — `/plugin marketplace add Londeren/prompt-writer-skill` then `/plugin install prompt-writer@londeren-plugins`. For claude.ai — see Install below.
```

Остальную часть блокквота (про язык скилла и автотриггер) сохранить как есть после перевода.

- [ ] **Step 3: README - обновить дерево файлов**

Раздел «Структура скилла» / «Layout». Новое дерево целиком:

```
.claude-plugin/
  marketplace.json              — marketplace manifest, points at plugins/prompt-writer
plugins/prompt-writer/          — the plugin, this is what ships to users
  .claude-plugin/plugin.json    — plugin manifest
  SKILL.md                      — entry point: routing, master rules, process
  templates/
    character-frame.md          — type A
    identification-frame.md     — type B
    one-shot-task.md            — type C
    extraction-prompt.md        — type D
    agentic-task.md             — type E
  reference/
    full-rules.md               — the full rule set with reasoning
    modal-registers.md          — the six modality registers in detail
  checklists/
    self-check.md               — the audit checklist
    input-triage.md             — taxonomy of defects in an incoming prompt
docs/, CLAUDE.md                — development notes, not part of the plugin
```

Строку под деревом про progressive disclosure сохранить.

- [ ] **Step 4: README - починить ссылки на reference**

В разделе про принципы методологии есть markdown-ссылки `[reference/full-rules.md](reference/full-rules.md)` и `[reference/modal-registers.md](reference/modal-registers.md)`. После переезда они ведут в никуда.

```bash
grep -n '](reference/\|](templates/\|](checklists/\|](SKILL.md' README.md
```

Каждую найденную ссылку переписать с префиксом `plugins/prompt-writer/`, например `[reference/full-rules.md](plugins/prompt-writer/reference/full-rules.md)`.

- [ ] **Step 5: CLAUDE.md - обновить описание репозитория**

Первый абзац раздела «Что это» говорит, что репозиторий состоит только из markdown. Теперь есть два JSON-манифеста. Добавить в конец абзаца:

```
The repository is also a plugin marketplace: `.claude-plugin/marketplace.json` at the root points at `plugins/prompt-writer/`, which is the plugin itself and the only part that ships to users.
```

- [ ] **Step 6: CLAUDE.md - одной строкой закрыть все пути архитектуры**

В разделе про progressive disclosure и в разделе про дублирование правил около восьми упоминаний путей вида `reference/full-rules.md`, `checklists/self-check.md`, `templates/`. Вместо правки каждого добавить строку в начало раздела архитектуры:

```
All skill paths in this file are relative to `plugins/prompt-writer/`.
```

Проверить, что после этого ни одно упоминание не вводит в заблуждение; если какое-то упоминание стоит вне этих двух разделов, дописать префикс явно.

- [ ] **Step 7: CLAUDE.md - переписать раздел «Известные блокеры публикации»**

Оба блокера закрыты. Заменить раздел целиком на:

```markdown
## Publication status

The repository is a marketplace with one plugin. Installed with
`/plugin marketplace add Londeren/prompt-writer-skill` and
`/plugin install prompt-writer@londeren-plugins`.

Not done yet: the submission to the Anthropic community catalog. It goes
through a web form at https://platform.claude.com/plugins/submit (individual
authors) or https://claude.ai/admin-settings/directory/submissions/plugins/new
(requires a Team or Enterprise organization). Pull requests against
anthropics/claude-plugins-community are closed automatically. The review
pipeline runs `claude plugin validate` plus automated safety screening.

The official catalog, claude-plugins-official, is curated by Anthropic at its
own discretion. There is no application process for it.

When editing the skill text, keep it platform neutral: present_files,
ask_user_input_v0 and the view tool exist only in claude.ai. The one place
that names a tool on purpose is the type question in SKILL.md, which names
both AskUserQuestion and ask_user_input_v0. The ask_user_input_v0 mention in
templates/agentic-task.md is an example of tool-description writing, not a
call, and must survive any search and replace.
```

- [ ] **Step 8: CLAUDE.md - дополнить раздел «Проверка изменений»**

Добавить в конец раздела:

````markdown
Manifests and packaging are checked with commands, not by eye:

```bash
claude plugin validate .
claude plugin validate ./plugins/prompt-writer --strict
claude --plugin-dir ./plugins/prompt-writer -p "..." --max-turns 1
find plugins/prompt-writer -type f | sort
```

The last one guards the packaging boundary: nothing from `docs/`,
`.superpowers/` or `tmp/` may appear in that listing.
````

- [ ] **Step 9: Проверить, что устаревших путей не осталось**

```bash
grep -nE '(^|[^/a-z])(SKILL\.md|reference/|templates/|checklists/)' README.md CLAUDE.md
```

Пройти каждое совпадение глазами. Допустимы: упоминания внутри дерева файлов, упоминания в разделе архитектуры CLAUDE.md, покрытые строкой из Step 6, и ссылки с префиксом `plugins/prompt-writer/`. Недопустимы: рабочие markdown-ссылки без префикса и команды установки со старыми путями.

Отдельно проверить, что исторические документы не тронуты:

```bash
git diff --name-only HEAD
```

Ожидается только `README.md` и `CLAUDE.md`. Файлы в `docs/superpowers/plans/`, `specs/`, `decisions/`, `translation/` — исторические записи о выполненной работе, их пути обновлять НЕ надо.

- [ ] **Step 10: Коммит**

```bash
git add README.md CLAUDE.md
git commit -m "docs: sync README and CLAUDE.md with the plugin layout

Install now goes through the marketplace, the file tree shows the
plugin boundary, and the publication blockers section is replaced by
the actual publication status."
```

---

### Task 4: Подготовка релиза и подача в community-каталог

**Files:**
- Modify: `plugins/prompt-writer/.claude-plugin/plugin.json` (keywords, version)

**Interfaces:**
- Consumes: все предыдущие задачи.
- Produces: тег и опубликованный маркетплейс, из которого установка проверена end-to-end.

- [ ] **Step 1: Починить keywords в plugin.json**

Сейчас в `keywords` есть `"russian"`. После перевода это неправда и будет вредить поиску. Файл целиком после правки:

```json
{
  "name": "prompt-writer",
  "displayName": "Prompt Writer",
  "version": "1.0.0",
  "description": "Methodology for writing LLM prompts, derived from analysis of Claude system prompts (Opus 4.7, Fable 5, Opus 5) and attention mechanics. Routes a request into one of five prompt types and applies the matching template, then audits the draft against a self-check list.",
  "author": {
    "name": "Sergey Lebedev",
    "url": "https://github.com/Londeren"
  },
  "homepage": "https://github.com/Londeren/prompt-writer-skill",
  "repository": "https://github.com/Londeren/prompt-writer-skill",
  "license": "MIT",
  "keywords": [
    "prompt-engineering",
    "system-prompt",
    "prompt-writing",
    "llm",
    "agent-prompt",
    "claude"
  ]
}
```

Версия поднята с `0.1.0` до `1.0.0`: поле `version` управляет доставкой обновлений пользователям, и первая публичная установка должна иметь осмысленную отправную точку. Если Сергей предпочитает стартовать с `0.1.0` — поменять здесь и в теге ниже согласованно.

- [ ] **Step 2: Валидация после правки**

```bash
claude plugin validate ./plugins/prompt-writer --strict
claude plugin validate .
```

Ожидается: оба `✔ Validation passed`.

- [ ] **Step 3: Коммит, тег, пуш**

Версия в манифесте и тег должны совпадать: расхождение версий — типовая причина отказа при подаче.

```bash
git add plugins/prompt-writer/.claude-plugin/plugin.json
git commit -m "chore(plugin): bump to 1.0.0 and drop the russian keyword"
git tag v1.0.0
git push origin main --tags
```

- [ ] **Step 4: Проверить установку с GitHub в изолированном окружении**

Это единственная проверка, которая ловит ошибки в `source`, в структуре репозитория и в правах доступа. Изолируем конфиг, чтобы не трогать рабочую установку Сергея:

Подкоманды `claude plugin marketplace add`, `claude plugin install` и `claude plugin list` в CLI существуют (проверено на 2.1.224).

```bash
export CLAUDE_CONFIG_DIR="$(mktemp -d)"
claude plugin marketplace add Londeren/prompt-writer-skill
claude plugin install prompt-writer@londeren-plugins
claude plugin list
find "$CLAUDE_CONFIG_DIR" -maxdepth 5 -type d -name 'prompt-writer*'
find "$CLAUDE_CONFIG_DIR" -type f -name 'SKILL.md'
```

Ожидается: маркетплейс добавился под именем `londeren-plugins`, плагин установился, обе команды `find` что-то нашли, и рядом с найденным `SKILL.md` нет ни `docs/`, ни `CLAUDE.md`. Если в установке видны они — `source` указывает не туда, вернуться к Task 2 Step 4.

**Проверка самой изоляции.** `CLAUDE_CONFIG_DIR` официальной документацией env-vars не подтвержден, поэтому нельзя считать по умолчанию, что изоляция сработала. Если обе команды `find` не нашли ничего — переменная не подхватилась, плагин уехал в рабочий `~/.claude`. Тогда: проверить наличие плагина в рабочем окружении через `claude plugin list`, убрать его (`claude plugin uninstall prompt-writer@londeren-plugins` и `claude plugin marketplace remove londeren-plugins`) и повторить проверку в отдельном пользователе или контейнере. Проверку не пропускать: она единственная ловит ошибки в `source`, в структуре репозитория и в правах доступа.

```bash
unset CLAUDE_CONFIG_DIR
```

- [ ] **Step 5: Предподачный чеклист**

Пройти по пунктам, каждый подтвердить фактом, а не ощущением:

- [ ] `claude plugin validate ./plugins/prompt-writer --strict` проходит без warning
- [ ] `claude plugin validate .` проходит
- [ ] `find plugins/prompt-writer -type f` не содержит ничего из `docs/`, `.superpowers/`, `tmp/`, `CLAUDE.md`
- [ ] `grep -rc 'present_files' plugins/prompt-writer/` возвращает 0 по всем файлам
- [ ] `version` в `plugin.json` совпадает с git-тегом
- [ ] `LICENSE` лежит внутри `plugins/prompt-writer/`
- [ ] `README.md` внутри `plugins/prompt-writer/` описывает установку из маркетплейса
- [ ] установка с GitHub в изолированном окружении прошла (Step 4)
- [ ] `homepage` и `repository` в манифесте открываются и ведут на публичный репозиторий

- [ ] **Step 6: Подача (ручной шаг Сергея, агент его не выполняет)**

Форма: https://platform.claude.com/plugins/submit для индивидуального автора, либо https://claude.ai/admin-settings/directory/submissions/plugins/new если есть Team/Enterprise-организация и права directory management.

Что происходит дальше: пайплайн прогоняет `claude plugin validate` и автоматический security-скан, затем идет одобрение на распространение. После аппрува плагин пиннится к конкретному commit SHA в `anthropics/claude-plugins-community`, CI двигает пин при новых коммитах. Публичный каталог синкается ночью, поэтому между аппрувом и появлением в `marketplace.json` есть задержка.

Проверять появление тут: https://github.com/anthropics/claude-plugins-community/blob/main/.claude-plugin/marketplace.json

- [ ] **Step 7: Записать факт подачи**

После отправки формы дописать в раздел «Publication status» в `CLAUDE.md` дату подачи и результат, когда он придет. Коммит `docs: record community catalog submission`.

---

## Что этот план сознательно НЕ делает

- **Не переносит скилл в `plugins/prompt-writer/skills/prompt-writer/`.** Плагин везет ровно один скилл, а раскладка с `SKILL.md` в корне плагина официально поддерживается и не создает лишнего уровня вложенности для `reference/`, `templates/`, `checklists/`. Если когда-нибудь скиллов станет два, переезд на `skills/` — отдельная задача.
- **Не использует источник `git-subdir`.** Он экономит трафик sparse-клоном на монорепах; на репозитории такого размера выигрыш нулевой, а манифест пришлось бы жестко связать с URL репозитория.
- **Не заводит отдельный репозиторий для публикации.** Максимальная чистота ценой постоянной синхронизации двух репозиториев.
- **Не обновляет пути в `docs/superpowers/`.** Планы, спеки и decisions — исторические записи о выполненной работе. Около 340 упоминаний путей там описывают состояние на момент своего написания.
- **Не трогает `.gitignore` и не выносит `.superpowers/` из репозитория.** После Task 2 они и так не попадают в плагин.

## Найденное попутно, кандидат в следующий этап

У CLI есть встроенный раннер эвалов для плагинов: `claude plugin eval [target]` прогоняет кейсы из `evals/**/case.yaml` (или `evals/**/prompt.md` плюс `graders/*.md`) против плагина, резолвит его по пути, имени или `plugin@marketplace`, и добавляет baseline-плечо без плагина. Это ровно тот контур, который сейчас закрывает пользовательский скилл `autoresearch`, но в штатном и воспроизводимом виде — и он дает объективную метрику качества, которую можно показать при подаче. Отдельный этап: перенести существующие evals в `plugins/prompt-writer/evals/`, зафиксировать baseline, положить результат в README.
