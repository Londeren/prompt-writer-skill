# English Translation (EN-only) Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Перевести скилл prompt-writer на английский (EN-only, русская версия не сохраняется) без смысловых потерь, с пятислойной верификацией полноты и intent.

**Architecture:** Markdown-only репозиторий, шесть слоев дублирования правил. Порядок перевода — от полной версии к выжимкам: инфраструктура (глоссарий, карта обратной сборки, карта замен) → full-rules → SKILL.md → modal-registers → templates → checklists → витрины. Верификация: слой 1 (механические инварианты) после каждого файла и по корпусу; слои 2-4 (анкор к tmp/, intent-карты, обратный перевод) после корпуса; слой 5 (функциональный) в конце.

**Tech Stack:** Markdown, git, grep. RU-оригиналы после перевода доступны только через `git show BASE_RU:<path>` — BASE_RU фиксируется в Task 1.

**Spec:** `docs/superpowers/specs/2026-08-08-english-translation-design.md` — источник всех принципов; при расхождении плана и спеки главенствует спека.
**Обязательное чтение:** `docs/superpowers/decisions/2026-08-09-audit-loop-controller-decisions.md`.

## Global Constraints

- **EN-only:** русская копия не сохраняется, история в git. Русские фразы-триггеры в frontmatter `description` SKILL.md сохраняются намеренно.
- **Перевод и контентные изменения не смешивать в одном коммите.** Единственное контентное изменение этапа — строка про язык вывода в SKILL.md (Task 3, отдельный коммит). Замены языкозависимых примеров (Ё → em dash и по карте) — часть перевода, не контентное изменение.
- **Содержание правил не меняется:** каждое правило после перевода утверждает то же, с тем же регистром модальности, rationale и областью применения.
- **Обратная сборка:** для правил, производных от системных промптов Claude, EN-формулировка восстанавливается из `tmp/CLAUDE-FABLE-5.md` / `tmp/OPUS-5.md` по карте (Task 1), не переводится с русского. Режимы: verbatim / phrase / free.
- **Глоссарий обязателен:** один термин — ровно один EN-эквивалент во всех слоях. Переводчики получают глоссарий в каждом диспатче.
- **Dogfooding:** перевод каждого файла идет циклом черновик → аудит → финал. Аудит-вопросы (презумпция дефекта): «Какое master-правило нарушает эта EN-версия и где именно?» и «Что в этом тексте звучит как калька с русского, а не как native-промпт?» Финал закрывает найденное; аудит без правок — не аудит.
- **EN-стиль по humanizer:** в переведенной прозе нет em dashes (заменяются точкой/запятой/двоеточием/скобками), нет Title Case в заголовках, нет softening («try to», «ideally», «where possible»), straight quotes. Em dash в тексте скилла допустим только как УПОМИНАЕМЫЙ пример запрета (внутри правил и примеров про em-dash ban).
- **Шапки-источники:** первые строки каждого файла, называющие основу методологии, переводятся согласованно: `the Claude system prompts (Opus 4.7, Fable 5, Opus 5)`.
- **Коммит после каждой задачи**, трейлер `Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>`. Переводческие коммиты с префиксом `translate:`, фиксы верификации `fix(translation):`.
- **Верификация слоев 2-4:** флаги чинит переводчик, проверяет тот же слой заново; слой закрыт после двух проходов подряд без новых флагов. Проверяющие — свежие субагенты: слой 3 получает только пару RU/EN + глоссарий (без права чинить); слой 4 получает ТОЛЬКО EN (RU-оригинал не передается).
- Артефакты перевода живут в `docs/superpowers/translation/` и коммитятся.

---

### Task 1: Инфраструктура перевода - глоссарий, карта обратной сборки, карта замен, базовые метрики

**Files:**
- Create: `docs/superpowers/translation/glossary.md`
- Create: `docs/superpowers/translation/reverse-assembly-map.md`
- Create: `docs/superpowers/translation/example-replacement-map.md`
- Create: `docs/superpowers/translation/baseline-metrics.md`

**Interfaces:**
- Produces: четыре артефакта, которые получает каждый последующий переводчик и верификатор. BASE_RU = SHA HEAD до первого переводческого коммита, записывается первой строкой baseline-metrics.md.

