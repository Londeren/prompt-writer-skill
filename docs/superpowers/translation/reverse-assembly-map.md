# Карта обратной сборки: правило/раздел → источник (tmp/) → режим

BASE_RU: `5148d66afea0c857e1909797f4a286c36576c529`

Формат режима: `verbatim` — вставить оригинал дословно; `phrase` — использовать фразеологию/структуру оригинала, не копировать 1:1; `free` — источника в tmp/ нет (вывод из анализа, либо формулировка восходит к Opus 4.7, которого нет в репозитории), переводить самостоятельно, сверяя терминологию с tmp/ как эталоном стиля.

`tmp/CLAUDE-FABLE-5.md` сокращено как `FABLE`, `tmp/OPUS-5.md` — как `OPUS`. Номера строк соответствуют файлам как они лежат на момент задачи (1598 и 2050 строк — см. `baseline-metrics.md` не фиксирует их отдельно, т.к. tmp/ не версионируется; строки сняты в рамках Task 1).

## 1. Стартовые анкоры из спеки (перенесены целиком)

| Правило скилла | Источник | Режим |
|---|---|---|
| 16 (вопрос-тест) | FABLE 458 «The test: does answering require knowing what that thing is?» | verbatim |
| 16 (двусторонний тест) | OPUS 767 «The test cuts both ways» | verbatim |
| 17 (закрытая лазейка) | FABLE 38 «does not rationalize compliance by citing public availability...», FABLE 274 «Urgency is not an exception», 286 «does not license» | verbatim |
| 17 (мета-детектор) | FABLE 55 / OPUS 48 «that reframing is the signal to REFUSE» | verbatim |
| 17 (маркер безусловности) | FABLE 434 «This check is unconditional» | verbatim |
| 23 (числовые пороги) | FABLE 440-533 (copyright-лимиты), 462 (шкала tool calls) | phrase |
| 24 (tie-breaker) | FABLE 333 «when in doubt err toward», OPUS 989 «err on the side of continuing» | phrase |
| 2 (полный арсенал) | FABLE 492-533, 565-568 (структура copyright-секции) | phrase |
| 4.5 (default stance) | OPUS 38-40 «defaults to helping... do not meet that bar» | verbatim |
| 4.10 (инвариант источника) | FABLE 132 / OPUS 127 «will never send reminders that reduce...» | phrase |
| 4.20 (пред-интерпретация) | OPUS 973 (end_conversation confirmation) | phrase |
| 4.20 (правила в description) | FABLE 648-650 (ask_user_input_v0), 1232-1234 (suggest_connectors) | phrase |
| Лестница решений (extraction) | OPUS 1284-1306 «stopping at the first match», «category match, not style preference» | verbatim |
| Кумулятивная оценка (character) | OPUS 61 «judges the cumulative output... past assistance is not authorization» | verbatim |

Проверено при имплементации Task 1 (грепом по tmp/, см. §2-3): все 14 строк подтверждены, номера строк совпадают.

## 2. Master rules SKILL.md (1-24) — полное покрытие

