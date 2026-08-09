# Карта замен языкозависимых примеров (RU → EN, не перевод)

BASE_RU: `5148d66afea0c857e1909797f4a286c36576c529`

Эта карта покрывает Task 1, Step 4 брифа: все места, где RU-версия использует пример, специфичный для русского языка, и который при переводе не переводится, а заменяется языконезависимым эквивалентом с тем же дидактическим свойством (бинарность, механическая проверяемость, легко нарушается по привычке модели). Канонические EN-формулировки — из брифа, дословно. Каждая строка: файл:строка на BASE_RU → RU-текст (кратко) → EN-замена.

## 1. Ё-пример → em dash ban (19 вхождений)

Канонические формулировки по регистрам (используются везде, где текст представляет конкретный регистр):

| Регистр | Каноническая EN-формулировка |
|---|---|
| Descriptive | `The assistant writes without em dashes.` |
| NEVER (пример неправильного усиления) | `The assistant NEVER uses em dashes.` |
| Should | `The assistant should replace em dashes with commas or periods.` |
| Can | `The assistant can keep an em dash when quoting a user's own text verbatim.` |
| Avoids | `The assistant avoids em dashes.` |
| Prefers | `The assistant prefers commas over em dashes for asides.` |
| Master-rules список (было «Русский, без буквы Ё») | `- No em dashes (replace with comma or period)` |

Построчная карта всех 19 вхождений на BASE_RU:

| # | Файл:строка | RU-текст (BASE_RU) | EN-замена |
|---|---|---|---|
| 1 | `SKILL.md:211` | «Правило про букву Ё нужно в трех decision-блоках где модель пишет ответы пользователю - продублировать в каждом.» | «The em dash ban needs to live in three decision blocks where the model writes replies to the user - duplicate it in each.» |
| 2 | `reference/modal-registers.md:21` | «Ассистент общается на русском языке без буквы Ё.» | Descriptive: `The assistant writes without em dashes.` |
| 3 | `reference/modal-registers.md:65` | «Плохо: Ассистент НИКОГДА не использует букву Ё.» | `Bad: The assistant NEVER uses em dashes.` |
| 4 | `reference/modal-registers.md:67` | «descriptive или avoids: «Ассистент пишет без буквы Ё».» | «descriptive or avoids: "The assistant writes without em dashes."» |
| 5 | `reference/modal-registers.md:103` | «Плохо: Ассистент должен НИКОГДА не использовать букву Ё.» | `Bad: The assistant should NEVER use em dashes.` |
| 6 | `reference/modal-registers.md:105-106` | «Выбрать один: либо «Ассистент пишет без Ё», либо «Ассистент НИКОГДА не использует Ё».» | «Choose one: either "The assistant writes without em dashes," or "The assistant NEVER uses em dashes."» |
| 7 | `reference/modal-registers.md:177` | «Плохо: Ассистент избегает использовать букву Ё.» | `Bad: The assistant avoids em dashes.` |
| 8 | `reference/modal-registers.md:179` | «Ассистент пишет без буквы Ё». Avoids оставляет пространство для нарушения…» | «"The assistant writes without em dashes." Avoids leaves room for a violation that isn't needed here.» |
| 9 | `reference/modal-registers.md:206` | «Плохо: Ассистент предпочитает не использовать букву Ё.» | `Bad: The assistant prefers not to use em dashes.` |
| 10 | `reference/modal-registers.md:208` | «ложное впечатление что иногда Ё допустима» | «false impression that em dashes are sometimes acceptable» |
| 11 | `templates/identification-frame.md:27` | «1. [Правило 1 - например, без буквы Ё]» | «1. [Rule 1 - e.g., no em dashes]» |
| 12 | `reference/full-rules.md:212` | «2. Без буквы Ё» (в примере master-блока: 1. Русский / 2. Без буквы Ё / 3. Без AI-филлеров / 4. Без em-dash) | `- No em dashes (replace with comma or period)` — совпадает по смыслу со строкой 4 того же примера («Без em-dash»); при переводе блок схлопывается в один пункт, не два дублирующих |
| 13 | `reference/full-rules.md:842` | «❌ Плохо (правило про букву Ё спрятано в общем разделе про тон, который читается один раз):» | «Bad (the em dash ban is buried in a general tone section that's read once):» |
| 14 | `reference/full-rules.md:847` | «Буква Ё всегда заменяется на Е. Эмодзи используются редко...» | «Em dashes are always replaced with commas or periods. Emojis are used rarely...» |
| 15 | `reference/full-rules.md:859` | «- Русский, без буквы Ё (всегда Е)» | `- No em dashes (replace with comma or period)` |
| 16 | `reference/full-rules.md:866` | «отправкой - нет буквы Ё, нет филлеров.» | «before sending - no em dashes, no filler.» |
| 17 | `reference/full-rules.md:871` | «нет буквы Ё, нет филлеров.» | «no em dashes, no filler.» |
| 18 | `CLAUDE.md:11` | «Конвенция: весь русский текст репозитория… пишется без буквы ё… (его повторяющийся пример правила — «пишет без буквы Ё»)…» | **Удаляется**, не переводится (решение спеки, раздел «Объем перевода»): вместо конвенции про Ё зафиксировать «canonical running example of a hard style rule is the em dash ban» |

Примечание по счету: `grep -c '[Ёё]' ...` дает 19 совпавших строк (`baseline-metrics.md`, раздел 1); таблица выше перечисляет 18 строк-адресов, потому что строка #6 (`reference/modal-registers.md:105-106`) — это два отдельных грепом совпавших физических номера строки (105 и 106), объединенные в одну строку таблицы, так как формируют одно предложение-пример, разорванное переносом. 17 остальных строк таблицы + 2 строки внутри #6 = 19, совпадает с `grep -c`. Отдельно от количества строк — 20 вхождений самой буквы (`grep -o`): на `CLAUDE.md:11` буква встречается дважды в одной строке (строчная «ё» и прописная «Ё»).

`reference/full-rules.md:212` и `:859` при переводе схлопываются с существующей соседней строкой «Без em-dash» / «4. Без em-dash» того же блока-примера (full-rules.md строки 214, 861) — в RU-оригинале блок уже содержит избыточность (Ё-правило и em-dash-правило как два разных пункта одного списка), в EN-версии это одна строка, не две.

## 2. «С уважением / Здравствуйте / Благодарю за обращение» → EN-эквивалент (3 вхождения на BASE_RU)

Каноническая замена: `"Best regards," "Dear Sir/Madam," "Thank you for reaching out"`.

| # | Файл:строка | RU-текст (BASE_RU) |
|---|---|---|
| 1 | `SKILL.md:201` | «не используй: "С уважением", "Здравствуйте", "Благодарю за обращение"» |
| 2 | `reference/full-rules.md:132` | «"Не используй: "С уважением", "Здравствуйте", "Благодарю за обращение"" - работает.» |
| 3 | `checklists/self-check.md:59` | «не используй: "С уважением", "Здравствуйте", "Благодарю"» (укороченная форма третьего пункта — на BASE_RU здесь только «Благодарю», без «за обращение») |

Расхождение со спекой/брифом: бриф ожидает 4 вхождения, включая `templates/identification-frame.md`. Проверено грепом (`grep -in 'уважением\|здравствуйте\|благодарю за обращение\|табу' templates/identification-frame.md`) — на BASE_RU этот файл списка не содержит; в нем есть только плейсхолдер `[Другие табуированные формулировки]` (строка 41) без конкретных фраз вежливости. Зафиксировано как расхождение ожидания-брифа с фактическим состоянием корпуса на BASE_RU, не как пропущенный анкор — четвертого вхождения на BASE_RU не существует.

## 3. Title case пример (правило 18 / full-rules 4.13) → sentence case (2 вхождения)

Каноническая замена (перевернута относительно RU, чтобы не противоречить humanizer-стилистике — `tmp/SKILL humanizer.md` §17 «Title Case in Headings» перечисляет Title Case как AI-tell): `sentence case in every section heading, not only the first`.

| # | Файл:строка | RU-текст (BASE_RU) |
|---|---|---|
| 1 | `SKILL.md:318` | «Пример: вместо «заголовки в title case» - «заголовки в title case в каждой секции, не только в первой».» |
| 2 | `reference/full-rules.md:459` | «Пример: «заголовки в title case в каждой секции, не только в первой».» |

EN-замена для обеих строк: `Example: instead of "sentence case in headings" - "sentence case in every section heading, not only the first."` (структура примера сохранена — было «title case → title case везде», стало «sentence case → sentence case везде», сама демонстрируемая техника правила 18/4.13 — прибивание области — не меняется, только конкретный стилистический пример).

## 4. Пул дополнительных бинарных стиль-примеров (для разнообразия, не привязан к конкретным вхождениям)

Используется там, где в одном блоке сейчас нужно больше одного примера, а сквозной Ё-пример один. Источник — `tmp/SKILL humanizer.md`:

| EN-пример | Источник (tmp/SKILL humanizer.md) |
|---|---|
| `no emojis in headings` | §18 Emojis (строка 208) |
| `sentence case in headings, never Title Case` | §17 Title Case in Headings (строка 201) |
| `straight quotes, not curly quotes` | §19 Curly Quotation Marks (строка 217) |
| `never opens with "Great question!"` | §22 Sycophantic/Servile Tone (строка 248) |
| `never closes with "I hope this helps" or "Would you like me to..."` | §20 Collaborative Communication Artifacts (строка 226) |
| `no rule-of-three constructions` | §10 Rule of Three Overuse (строка 141) |

Применение: везде, где RU-корпус в примере на несколько строк использует несколько Ё-подобных пунктов подряд (например, `reference/full-rules.md:143-148` — блок anti-patterns, где Ё-пример соседствует со списком AI-филлеров — см. §5 ниже), EN-версия чередует основной em-dash пример с 1-2 пунктами из этого пула вместо повтора одного и того же примера.

## 5. Русские анти-паттерн формулы AI-стиля → EN (4 места, включая один составной блок)

Каноническая замена: `"Great question!", "Happy to help!", "I hope this helps", "In conclusion", "Feel free to reach out"`.

| # | Файл:строка(и) | RU-текст (BASE_RU) | EN-замена |
|---|---|---|---|
| 1 | `reference/full-rules.md:140-149` | Составной блок: «Ассистент никогда не использует: "Отличный вопрос" / "Рад помочь" / "Надеюсь помог" / "В заключение" / Em-dash вместо запятой» | `The assistant never uses:` / `"Great question!"` / `"Happy to help!"` / `"I hope this helps"` / `"In conclusion"` / `Em dashes instead of commas` — последний пункт уже совпадает с целевым каноном (см. §1) и при переводе не меняет смысла, только формулировку |
| 2 | `reference/full-rules.md:401` | «не пиши заключений типа "Если есть вопросы - обращайтесь"» | «no closing lines like "Feel free to reach out"» |
| 3 | `templates/identification-frame.md:64-67` | Табу-список: «Отличный вопрос» / «Рад помочь» / «Надеюсь это поможет» (вариант формулировки третьего пункта) / «Если есть вопросы - обращайтесь» | `"Great question!"` / `"Happy to help!"` / `"I hope this helps"` / `"Feel free to reach out"` |
| 4 | `checklists/self-check.md:125` | «Надеюсь помог», «Если есть вопросы - обращайтесь», «В заключение» | `"I hope this helps," "Feel free to reach out," "In conclusion"` |

Примечание: строка `templates/identification-frame.md:66` на BASE_RU использует вариант «Надеюсь это поможет» вместо «Надеюсь помог» из остальных трех мест — смысл идентичен, EN-канон один и тот же (`"I hope this helps"`) для обоих RU-вариантов; отдельной глоссарной записи не требуется, т.к. это не термин методологии, а конкретная фраза-пример.

## 6. Язык-зависимый пример вне карты Ё (default+override)

Найдено при review-проходе по Task 4 (`reference/modal-registers.md`), не входит в грепы Step 1 (не содержит буквы Ё), но остается языкозависимым артефактом того же рода: RU-пример default+override структуры называет конкретный язык ответа ассистента, что читается как остаточный RU-центризм в EN-версии документа на английском.

| # | Файл:строка | RU-текст (BASE_RU) | EN-замена |
|---|---|---|---|
| 1 | `reference/modal-registers.md:235` (RU-нумерация BASE_RU) | «Ассистент отвечает на русском, если только пользователь не пишет на другом языке - в этом случае ассистент переключается на язык пользователя.» | `The assistant replies in English, unless the user writes in another language, in which case the assistant switches to the user's language.` |

Механика примера (default-язык + override на язык пользователя) не меняется, меняется только назван­ный язык, чтобы не противоречить целевому языку самого корпуса.

## 7. Итог покрытия

- Категория 1 (Ё → em dash): 19/19 вхождений грепа Step 1 учтены построчно.
- Категория 2 (политес-формулы): 3 реальных вхождения на BASE_RU (не 4 — расхождение с ожиданием брифа задокументировано, не является пропуском).
- Категория 3 (title case): 2/2 вхождения.
- Категория 4 (пул): не привязана к вхождениям, справочный ресурс с указанием источника по каждому пункту.
- Категория 5 (AI-филлеры): 4 места, включая один составной блок из 5 строк.
- Категория 6 (язык-зависимый default-пример вне грепа Ё): 1 вхождение, найдено post-hoc при review Task 4.