- [ ] **Step 1: Зафиксировать BASE_RU и базовые метрики**

В `baseline-metrics.md` записать BASE_RU (`git rev-parse HEAD`) и вывод команд (по каждому файлу корпуса):

```bash
grep -c '^### [0-9]*\.' SKILL.md                     # master rules: ожидается 24
sed -n '/^## Quick reference/,/^Полные правила/p' SKILL.md | grep -c '^[0-9]*\.'   # QR: 24
grep -c '^### [0-9]' reference/full-rules.md           # разделы full-rules
sed -n '/^## 8\./,$p' reference/full-rules.md | grep -c '^[0-9]*\. '               # итоговые принципы: 40
grep -c '^- \[ \]' checklists/self-check.md            # пункты self-check: 63
grep -c '^- \[ \]' reference/full-rules.md             # пункты §6.2: 26
for f in SKILL.md reference/*.md templates/*.md checklists/*.md; do echo "$f: $(grep -c '<example>' $f)"; done
grep -rn '[Ёё]' SKILL.md reference/ templates/ checklists/ README.md CLAUDE.md     # все вхождения Ё - для карты замен
```

Плюс регистровая карта: для каждого master-правила SKILL.md и раздела full-rules зафиксировать список модальных маркеров (НИКОГДА/ВСЕГДА капсом, «никогда», «всегда», «должен», «может», «избегает», «предпочитает», NEVER/ALWAYS/should/can/avoids/prefers уже в тексте) в формате «правило N: маркеры». Карта нужна слою 1 для проверки «регистр не сдвинулся».

- [ ] **Step 2: Глоссарий**

`glossary.md`: взять стартовую таблицу RU→EN из спеки (раздел «Глоссарий до перевода») целиком, затем закрыть по корпусу: пройти SKILL.md, full-rules, modal-registers, self-check, input-triage, templates грепом по повторяющимся терминам методологии и добавить недостающее (минимум: термины этапов 1-3 - «маркер безусловности» → `unconditionality marker`, «полный арсенал» → `full arsenal`, «реприза» → `compressed reprise at the end`, «выборочный цикл» → `selective loop`, «граничный случай/пример» → `boundary case / boundary example`, «прибитая область» → `pinned scope`, «сводка аудита» → `audit summary`, «эскалация» → `escalation`, «триггеры распознавания» → `recognition triggers`, «стоп-условия» → `stop conditions`, «правило близости» → `proximity principle`). Каждый термин - ровно один EN-эквивалент.

- [ ] **Step 3: Карта обратной сборки**

`reverse-assembly-map.md`: таблица «правило/раздел → источник (файл:строки в tmp/) → режим (verbatim/phrase/free)». Обязательные стартовые анкоры - таблица из спеки (правила 16, 17, 23, 24, 2, разделы 4.5, 4.10, 4.20, лестница, кумулятивная оценка) - переносится целиком. Затем закрыть по всем master rules 1-24 и разделам full-rules 1.1-7.7: для каждого правила с текстом «из системного промпта Claude» найти оригинал грепом по `tmp/CLAUDE-FABLE-5.md` и `tmp/OPUS-5.md`; найден - режим verbatim или phrase со ссылкой файл:строка; не найден (источник Opus 4.7) - режим free, явной строкой.

- [ ] **Step 4: Карта замен примеров**

`example-replacement-map.md` - все языкозависимые замены, с перечислением каждого вхождения (файл:строка на BASE_RU):

1. **Ё-пример → em dash ban** (все ~19 вхождений из грепа Step 1). Канонические формулировки по регистрам (для modal-registers и всех слоев):
   - Descriptive: `The assistant writes without em dashes.`
   - NEVER (пример неправильного усиления - как сейчас с Ё): `The assistant NEVER uses em dashes.`
   - Should: `The assistant should replace em dashes with commas or periods.`
   - Can: `The assistant can keep an em dash when quoting a user's own text verbatim.`
   - Avoids: `The assistant avoids em dashes.`
   - Prefers: `The assistant prefers commas over em dashes for asides.`
   - Master-rules пример (было «Русский, без буквы Ё»): `- No em dashes (replace with comma or period)`