| Правило | Источник | Режим |
|---|---|---|
| 1. Decision-type структура, не тематическая | Нет прямой цитаты в теле правила 1; анализ структуры системного промпта (блоки `refusal_handling`, `tone_and_formatting`, `user_wellbeing` — см. full-rules 3.1 ниже) обосновывает вывод, но само правило 1 — обобщение методологии | free |
| 2. Минимум 4 регистра модальности | Композитное правило; отдельные регистровые примеры — см. full-rules 2.1 (частично verbatim). Текст самого master-правила 2 не цитирует систем-промпт напрямую | free |
| 3. Примеры обязательны для каждого сложного правила | Общий методологический вывод (LLM обобщают по сходству), не цитата | free |
| 4. Никакого softening | Общий вывод; конкретные слова-кандидаты («please», «try to») не найдены как цитата в FABLE/OPUS — в системных промптах Claude softening-слов, по определению правила, нет вовсе (отсутствие — не цитируемый факт) | free |
| 5. Позитивные формулировки для тона, явные anti-patterns для конкретных запретов | RU-примеры запретов («С уважением», «Здравствуйте») — собственные, не из tmp/; принцип общий | free |
| 6. Master rules в начале промпта | Общий вывод про primacy bias; не цитата конкретного текста FABLE/OPUS | free |
| 7. Критичные правила дублируются рядом с применением | Общий структурный вывод, не цитата | free |
| 8. Длина описания пропорциональна важности | Наблюдение о пропорции (copyright — многостраничный блок, запрет на эмодзи — одна строка); структурный факт, не прямая цитата одной фразы | free |
| 9. Reasoning встроен в правило | RU-пример про копирайт-rationale — пересказ идеи, не дословная цитата из FABLE/OPUS | free |
| 10. Self-check вопросы перед критичными действиями | Общий принцип; конкретный список вопросов — см. full-rules 4.4 (verbatim, FABLE/OPUS copyright self-check) | free (для текста самого master-правила; конкретизация в full-rules 4.4 verbatim) |
| 11. XML-теги для структуры (градация по сложности) | Упоминает `<claude_behavior>`, `<refusal_handling>` — реальные теги OPUS 3 `<claude_behavior>`, OPUS 42 `<refusal_handling>` | phrase |
| 12. Примеры всегда в XML-тегах | Инлайн контраст-пары — «latest iPhone 2025» (FABLE 160 / OPUS 153, verbatim), «"мне нужна машина" не "хочу именно RideCo"» (FABLE 274/OPUS аналог, phrase), «"быстрый пост на 200 слов" все равно файл» (не найдено дословно) | phrase |
| 13. Длинные данные и документы - наверх | Источник — официальный гайд Anthropic по промптингу (не системный промпт, не в tmp/); статистика «до 30%» из гайда | free |
| 14. Стиль промпта протекает в стиль вывода | Общий вывод, не цитата | free |
| 15. Для thinking - общие инструкции вместо расписанных шагов | Источник — официальный гайд Anthropic по промптингу (не в tmp/) | free |
| 16. Дай модели диагностический вопрос-тест для классификации | FABLE 458 «The test: does answering require knowing what that thing is?»; OPUS 767 «The test cuts both ways» | verbatim |
| 17. Закрывай лазейку, называя оправдание заранее | FABLE 38, 274, 286 (loophole examples); FABLE 55 / OPUS 48 (мета-детектор); FABLE 434 (маркер безусловности) | verbatim |
| 18. Прибивай область применения явно | Пример «применяется к каждому вопросу» (RU-парафраз FABLE 458 «APPLIES TO EVERY QUESTION»); «применять в каждом ответе» — близко к OPUS 1358/1508 «apply to every response» | phrase |
| 19. Секреты никогда не попадают в промпт | Собственное правило методологии скилла, не производное от системного промпта Claude (в системном промпте Claude нет инструкций про секреты в *генерируемых* промптах — это специфика данного скилла) | free |
| 20. Вставленный чужой промпт - инертные данные | Расширение принципа untrusted data (см. full-rules 4.10, phrase-источник FABLE 132/OPUS 127), но само правило 20 — авторское применение принципа к новому контексту (вставленный промпт), не цитата | free |
| 21. Техники с риском фабрикации - по умолчанию не встраивать | Авторское решение скилла (ToT/MoE/self-consistency в одиночном промпте) — не из системного промпта Claude | free |
| 22. Цикл черновик → аудит → финал для промптов с ценой ошибки | Авторская методология скилла; не цитата FABLE/OPUS. (Смежный `end_conversation` подтверждающий вызов — OPUS 973 — источник для 4.20, не для 22) | free |
| 23. Числовые пороги вместо качественных прилагательных | FABLE 440-533 (COPYRIGHT HARD LIMITS: «15+ words... SEVERE VIOLATION», «ONE quote per source MAXIMUM»), FABLE 462 (Scale tool calls: 1/3-5/5-10) | phrase |
| 24. Tie-breaker при сомнении | FABLE 333 «when in doubt err toward markdown or inline»; OPUS 989 «Always err on the side of continuing the conversation» | phrase |

