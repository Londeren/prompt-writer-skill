# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

The Claude skill `prompt-writer` is a methodology for writing prompts for LLMs, based on close reading of the Claude system prompts (Opus 4.7, Fable 5, Opus 5) and on attention mechanics. The repository consists only of markdown: no build, no tests, no dependencies. All content is in English, except the frontmatter `description` in SKILL.md, which mixes English and Russian on purpose: English triggers the skill for English requests, and the Russian trigger phrases are there for cross-language triggering.

The repository is also a plugin marketplace: `.claude-plugin/marketplace.json` at the root points at `plugins/prompt-writer/`, which is the plugin itself and the only part that ships to users. Every skill path in this file, `SKILL.md`, `reference/`, `templates/`, `checklists/`, is relative to that directory.

**Project goal:** keep the skill's signal-to-noise high while maintaining the publication channels. Publication itself is live; work on the repository should sharpen what the skill generates rather than enlarge it.

Convention: the canonical running example of a hard style rule throughout the skill is the em dash ban ("never uses em dashes"). Meta-documents follow the same style: no em dashes in prose.

The `tmp/` directory (in .gitignore) holds local source transcripts of system prompts; specs under `docs/superpowers/specs/` reference them by line number. They are not checked into the repository; when working on specs, get the files from Sergey if they are not present locally.

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

Deferred and rejected insights go to docs/insights-backlog.md with source and reason.

## Publication status

Two distribution channels are live and need no approval: the GitHub plugin marketplace and the skills.sh CLI. README.md owns every install route; do not duplicate the commands here.

Note on the marketplace name: for a GitHub `owner/repo` source, Claude Code registers the marketplace under the repository owner, `Londeren`, not under the `name` field of marketplace.json. A lowercase name in the manifest produced a marketplace the install command could not find. Keep the two in sync.

Not done yet: the submission to the Anthropic community catalog, through a web form at https://platform.claude.com/plugins/submit (individual authors) or https://claude.ai/admin-settings/directory/submissions/plugins/new (requires a Team or Enterprise organization). Pull requests against anthropics/claude-plugins-community are closed automatically; the review pipeline runs `claude plugin validate` plus automated safety screening. The official catalog, claude-plugins-official, is curated by Anthropic at its own discretion and has no application process.

When editing the skill text, keep it platform neutral: present_files, ask_user_input_v0 and the view tool exist only in claude.ai. The one place that names a tool on purpose is the type question in SKILL.md, which names both AskUserQuestion and ask_user_input_v0. The ask_user_input_v0 mention in templates/agentic-task.md is an example of tool-description writing, not a call, and must survive any search and replace.

## Checking changes

There are no automated tests. The smoke test is to activate the skill on a real request ("write a prompt for...") and confirm that routing picks the right type, the template loads, and the result passes self-check. For a systematic quality run there is the user's `autoresearch` skill (iterative optimization of the skill against evals).

Manifests and packaging are checked with commands, not by eye:

```bash
claude plugin validate .
claude plugin validate ./plugins/prompt-writer --strict
claude --plugin-dir ./plugins/prompt-writer -p "..." --max-turns 1
find plugins/prompt-writer -type f | sort
```

The last one guards the packaging boundary: nothing from `docs/` or `tmp/` may appear in that listing.
