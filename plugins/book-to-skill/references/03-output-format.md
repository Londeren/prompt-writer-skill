# How to assemble the artifact

Read this sheet before phase 3. It holds the shape of a unit inside a sheet, the templates, and the size rules.

<unit_format>

## How to write a unit into a reference sheet

A format of five fields, proven in practice. Every unit looks like this:

```markdown
#### 1.4. Write about the reader, not about yourself
- **Rule:** the subject and the topic of the key sentences is the reader and the reader's situation, not the author, the company, or the product.
- Why it works: the reader is interested in themselves and their problem, and scrolls past a text about the author.
- Bad → Good: "We provide a personal specialist and develop a strategy" → "Your specialist walks you through every mistake until you get a result".
- When not to apply: <the author's caveat> or the line "No special caveats in the source".
- Anchor: «Подлежащим ключевой фразы должен быть читатель, а не компания.» (гл. 3, раздел «Подлежащее»)
```

The anchor stays in the language of the source, whatever language the skill is written in. The one above comes from a Russian source; in English it reads "The subject of the key sentence has to be the reader, not the company."

Why these five fields:

- **Rule** is checkable, a yes or no verdict can be delivered on a concrete piece of work by it.
- **Why it works** lets the agent apply the rule in a situation the source never covered, instead of following it blindly.
- **Bad → Good** gives a foothold, without which the rule collapses into a slogan. Where the source carries no example for a rule, put in the explicit line "No example in the source" instead of composing one: an invented example is writing on the author's behalf, and the same explicit line that covers a missing caveat covers a missing example.
- **When not to apply** is the most expensive field. The explicit line "no caveats in the source" is mandatory where there are none. An empty field is indistinguishable from a forgotten one, and an explicit line records that the source was checked.
- **Anchor** makes the unit self-sufficient: a verbatim quote with an address, the ground already inside, and the consumer of the skill needs neither a search over the source nor the source itself at hand. The anchor stays in the language of the source. If the skill is going to be published beyond the user's team, trim the quotes down to addresses: verbatim chunks of a book are not handed outside. Trimming is the last step and it runs on a finished artifact. Assembly and the self-check run on full quotes: the audit checks anchors by mechanical search over the source, and once the quotes are gone there is nothing left to search for.

Numbering runs through each sheet, `NN.M`. It is there so that SKILL.md and the agent's answers can point at a specific rule rather than at "a principle from the book".

</unit_format>

<skill_template>

## The SKILL.md template

```markdown
---
name: <short name in lowercase latin>
description: <what it does and when to apply it, with explicit user trigger phrases>
---

# <The name of the method>

<One paragraph: what the skill does and to what material it applies. If there is a
neighbouring skill, the fork to it goes here.>

## The core of the method (hold in mind at all times)

<5-8 numbered principles, each a bold thesis plus one or two sentences of
unpacking. This is the front-load, the most valuable thing in the skill.>

## The order of work

### 1. Diagnosis
### 2. <Frame or preparation>
### 3. <The main work>
### N. <The mandatory final check>

<Each phase with links to the reference sheets it needs.>

## Routing by task

| Task | Sheets |
|---|---|
| <a typical user request> | 02, 03 + the core |

<Below it, the line: read ONLY the sheets the task needs.>

## Output rules

<How the agent hands over the result: explain by a rule and not by taste; before →
after on fragments; the marker [to clarify: ...] instead of invention; tone.>

## Build provenance

<Sources with paths and export dates, priority tiers where there are several
sources, the build date, the statistics: extracted / rejected / included. A
disputed unit is re-checked against the source while the local export is alive;
once the export is gone there is nothing left to revalidate against, and that
limitation is recorded here honestly.>

## Precedent

<Optional: a link to a real application and where its materials live.>
```

Do not rearrange the blocks. The core comes first: the start of the file gets the most attention, and on a partial read that is exactly what the agent sees.

</skill_template>

<splitting>

## How to split material across sheets

Split by **user tasks**, not by source chapters. A book's table of contents is optimized for linear reading, a skill for targeted access.

The sign of a correct split: by the routing table a typical request opens one or two sheets, not five.

The last-numbered sheet is always `NN-checklist-and-antipatterns.md`: the checklist for checking finished work plus the whole catch of extractor D. It is used in two modes, as the final check and as a diagnosis of someone else's material, and is therefore needed more often than the rest.

</splitting>

<sizing>

## What size the files should be

| File | Target size | Ceiling |
|---|---|---|
| SKILL.md | 100-200 lines | 500 lines |
| Reference sheet | 150-250 lines | 300 lines, split by topic beyond that |
| The core of the method | 5-8 principles | 10, beyond that it is not a core |

A sheet longer than 300 lines needs a table of contents at the top. Better to split it than to give it one.

</sizing>

<naming>

## How to name the skill and the sheets

The folder and the `name` field: latin, lowercase, hyphens. Name it after the method or the book, not after the action: `yasno-ponyatno`, not `text-improver`. A method is recognizable, an action is not.

Sheets carry a numeric prefix for ordering: `01-context.md`, `02-text.md`.

</naming>

<description>

## How to write the frontmatter description

The description is the only triggering mechanism. The agent sees the name and the description alone, and reads the body only after it has decided.

Requirements:
- what it does and when to apply it, both in the description and not in the body;
- explicit user trigger phrases, the ones where the method is not named included;
- the description has to be insistent. The default behaviour is undertriggering, an agent tends not to open a skill where it would help.

Weak: "A method of working with text from the book N".
Strong: "Method N for Russian texts: rewrite something clearly, audit its clarity, design its structure. Triggers: 'by N', 'make this clearer', 'rewrite without the fluff', 'why is this text not working', 'structure this text'."

Technical bounds: a description up to 1024 characters, third person, the skill name in lowercase latin with hyphens. Do not retell the skill's process in the description: an agent that sees the process in the description executes the description instead of reading the body. What the skill does, as one noun phrase; after that, triggers only.

A description is not chosen by eye. The tuning procedure for triggering is in the sheet `04-evals.md`.

</description>

<anti_patterns>

## What must not appear in the output

- Raw chunks of the source longer than three sentences.
- Blocks of the form "in chapter 5 the author explains", that is a reference guide, not a skill.
- Rules carrying neither an example nor the explicit line about its absence in the source.
- An empty "when not to apply" field instead of an explicit line about the absence of caveats.
- Horizontal rules between blocks.

</anti_patterns>