2. **«С уважением / Здравствуйте / Благодарю за обращение» → `"Best regards," "Dear Sir/Madam," "Thank you for reaching out"`** (4 вхождения: SKILL.md правило 5, full-rules 2.6, self-check, identification-frame).
3. **Пример правила 18/4.13** «заголовки в title case в каждой секции» → `sentence case in every section heading, not only the first` (перевернут, чтобы не противоречить humanizer).
4. **Пул дополнительных бинарных стиль-примеров** (для разнообразия там, где сейчас всюду Ё; использовать, когда в одном блоке нужно больше одного примера): `no emojis in headings`, `sentence case in headings, never Title Case`, `straight quotes, not curly quotes`, `never opens with "Great question!"`, `never closes with "I hope this helps" or "Would you like me to..."`, `no rule-of-three constructions`.
5. **Русские анти-паттерн формулы AI-стиля** («Отличный вопрос», «Рад помочь», «Надеюсь помог», «В заключение», «Если есть вопросы - обращайтесь») → `"Great question!", "Happy to help!", "I hope this helps", "In conclusion", "Feel free to reach out"`.

- [ ] **Step 5: Верификация и коммит**

Run: `ls docs/superpowers/translation/` - четыре файла. Проверить: глоссарий не содержит термин с двумя EN-эквивалентами; карта обратной сборки покрывает все правила 1-24 (24 строки минимум) и каждая строка имеет режим.

```bash
git add docs/superpowers/translation/
git commit -m "docs: translation infrastructure - glossary, reverse-assembly map, replacement map, baseline metrics"
```

---

### Task 2: Перевод reference/full-rules.md

**Files:**
- Modify: `reference/full-rules.md` (полная замена содержимого на EN)

**Interfaces:**
- Consumes: все четыре артефакта Task 1.
- Produces: канонические EN-формулировки всех правил - SKILL.md (Task 3) сжимает выжимки из этого файла, не переводит с русского.

- [ ] **Step 1: Черновик перевода**

Перевести файл целиком по порядку разделов. Для каждой строки карты обратной сборки применить режим: verbatim - вставить оригинал из tmp/ дословно; phrase - использовать фразеологию оригинала; free - перевод с сверкой терминов по глоссарию. Замены примеров по карте Task 1. Структура (номера разделов, заголовки-уровни, код-блоки, порядок) сохраняется 1:1. Заголовки в sentence case.

- [ ] **Step 2: Аудит и финал (dogfooding)**

Аудит черновика: два вопроса из Global Constraints + проверка регистровой карты этого файла (маркеры each раздела совпадают с baseline). Финал закрывает каждый найденный пункт.

- [ ] **Step 3: Слой 1 по файлу**

```bash
grep -c '^### [0-9]' reference/full-rules.md    # = значение из baseline
grep -c '^- \[ \]' reference/full-rules.md      # = 26
sed -n '/^## 8\./,$p' reference/full-rules.md | grep -c '^[0-9]*\. '  # = 40
grep -c '<example>' reference/full-rules.md     # = baseline
grep -n '[А-Яа-я]' reference/full-rules.md      # пусто
head -3 reference/full-rules.md                 # шапка-источник: the Claude system prompts (Opus 4.7, Fable 5, Opus 5)
```

Em-dash проверка: `grep -n '—' reference/full-rules.md` - вхождения только внутри правил/примеров, ГОВОРЯЩИХ про em dash (проверить каждое вручную).

- [ ] **Step 4: Commit**

```bash
git add reference/full-rules.md
git commit -m "translate: reference/full-rules.md to English"
```

---

### Task 3: Перевод SKILL.md + отдельным коммитом строка про язык вывода

**Files:**
- Modify: `SKILL.md`

**Interfaces:**
- Consumes: EN full-rules (Task 2) - Master rules и Quick reference сжимаются из него; глоссарий и карты Task 1.
- Produces: канонический EN SKILL.md; frontmatter description ≤1024 символов.

- [ ] **Step 1: Черновик перевода**

Перевести SKILL.md. Master rules 1-24 и Quick reference 1-24 - выжимки из уже переведенного full-rules (та же лексика, тот же регистр), не независимый перевод. Routing, процесс, workflow-сценарии - перевод по глоссарию. Замены примеров по карте. Инструменты claude.ai-окружения (present_files, ask_user_input_v0, view) - переводить как есть, НЕ платформо-нейтрализовать (это отдельный известный блокер публикации, не задача этого этапа).

