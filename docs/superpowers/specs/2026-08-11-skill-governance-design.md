# Design: управление изменениями скилла (governance)

Дата: 2026-08-11
Статус: draft, ждет ревью
Контекст: разбор промпта Sol (`tmp/5.6-Sol_SystemPrompt.md`) предложил три новых мастер-правила и ни одного удаления. Сергей поставил вопрос: рост скилла надо сделать управляемым, а изменения проверяемыми без чтения всего текста.

## Проблема

Каждый разбор нового источника (Opus 4.7, prompting best practices, Fable 5, Opus 5, Sol) добавлял правила и ничего не удалял. Причины храповика: допуском управляет "интересный инсайт", а не названная потеря; давления на удаление нет; Сергей не может проверять изменения чтением полного текста (24 мастер-правила, ~2300 строк скилла, шесть слоев синхронизации).

Проблема не в размере файлов как таковом. Проблема в том, что добавление может дать больше шума, чем пользы, и ничто в процессе не заставляет ответить на этот вопрос до интеграции.

## Философия

1. Ответ по умолчанию на любой новый инсайт: нет. Инсайт входит только с сильным доводом: назвать, что теряет сгенерированный промпт без него. "Интересно" не довод; названная потеря довод.
2. Критерий: сигнал против шума с учетом всего скилла, не размер файла и не бюджеты строк. Заморозки нет: скилл обязан уметь эволюционировать, в том числе уменьшаясь.
3. Интеграция органическая, никогда не append-only. Интегрировать батч значит перечитать весь скилл и решить, во что новое сливается, что оно переписывает, что удаляет. Удаление легитимно само по себе: источник может доказать, что существующее правило неверно или избыточно, и тогда ничего не добавляется взамен.
4. Скилл редактируется собственными правилами: правка скилла это его же сценарий "improve an existing prompt", примененный к себе, включая аудит по self-check. Governance-документы ссылаются на правила скилла, а не пересказывают их.

## Констрейнты

1. Без буквы ё и без длинных тире, сквозная конвенция репозитория.
2. Секция в CLAUDE.md на английском, как весь файл. Спека и backlog на русском, как рабочие документы docs/.
3. Контент самого скилла (plugins/) в этой работе не трогается.

## Артефакты

### 1. Новая секция "Changing the skill" в CLAUDE.md

Место: после "Intentional duplication of rules", перед "Publication status". Точный текст:

```markdown
## Changing the skill

The default answer to any new insight is no. An insight enters only with a strong
argument: name what a generated prompt loses without it. Interesting is not an
argument; a named loss is. The criterion is signal versus noise judged against
the whole skill, not file size.

Integration is organic, never append-only. Before integrating anything, re-read
the whole skill end to end and decide what the new material merges into, what it
rewrites, what it deletes. Deletion is legitimate on its own: a source can prove
one of our rules wrong or redundant, with nothing added in return.

The skill is edited by its own rules: a skill change is the skill's own "improve
an existing prompt" scenario applied to itself, self-check audit included. The
rules are not restated here; they are followed.

Placement ladder, cheapest slot that closes the loss: backlog → a line inside an
existing rule → a checklist item → one template → a full-rules subsection → a new
master rule. A new master rule requires a red-case: a real request where the
current skill produces the defect the rule fixes.

Protocol for an insight batch:
1. An argument table first: insight → loss without it → ladder slot → what gets
   merged, rewritten, or deleted for it to land organically. Sergey approves
   rows, not prose.
2. After integration, a fresh-context subagent reads the entire skill blind and
   names what it would cut. New material on that list is a failed integration.
3. Smoke A/B: the same request through the skill before and after; the change
   must show up in the generated prompt.
4. Report back: the table with outcomes plus per-file line deltas (observability,
   not a gate).

Deferred and rejected insights go to docs/insights-backlog.md with source and reason.
```

### 2. Чистка CLAUDE.md

- "Publication status" сжимается примерно втрое. Остаются: live-каналы одной строкой (GitHub-маркетплейс, skills.sh), несделанный сабмишен в community catalog с двумя ссылками и заметкой про автозакрытие PR, gotcha про имя маркетплейса (регистрируется по владельцу репозитория). Инструкции по установке уходят из секции: ими владеет README.md.
- "What this is": строка Project goal обновляется. Цель "publish" выполнена; новая цель: держать сигнал/шум скилла высоким при поддержании каналов публикации.
- "Intentional duplication of rules" остается без изменений: механика синхронизации слоев нигде в скилле не описана.
- Остальное в CLAUDE.md без изменений.

### 3. Новый файл docs/insights-backlog.md

Одна строка на инсайт: инсайт, источник (файл и строка или URL), статус (rejected или deferred), причина. Создается пустым: шапка с форматом и таблица без строк. Пункты попадают в него только из прогонов протокола, после таблицы доводов и решения Сергея.

## Детали протокола

Блок в CLAUDE.md сжат; здесь то, что в него не попало.

### Таблица доводов (шаг 1)

Колонки: инсайт / потеря без него (конкретный дефект в сгенерированных промптах) / слот лестницы / что сливается, переписывается или удаляется, чтобы новое легло органично. Сергей утверждает строки; отклоненные уходят в backlog с его причиной. Батч, где не принято ни одной строки, валидный исход, о нем сообщается так же.

### Слепое ревью свежим контекстом (шаг 2)

Субагент с чистым контекстом получает копию `plugins/prompt-writer/` в scratchpad: без git-истории, без диффа, без знания, что менялось. Задание: прочитать скилл целиком и перечислить, что он вычеркнул бы как шум, дубль или противоречие, с указанием файла и места; добавлений не предлагать.

Критерий прохождения: ни один пункт cut-листа не указывает на текст, добавленный или измененный этим батчем. Попадание значит проваленную интеграцию пункта: переделка (плотнее слить, слот ниже, или backlog) и повторное ревью. После двух неудачных переделок пункт уходит в backlog. Вывод ревьюера прикладывается к отчету в любом случае.

Ограничение названо честно: слепое ревью ловит шум, но не ловит упущенную пользу. Ее страхует backlog: отклоненное не пропадает.

### Смоук A/B (шаг 3)

Один-два репрезентативных запроса под затронутые типы промптов. "До": копия `plugins/prompt-writer/` из коммита перед интеграцией (git show в scratchpad). "После": рабочее дерево. Обе версии гоняются через `claude --plugin-dir <dir> -p "<запрос>"`, сгенерированные промпты сравниваются. Изменение обязано проявиться в выводе: новый блок, измененная формулировка, исчезнувший кусок. Нет видимой разницы, значит добавлен текст, а не поведение: откат или переделка, сдавать как есть нельзя.

Autoresearch не часть рутинного протокола; он подключается точечно, когда эффект спорный.

### Отчет (шаг 4)

Состав: таблица доводов с исходом по каждой строке, вердикт слепого ревью, результат смоука, дельты строк по файлам. Дельты это наблюдаемость, не гейт.

## Вне скоупа

- Изменения контента скилла: Sol-кандидаты идут через новый протокол отдельным заходом, начиная с таблицы доводов. Это будет первое применение протокола.
- Эвал-набор не строится, autoresearch остается инструментом по требованию.

## Порядок имплементации

1. Секция "Changing the skill" в CLAUDE.md плюс чистка по списку выше.
2. Создание docs/insights-backlog.md, пустого.
3. Проверка: в новых текстах нет ё вне упоминаний самой буквы, нет длинных тире, нет разделителей; секция CLAUDE.md читается за минуту.
