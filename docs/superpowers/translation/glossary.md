# Глоссарий перевода RU → EN

BASE_RU: `5148d66afea0c857e1909797f4a286c36576c529` (см. `baseline-metrics.md`)

Рабочий документ (Task 1 стадии english-translation). Каждый термин методологии — ровно один EN-эквивалент. Источник: спека `docs/superpowers/specs/2026-08-08-english-translation-design.md`, раздел «Глоссарий до перевода» (таблица ниже перенесена целиком), расширенная сплошным грепом по SKILL.md, reference/full-rules.md, reference/modal-registers.md, checklists/*.md, templates/*.md (Task 1, Step 2 брифа).

## 1. Стартовая таблица (из спеки, verbatim)

| RU | EN |
|---|---|
| decision-блок | decision block |
| decision-type структура | decision-type structure |
| тематическая структура | topical structure |
| регистр модальности | modality register |
| вопрос-тест | diagnostic test question |
| закрывать лазейку, называя оправдание | close the loophole by naming the excuse |
| прибивать область применения | pin the scope explicitly |
| смягчение / softening | softening |
| контраст-пара | contrast pair |
| дублирование критичных правил | duplication of critical rules |
| правило близости | proximity rule (rule lives next to its decision point) |
| полный арсенал (из Fable/Opus-спеки) | full arsenal for hard limits |
| tie-breaker при сомнении | when-in-doubt tie-breaker |
| числовые пороги | numeric thresholds |
| мета-детектор рационализации | rationalization detector |
| пред-интерпретация наблюдений | pre-interpreting expected observations |
| лестница решений stop-at-first-match | decision ladder, stop at first match |
| кумулятивная оценка | cumulative-output judgment |
| инвариант источника | source invariant |
| черновик / аудит / финал | draft / audit / final version |
| цикл черновик → аудит → финал | draft → audit → final loop |
| презумпция дефекта | presumption of defect |
| класс ответов | answer class |
| цена ошибки | cost of error |
| формула агентного промпта | agentic prompt formula |
| начальное / целевое состояние | initial state / target state |
| запрещенные действия | forbidden actions |
| триаж входного промпта | input triage |
| поломка (в триаже) | defect |
| инертные данные | inert data |
| секреты | secrets |
| сводка аудита | audit summary |
| распухание аудита | audit bloat |

32 строки, состав и формулировки не изменены относительно спеки.

## 2. Расширение — минимум из брифа Task 1 (Step 2)

Термины этапов 1-3 (правила 16-24, Тип E, agentic-паттерны), которых стартовая таблица не покрывала:

| RU | EN |
|---|---|
| маркер безусловности | unconditionality marker |
| полный арсенал (общая форма, вне hard-limit контекста; см. §4 «Разрешенные коллизии» ниже) | full arsenal |
| реприза (сжатая реприза в конце промпта) | compressed reprise at the end |
| выборочный цикл | selective loop |
| граничный случай / граничный пример / боундари-кейс | boundary case / boundary example |
| эскалация | escalation |
| триггеры распознавания | recognition triggers |
| стоп-условия | stop conditions |

`сводка аудита` и `правило близости` — брифом заявлены как минимальные добавления, но оба термина уже есть в стартовой таблице (§1). Новых строк для них не заводится; коллизия формулировок разобрана в §4.

## 3. Расширение — сплошной грепп по корпусу (Task 1, Step 2)

Термины, повторяющиеся минимум в двух из шести слоев (SKILL.md master rules + quick reference, full-rules.md, modal-registers.md, checklists/*, templates/*), не покрытые §1-2:

| RU | EN |
|---|---|
| прибитая область (прилагательное: «область прибита явно») | pinned scope |
| анти-модель / анти-модели | negative anchor(s) |
| дефолтная позиция / default stance | default stance |
| взаимоисключающие маршруты | mutually exclusive routes |
| имитация (конкретного человека) | imitation |
| качество имитации | quality of imitation |
| ground-in-quotes / «процитировать релевантные части» | ground in quotes |
| felt-quality (калибровка тона) | felt-quality calibration |
| character framing | character framing (unchanged, already English in RU text) |
| identification framing | identification framing (unchanged, already English in RU text) |
| preamble / преамбула | preamble |
| self-check вопросы | self-check questions |
| failure mode / провальная траектория | failure mode |
| override | override (unchanged) |
| routing / роутинг | routing |
| decision-блок vs тематический блок (см. §1 decision-type структура) | — покрыто §1, отдельной строки не требует |

## 4. Разрешенные коллизии

Две пары терминов из брифа и корпуса конфликтуют по формулировке при одинаковом или пересекающемся смысле. Зафиксировано явное разрешение — по одному канону на термин, чтобы соблюсти правило «ровно один EN-эквивалент»:

- **«правило близости».** Стартовая таблица (§1, спека, обязательна verbatim) дает `proximity rule (rule lives next to its decision point)`. Бриф Task 1 отдельно требует `proximity principle` как формулировку для того же RU-термина. Канон: строка §1 остается неизменной (verbatim-требование спеки имеет приоритет); `proximity principle` — допустимый короткий алиас в прозе переводов reference/full-rules.md, не отдельное значение глоссария. Переводчик слоев 2-4 использует `proximity rule (rule lives next to its decision point)` как индексовую запись; `proximity principle` не вводит второго эквивалента, это стилистический вариант той же записи.
- **«полный арсенал».** Стартовая таблица дает специфическую форму `полный арсенал (из Fable/Opus-спеки) → full arsenal for hard limits` — она об одном конкретном паттерне (весь арсенал усиления для hard limits: капс, числовой порог, дублирование, self-check, последствия, примеры). Корпус (SKILL.md:181 «весь арсенал сразу», full-rules.md:271 «оформляются полным арсеналом») использует общую форму без явной привязки к hard limits. Канон: `full arsenal for hard limits` — при первом введении термина и в reference/full-rules.md §2.7/3.5 (формальный контекст); `full arsenal` — сокращенная форма в прозе после первого введения, не самостоятельная запись. Оба указывают на одну и ту же строку глоссария, не на две разные.

## 5. Правило применения

Каждый RU-термин из таблиц §1-3 имеет ровно одну EN-запись. Где корпус использует несколько RU-синонимов одного понятия (граничный случай / граничный пример / боундари-кейс; анти-модель / анти-модели), все синонимы сведены в одну строку с одним EN-эквивалентом. Коллизии §4 — единственные найденные конфликты источников; оба разрешены explicit canon + alias, не двумя параллельными значениями.