- [ ] **Step 2: Frontmatter description - сжать до ≤1024 символов**

Текущая длина значения description ~1135 символов - превышает лимит спецификации скиллов (1024). Сжимать английскую часть (убрать перечислительную избыточность), русские фразы-триггеры («напиши промпт», «сделай инструкцию для модели», «помоги настроить ассистента», «улучши этот промпт», «промпт для Claude Code», «задание для кодинг-агента») сохранить все. Проверка: `awk '/^description:/{print length($0)-13}' SKILL.md` ≤ 1024.

- [ ] **Step 3: Аудит и финал (dogfooding)**

Два аудит-вопроса + сверка каждого master-правила с EN full-rules: выжимка не противоречит полной версии, номера совпадают, регистры совпадают с регистровой картой.

- [ ] **Step 4: Слой 1 по файлу + commit перевода**

```bash
grep -c '^### [0-9]*\.' SKILL.md   # = 24
sed -n '/^## Quick reference/,/full-rules.md/p' SKILL.md | grep -c '^[0-9]*\.'  # = 24
grep -c '<example>' SKILL.md       # = baseline (10)
grep -n '[А-Яа-я]' SKILL.md        # только строка 3 (frontmatter description, RU-триггеры)
```

```bash
git add SKILL.md
git commit -m "translate: SKILL.md to English, compress frontmatter description to spec limit"
```

- [ ] **Step 5: Контентное изменение отдельным коммитом - строка про язык вывода**

В переведенную секцию процесса (после шага применения шаблона, рядом с описанием заполнения) добавить дословно:

```
The methodology is language-agnostic. Write the generated prompt in the language of the user's task and audience, not in the language of this skill. Register names (NEVER/should/can/avoids/prefers) stay in English inside any prompt - they are anchors for the model, not prose.
```

```bash
git add SKILL.md
git commit -m "feat: add output-language rule - generated prompts follow the task language"
```

---

### Task 4: Перевод reference/modal-registers.md

**Files:**
- Modify: `reference/modal-registers.md`

**Interfaces:**
- Consumes: глоссарий, карта замен (канонические em-dash формулировки шести регистров), EN full-rules.

- [ ] **Step 1: Черновик перевода**

Эпицентр Ё-замены: все шесть регистров переписываются на em-dash примере по каноническим формулировкам карты Task 1. Где в RU-версии несколько Ё-примеров подряд - использовать пул дополнительных примеров для разнообразия. Примеры из системного промпта Claude - обратная сборка (найти оригиналы грепом по tmp/).

- [ ] **Step 2: Аудит и финал; слой 1 по файлу**

Аудит-вопросы + проверка: шесть регистров на месте, каждый показан на бинарном проверяемом примере. `grep -n '[А-Яа-я]' reference/modal-registers.md` пусто; `grep -c '[Ёё]' reference/modal-registers.md` = 0.

- [ ] **Step 3: Commit**

```bash
git add reference/modal-registers.md
git commit -m "translate: reference/modal-registers.md to English, em-dash ban as the running example"
```

---

### Task 5: Перевод templates/ (5 файлов)

**Files:**
- Modify: `templates/character-frame.md`, `templates/identification-frame.md`, `templates/one-shot-task.md`, `templates/extraction-prompt.md`, `templates/agentic-task.md`

**Interfaces:**
- Consumes: EN full-rules и SKILL.md (ссылки «правило N» должны совпадать по номерам и лексике), глоссарий, карты.

- [ ] **Step 1: Черновик перевода всех пяти файлов**

Скелеты промптов внутри код-блоков переводятся тоже (это шаблоны генерируемых промптов). Ссылки «(правило N)» → `(rule N)` с теми же номерами. identification-frame: Ё-пример в скелете master_rules → em-dash формулировка; «С уважением...» → EN-эквиваленты по карте. extraction/agentic: обратная сборка цитат (лестница OPUS 1284-1306, пред-интерпретация OPUS 973, ask_user_input_v0 / suggest_connectors FABLE 648-650, 1232-1234).

