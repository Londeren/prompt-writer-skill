# prompt-writer: editing rules

Rules for editing the `prompt-writer` plugin, imported by the root [CLAUDE.md](../CLAUDE.md), which also holds the repository-wide conventions (packaging boundary, docs, publication, validation commands). The file lives in `docs/` rather than inside the plugin because everything under `plugins/<name>/` ships to users.

Every path below is relative to `plugins/prompt-writer/`.

## What this is

The Claude skill `prompt-writer` is a methodology for writing prompts for LLMs, based on close reading of the Claude system prompts (Opus 4.7, Fable 5, Opus 5) and on attention mechanics. All content is in English, except the frontmatter `description` in SKILL.md, which mixes English and Russian on purpose: English triggers the skill for English requests, and the Russian trigger phrases are there for cross-language triggering.

**Goal:** keep the skill's signal-to-noise high while maintaining the publication channels. Publication itself is live; work here should sharpen what the skill generates rather than enlarge it.

Convention: the canonical running example of a hard style rule throughout the skill is the em dash ban ("never uses em dashes").

## Architecture: progressive disclosure

`SKILL.md` is the only file loaded when the skill activates. Everything else is pulled in by the model as needed while it works:

- **Routing** in SKILL.md classifies a request into one of five prompt types (A character assistant, B person imitation, C one-shot task, D extraction/transformation, E agentic task), each pointing to its template in `templates/`.
- `reference/full-rules.md` holds the full rule set with reasoning; `reference/modal-registers.md` covers the six modality registers in detail. SKILL.md carries a compressed version of both (Master rules + Quick reference).
- `checklists/self-check.md` is the checklist for the audit step.

## Intentional duplication of rules

The same rules exist at six levels of detail: Master rules and Quick reference in SKILL.md, the full versions in reference/full-rules.md, its summary sections §6.1 (structural template) and §6.2 (checklist), the checklist items in checklists/self-check.md, their embodiment in templates/, and the storefront in README.md (the rule count, the type table, the file tree). This is not an accident: the skill itself teaches that duplicating critical rules in a prompt is a pattern, not an anti-pattern.

Consequence for editing: when a rule changes or a new one is added, sync every layer, the rule itself in reference, its digest and number in Master rules / Quick reference in SKILL.md, the matching checklist item, the affected templates, and the summary layers, README.md and full-rules §6.1/§6.2. Numbering of the Master rules in SKILL.md runs continuously and is cross-referenced elsewhere ("details in rule 12"); check those references when inserting a rule in the middle.

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

Deferred and rejected insights go to `docs/insights-backlog.md` with source and reason.

## Named tools

The skill text stays platform neutral, with two deliberate exceptions that must survive any search and replace: the type question in SKILL.md names both AskUserQuestion and ask_user_input_v0, and the `ask_user_input_v0` mention in `templates/agentic-task.md` is an example of tool-description writing, not a call.

## Checking changes

The smoke test is to activate the skill on a real request ("write a prompt for...") and confirm that routing picks the right type, the template loads, and the result passes self-check. For a systematic quality run there is the user's `autoresearch` skill (iterative optimization of the skill against evals).
