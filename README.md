# Prompt Writer

> A Claude skill that turns "write me a prompt" into an engineered prompt: routing by task type, modality registers, XML-tagged examples, and a final self-check. Skill content is in Russian; the prompts it produces follow whatever language your task needs.
>
> **Quick start (EN):** for Claude Code — `git clone` this repo into `~/.claude/skills/prompt-writer`. For claude.ai — zip a `prompt-writer/` folder containing `SKILL.md` and upload it at [Customize → Skills](https://claude.ai/customize/skills) (requires "Code execution and file creation" enabled in Settings → Capabilities; available on all plans, including Free). The skill auto-triggers on prompt-writing requests in any language; vague requests get 2-3 clarifying questions first.

Skill для Claude, который превращает «напиши промпт для …» в инженерно выстроенный промпт. Методология основана на разборе системного промпта Claude Opus 4.7 и того, как модель на самом деле использует промпт — не линейным чтением, а attention-механикой на каждом токене вывода.

## Что делает

Когда вы просите Claude написать или улучшить промпт, скилл:

1. Классифицирует задачу в один из четырех типов промпта.
2. Ведет по шаблону этого типа — со структурой, проверенной на реальных системных промптах.
3. Применяет 22 master-правила (регистры модальности, decision-type структура, примеры, XML-теги, размещение данных).
4. Прогоняет результат через цикл черновик → аудит → финал: чеклист, диагностические вопросы, переписывание закрывающее найденное.

## Четыре типа промптов

| Тип | Для чего | Ключевая особенность |
|---|---|---|
| **A — Character assistant** | Долгоживущий ассистент от лица компании или роли: саппорт-бот, internal knowledge assistant, онбординг-бот | Descriptive third person: «Ассистент делает X» — устойчивее к манипуляциям, чем «ты» или «я» |
| **B — Person imitation** | Имитация конкретного человека: бот-двойник основателя, ghost-writer постов | Identification framing («Ты [Имя]») + обязательные 15-25 реальных примеров сообщений |
| **C — One-shot task** | Одноразовая линейная задача: суммаризация, перевод, конкретный текст | Короткий императивный промпт без character и лишней структуры |
| **D — Extraction / transformation** | Многоразовый промпт по методологии: извлечение инсайтов, генерация по бренд-гайду, grading | Decision-блоки по структуре деливерабла, входные документы наверху в `<document>` тегах |

## Ключевые принципы методологии

- **Decision-type структура вместо тематической.** Блоки промпта отвечают на вопросы, которые модель задает себе в момент генерации («когда делать X», «как выбрать между A и B»), а не описывают темы («Про продукт»).
- **Регистры модальности.** Осознанная иерархия силы инструкций: descriptive third person для identity, NEVER/ALWAYS только для реальных hard limits, should/can/avoids/prefers для остального. Без капса и MUST — на современных моделях они вызывают overtriggering.
- **Примеры как часть правила.** 3-5 примеров в `<example>` тегах на каждое сложное правило; граничные случаи важнее центральных.
- **Дублирование критичных правил.** То, что в коде антипаттерн (DRY), в промпте паттерн: attention весит близость правила к месту применения сильнее, чем эмфазис.
- **Данные наверх, запрос вниз.** Крупные входные документы — в начало промпта, выше инструкций: до 30% к качеству на длинном контексте.

Полный свод правил с reasoning — в [reference/full-rules.md](reference/full-rules.md), детальный разбор шести регистров модальности — в [reference/modal-registers.md](reference/modal-registers.md).

## Установка

### Claude Code

Склонировать в папку персональных скиллов (имя папки должно совпадать с `name` из frontmatter — `prompt-writer`):

```bash
git clone https://github.com/londeren/prompt-writer-skill.git ~/.claude/skills/prompt-writer
```

Или в скиллы конкретного проекта: `.claude/skills/prompt-writer/`.

Установка из плагин-маркетплейса (`/plugin install`) — в планах; пока скилл ставится вручную.

### claude.ai

Работает на всех планах, включая Free. Предварительно включить «Code execution and file creation» в Settings → Capabilities.

1. Собрать zip, в корне которого лежит папка `prompt-writer/` с `SKILL.md` внутри (файлы прямо в корне архива не пройдут валидацию):

   ```bash
   mkdir prompt-writer && cp -r SKILL.md templates reference checklists prompt-writer/
   zip -r prompt-writer.zip prompt-writer
   ```

2. Загрузить архив на странице [Customize → Skills](https://claude.ai/customize/skills): «Create skill» → «Upload a skill».

## Использование

Скилл активируется сам на запросах вида:

- «Напиши промпт для саппорт-бота нашего продукта»
- «Сделай инструкцию для Claude Project, который пишет посты за меня»
- «Улучши этот системный промпт» + текст промпта
- "Set up an assistant that answers questions about our docs"

Если задача описана детально — скилл сразу пишет промпт, без лишних вопросов. Если размыто — задаст 2-3 уточняющих вопроса и предложит выбрать тип.

## Структура скилла

```
SKILL.md                        — точка входа: routing, master-правила, процесс
templates/
  character-frame.md            — шаблон для типа A
  identification-frame.md       — шаблон для типа B
  one-shot-task.md              — шаблон для типа C
  extraction-prompt.md          — шаблон для типа D
reference/
  full-rules.md                 — полный свод правил с reasoning
  modal-registers.md            — шесть регистров модальности детально
checklists/
  self-check.md                 — чеклист и вопросы для этапа аудита
```

При активации загружается только SKILL.md; шаблоны и справочники Claude подгружает по мере необходимости (progressive disclosure).

## Лицензия

[MIT](LICENSE)