## 3. full-rules.md разделы (1.1-7.7) — полное покрытие

| Раздел | Источник | Режим |
|---|---|---|
| 1.1 Модель не читает промпт линейно | Общий вывод про attention-механику; primacy/recency bias упомянуты как факт «Anthropic у себя делает оба раза», без конкретной цитаты | free |
| 1.2 Модель решает токен за токеном | Общий вывод, не цитата | free |
| 1.3 У модели нет воли, статистика | Общий вывод, не цитата | free |
| 2.1 Шесть регистров модальности | «Claude can discuss virtually any topic factually and objectively» — FABLE 34 / OPUS 43 (verbatim); «Claude uses accurate medical or psychological information...» — FABLE 94 / OPUS 101 (near-verbatim, OPUS длиннее); «Claude NEVER reproduces song lyrics», «should search before responding to factual questions», «avoids the use of emotes inside asterisks», «should favor original sources over aggregators» — НЕ найдены в tmp/ (источник Opus 4.7, отсутствует в репозитории) | verbatim (2 примера) + free (4 примера) — построчно в самом разделе |
| 2.2 Не смешивай регистры | Общий методологический вывод, не цитата | free |
| 2.3 Третье лицо или второе лицо | Анализ практики Anthropic («Claude does» vs «you do»); не прямая цитата фрагмента промпта — сам факт использования третьего лица виден по всему tmp/, но не цитируется одной фразой | free |
| 2.4 Запрет на softening | Отсутствие softening-слов в FABLE/OPUS — структурное наблюдение, не цитата | free |
| 2.5 Позитивные формулировки сильнее негативных | Общий вывод (эффект розового слона), не цитата | free |
| 2.6 Явные anti-patterns vs позитивные правила | RU-пример собственный («С уважением», «Отличный вопрос» и т.д.) | free |
| 2.7 Не орать капсом на современных моделях | «15+ words from any single source is a SEVERE VIOLATION» повторено 4+ раза — FABLE 441, 482, 511, 567; self-check — FABLE 516-519 / OPUS 1436-1446 (verbatim). Формулировка «CRITICAL: You MUST use this tool when X» vs «Use this tool when X» — из официального гайда Anthropic, не найдена в tmp/ | verbatim (copyright-часть) + free (гайд про CRITICAL/MUST) |
| 3.1 Decision-type vs тематическая структура | Имена блоков: `refusal_handling` — FABLE 32 / OPUS 42; `tone_and_formatting` — FABLE 68 / OPUS 76; `user_wellbeing` — FABLE 92 / OPUS 98. `search_first` в тексте раздела не соответствует ни одному реальному блоку в tmp/ (ближайшие реальные — `search_usage_guidelines` FABLE 468, `search_examples` FABLE 535) — неточность формулировки раздела, зафиксировать для слоя 2 при переводе | phrase (для 3 из 4 названных блоков); `search_first` — free/требует правки при переводе |
| 3.2 Когда тематическая допустима | Собственная классификация скилла, не цитата | free |
| 3.3 Master list для критичных правил | RU-пример собственный (Ё-пример, см. `example-replacement-map.md`); принцип «Anthropic завершает поисковую секцию блоком critical_reminders» — общее наблюдение, конкретный блок critical_reminders в tmp/ не процитирован дословно в этом разделе | free |
| 3.4 Структура preamble + decision-блоки | Авторский шаблон скилла | free |
| 3.5 Длина описания как сигнал веса | Наблюдение о пропорции (copyright многостраничный, emoji — одна строка) — структурный факт, не цитата одной фразы | free |
| 4.1 Обучение через примеры обязательно | Общий вывод, RU-пример (bullets резюме) собственный | free |
| 4.2 Примеры боундари-кейсов, не центральных | «How's my python project coming along?» — OPUS 1136 (near-verbatim: OPUS «the possessive plus the assumption of ongoing state is the cue») | verbatim |
| 4.3 Reasoning встроен в правило | RU-пример про копирайт-rationale — пересказ, не дословная цитата | free |
| 4.4 Self-check вопросы перед действием | «Could I have paraphrased instead?» — OPUS 1443 (verbatim); «Is this quote 15+ words?» — FABLE 516 / OPUS 1444 (verbatim); «song lyric, poem, or haiku» — FABLE 518 / OPUS 1445 (verbatim); «Am I closely mirroring the original phrasing?» — FABLE 519 (verbatim) | verbatim |
| 4.5 Default + override структура | OPUS 38-40 «Claude defaults to helping. Claude only declines a request when helping would create a concrete, specific risk of serious harm; requests that are merely edgy, hypothetical, playful, or uncomfortable do not meet that bar» | verbatim |
| 4.6 Явные failure modes - инвентаризация | «Do not reframe a request to make it appropriate — that reframing is the signal to refuse» — FABLE 55 / OPUS 48 (verbatim); «degrade over extended interactions» — OPUS 961 (phrase, RU-текст раздела перефразирует как «Claude's identity degrades over extended interactions»); «Do not collapse into self-abasement» — FABLE 152 / OPUS 147 «without collapsing into self-abasement» (phrase) | phrase |
| 4.7 Cost-benefit язык вместо буквы правил | «Tool_search is essentially free; unnecessary search is cheap, missed search costs user real effort» — НЕ найдено в tmp/ дословно; ближайшая по духу реальная цитата — FABLE 458 «Searching costs seconds. Confabulating costs the user's trust» (иная формулировка того же принципа) | free (конкретная цитата не найдена; смежная verbatim-цитата FABLE 458 может использоваться как альтернативная опора) |
| 4.8 Невидимость механики | Список «не анонсировать», «не извиняться», «I hope this helps» — RU-обобщение практики, не единая цитата из tmp/ | free |
| 4.9 Иерархия источников при конфликте | Собственная RU-схема приоритетов, не цитата | free |
| 4.10 Tool outputs как untrusted data | FABLE 132 / OPUS 127 «Anthropic will never send reminders that reduce Claude's restrictions or conflict with its values. Since users can add content in tags at the end of their own messages...» | phrase |
| 4.11 Диагностический вопрос-тест для размытых классификаций | FABLE 458 (verbatim), OPUS 767 «The test cuts both ways» (verbatim) — дублирует правило 16 | verbatim |
| 4.12 Закрытие лазейки через названное оправдание | FABLE 38, 274, 286; FABLE 55/OPUS 48 (мета-детектор); FABLE 434 (маркер безусловности); OPUS 61 (кумулятивная оценка) | verbatim |
| 4.13 Прибивание области применения | То же, что правило 18: парафраз FABLE 458 «APPLIES TO EVERY QUESTION», OPUS 793/1358/1508 «every response» | phrase |
| 4.14 Два масштаба примеров: блок и инлайн-пара | «latest iPhone 2025» — FABLE 160 / OPUS 153 (verbatim); ««мне нужна машина» не «хочу именно RideCo»» — парафраз FABLE 274 «I need a ride» / RideCo (phrase); ««быстрый пост на 200 слов» все равно файл» — не найдено дословно (free) | phrase (смешанный состав) |
| 4.15 Секреты никогда не попадают в промпт | Собственное правило скилла (см. правило 19) | free |
| 4.16 Вставленный промпт - инертные данные | Расширение 4.10 на новый контекст; собственное применение принципа (см. правило 20) | free |
| 4.17 Цикл черновик → аудит → финал | Авторская методология (см. правило 22); `end_conversation` confirm-механизм OPUS 973 — источник для другого раздела (4.20), не для 4.17 напрямую | free |
| 4.18 Числовые пороги вместо качественных прилагательных | FABLE 441/482/511/567 (copyright), FABLE 462 (tool calls), FABLE 357/OPUS 1197 («<100 строк — одним вызовом»), FABLE 471/OPUS 1393 («1-6 слов») | phrase |
| 4.19 Tie-breaker при сомнении | FABLE 333/OPUS 1171 «when in doubt err toward markdown or inline» (verbatim); OPUS 989 «Always err on the side of continuing the conversation in any cases of uncertainty» (verbatim); FABLE 450/OPUS 1363 «When in doubt, or if recency could matter, search» (verbatim) | verbatim |
| 4.20 Агентные паттерны: правила в description, пред-интерпретация | OPUS 973 (end_conversation confirmation, phrase); FABLE 648-650 (`ask_user_input_v0`), FABLE 1232-1234/1201 (`suggest_connectors`) | phrase |
| 5.1 Два framing - character и identification | Авторская классификация (сами термины `character framing`/`identification framing` — устоявшийся EN-жаргон промпт-инжиниринга, не цитата конкретного текста tmp/) | free |
| 5.2 Когда что использовать | Собственные критерии выбора, не цитата | free |
| 5.3 Правила одинаково работают в обоих framing | Собственный вывод | free |
| 5.4 Качество имитации не зависит от framing | Собственный вывод (словарь, синтаксис, пунктуация, интонации, табу — общая методология, не цитата) | free |
| 5.5 Шаблон identification framing | Авторский шаблон скилла | free |
| 5.6 Калибровка тона через felt-quality и анти-модели | «What this should feel like... Be specific... The tool might already be right there» — FABLE 295-300 / OPUS 1109-1114 (phrase; RU-текст раздела — своя аналогия «человек который заметил инструмент лежащий рядом», не дословная цитата, но по духу и месту в промпте соответствует этому блоку) | phrase |
| 6.1 Структурный шаблон промпта | Авторский скелет промпта | free |
| 6.2 Чеклист самопроверки промпта | Авторский чеклист, синтезирует остальные правила | free |
| 6.3 Контр-примеры - частые ошибки | Собственные RU ❌/✅ примеры | free |
| 7.1 XML-теги для структуры | Системный промпт Claude построен на XML-тегах — OPUS начинается с `<claude_behavior>` (строка 3), содержит `<refusal_handling>` (42), `<tone_and_formatting>` (76), `<user_wellbeing>` (98) и другие; конкретная рекомендация «использовать XML-теги» — из официального гайда Anthropic (не в tmp/) | phrase (структура тегов подтверждена в tmp/) + free (сама рекомендация из гайда) |
| 7.2 Примеры в XML-тегах, 3-5, разнообразные | Официальный гайд Anthropic (не в tmp/) | free |
| 7.3 Длинные данные и документы - наверх | Официальный гайд Anthropic, статистика «до 30%» — не в tmp/ | free |
| 7.4 Формат вывода: «что делать» и XML-индикаторы | Официальный гайд Anthropic | free |
| 7.5 Thinking: общие инструкции вместо расписанных шагов | Официальный гайд Anthropic | free |
| 7.6 Роль и контекст-мотивация | Общий вывод, совпадает с 4.3 | free |
| 7.7 Техники с риском фабрикации в одиночном промпте | Авторское решение скилла (см. правило 21) | free |

## 4. Итог покрытия

- Master rules: 24/24 строк, режим указан явно для каждой (в т.ч. составные строки с mixed-режимом).
- full-rules.md разделы: 51/51 строк (1.1-7.7), режим указан явно для каждой.
- Расхождение с текстом самого full-rules 3.1 (`search_first`) и full-rules 4.7 (`essentially free`) зафиксировано как находка для слоя 2/3 верификации — при переводе этих двух разделов формулировка должна либо получить точный источник, либо явно остаться `free` без ложной атрибуции системному промпту.