- [ ] **Step 2: Аудит и финал; слой 1 по файлам**

По каждому файлу: `grep -n '[А-Яа-я]' templates/*.md` пусто; `grep -c '<example>'` = baseline по файлу; код-фенсы сбалансированы (четное число ``` в каждом файле); формула агентного промпта - все шесть частей на месте.

- [ ] **Step 3: Commit**

```bash
git add templates/
git commit -m "translate: all five templates to English"
```

---

### Task 6: Перевод checklists/ (2 файла)

**Files:**
- Modify: `checklists/self-check.md`, `checklists/input-triage.md`

**Interfaces:**
- Consumes: EN full-rules, SKILL.md, templates (пункты чеклистов ссылаются на их формулировки и номера правил).

- [ ] **Step 1: Черновик, аудит, финал**

Перевод по глоссарию, ссылки `(rule N)` сверяются с EN SKILL.md. Ё-пункты и «С уважением» - по карте замен.

- [ ] **Step 2: Слой 1 по файлам + commit**

```bash
grep -c '^- \[ \]' checklists/self-check.md   # = 63
grep -n '[А-Яа-я]' checklists/                # пусто
git add checklists/
git commit -m "translate: checklists to English"
```

---

### Task 7: README.md, CLAUDE.md - витринные слои

**Files:**
- Modify: `README.md` (переписать на EN, структуру сохранить)
- Modify: `CLAUDE.md` (перевести на EN; конвенция «без буквы ё» удаляется, вместо нее конвенция canonical example)

**Interfaces:**
- Consumes: устоявшаяся EN-терминология всех предыдущих задач.

- [ ] **Step 1: README.md**

Переписать на английском: структура секций сохраняется (что делает, пять типов таблицей, ключевые принципы, установка Claude Code / claude.ai, использование, дерево файлов, лицензия). Двуязычная EN-врезка сверху больше не нужна - весь файл английский; счетчик «24 master rules», «five prompt types» сохраняются. Примеры активационных фраз: оставить одну русскую («напиши промпт для...») как иллюстрацию кросс-язычных триггеров.

- [ ] **Step 2: CLAUDE.md**

Перевести на английский. Раздел про конвенцию «без буквы ё» заменить на:

```
Convention: the canonical running example of a hard style rule throughout the skill is the em dash ban ("never uses em dashes"). Meta-documents follow the same style: no em dashes in prose.
```

Карта слоев синхронизации, блокеры публикации, смоук-тест - переводятся с сохранением смысла. Упоминание «весь контент на русском» заменить на «all content in English; Russian trigger phrases in the SKILL.md frontmatter are intentional».

- [ ] **Step 3: Слой 1 + commit**

```bash
grep -n '[А-Яа-я]' README.md CLAUDE.md   # README: максимум одна строка-иллюстрация триггера; CLAUDE.md: строка про русские триггеры, если содержит кириллицу в цитате
git add README.md CLAUDE.md
git commit -m "translate: README and CLAUDE.md to English, swap canonical example convention"
```

---

### Task 8: Верификация - слои 1-4 по корпусу

**Files:**
- Create: `docs/superpowers/translation/verification-report.md` (наполняется по слоям)
- Modify: файлы корпуса (фиксы по флагам, коммиты `fix(translation): ...`)

- [ ] **Step 1: Слой 1 - полный корпус**

Все команды baseline-metrics против EN-версии: счетчики совпадают (отклонения - только по карте замен); регистровая карта по каждому правилу совпадает; `grep -rn '[А-Яа-я]' SKILL.md reference/ templates/ checklists/` - только frontmatter SKILL.md; шапки-источники всех файлов согласованы; em dash только в говорящих про него местах; `grep -rn '[Ёё]'` по корпусу = 0. Результаты в отчет.

- [ ] **Step 2: Слой 2 - анкор к tmp/ (субагент)**

Свежий субагент получает reverse-assembly-map.md и EN-файлы: для каждой строки verbatim/phrase сверить EN-формулировку с оригиналом в tmp/ (файл:строка). Перефраз там, где есть оригинал - флаг. Флаги чинит переводчик, повторная проверка тем же слоем; закрыт после двух проходов подряд без новых флагов.

- [ ] **Step 3: Слой 3 - intent-карты (свежие субагенты, адверсариально)**

Покрытие: все 24 master-правила + все разделы full-rules глав 1-7 + оба чеклиста + пять шаблонов. Батчи по 5-6 правил на верификатора. Каждый верификатор получает ТОЛЬКО: RU-оригинал правила (`git show BASE_RU:<path>` выдержка), EN-версию, глоссарий. Задача адверсариальная: «найди расхождение хотя бы в одном из пяти полей: claim / rationale / примеры (включая граничные) / регистр / scope». Вердикт по каждому правилу: match или расхождение с описанием. Без права чинить. Расхождения чинит переводчик, перепроверка тем же батчем; слой закрыт после двух чистых проходов.

- [ ] **Step 4: Слой 4 - обратный перевод (детектор дрейфа)**

Независимый агент БЕЗ доступа к RU-оригиналу переводит EN → RU: полностью SKILL.md и modal-registers.md; выборочно 3-4 раздела full-rules (взять 2.7, 4.12, 4.17, 4.20 - самые нагруженные этапами 1-3) и один шаблон (extraction-prompt). Контроллер диффит обратный перевод с `git show BASE_RU:<path>`: расхождения смысла (не стиля) - флаги переводчику. Закрытие - как у слоев 2-3.

- [ ] **Step 5: Отчет и коммит**

Таблица «правило/раздел × слой 1-4 → вердикт» в verification-report.md.

```bash
git add docs/superpowers/translation/verification-report.md [исправленные файлы]
git commit -m "docs: translation verification report - layers 1-4 closed"
```

(Фиксы по флагам коммитятся по мере закрытия слоев отдельными `fix(translation):` коммитами.)

---

### Task 9: Слой 5 - функциональная проверка и финальный отчет

**Files:**
- Modify: `docs/superpowers/translation/verification-report.md` (слой 5 + итог)

- [ ] **Step 1: Self-check EN-версии**

Прогнать EN SKILL.md + шаблоны по переведенному checklists/self-check.md (субагент): пункты чеклиста находят свои объекты в EN-файлах, ссылки на правила не битые.

- [ ] **Step 2: Двуязычный смоук-тест**

Субагент 1: `write a prompt for a customer support assistant` - routing выбирает тип A, шаблон подгружается, результат проходит self-check, вывод на английском. Субагент 2: `напиши промпт для ассистента поддержки` - скилл срабатывает (триггеры), результат на русском (язык задачи), методология применяется та же.

- [ ] **Step 3: Итог отчета + коммит**

Критерий готовности из спеки: слой 1 сходится, флаги 2-4 закрыты или явно приняты с обоснованием, слой 5 пройден. Финальный глоссарий и отчет - в docs/superpowers/translation/.

```bash
git add docs/superpowers/translation/
git commit -m "docs: translation complete - layer 5 functional checks passed"
```

Отчет пользователю: что сделано, результаты проверок по слоям, список коммитов.

---

## Model selection notes (для SDD-контроллера)

Перевод - работа суждения, не транскрипция: переводчики файлов - стандартная модель (sonnet), full-rules и SKILL.md - допустимо повысить. Верификаторы слоев 2-4 - sonnet, свежий контекст, изоляция по спеке. Финальное ревью ветки - самая способная модель.

## Self-Review (выполнено при написании плана)

Спека покрыта: обратная сборка + карта (Task 1/Step 3, Task 2), глоссарий закрытый (Task 1/Step 2), замены примеров (Task 1/Step 4, Tasks 4-6), frontmatter лимит (Task 3/Step 2 - фактическая длина 1135 > 1024, сжатие обязательно), новое правило про язык вывода отдельным коммитом (Task 3/Step 5), dogfooding-цикл в каждом переводческом таске, порядок файлов по спеке (full-rules → SKILL → modal-registers → templates → checklists → витрины), пять слоев верификации с изоляцией проверяющих и правилом двух чистых проходов (Tasks 2-9), шапки-источники как инвариант (слой 1), README/CLAUDE.md (Task 7), «Что НЕ делаем» соблюдено (RU-копия не сохраняется, триггеры остаются, docs/superpowers и tmp/ не переводятся).
