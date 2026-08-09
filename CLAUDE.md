# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

The Claude skill `prompt-writer` is a methodology for writing prompts for LLMs, based on close reading of the Claude system prompts (Opus 4.7, Fable 5, Opus 5) and on attention mechanics. The repository consists only of markdown: no build, no tests, no dependencies. All content is in English, except the frontmatter `description` in SKILL.md, which mixes English and Russian on purpose: English triggers the skill for English requests, and the Russian trigger phrases are there for cross-language triggering.

**Project goal:** publish the skill on the Claude Plugins Store / marketplace. Work on the repository should move it toward a publishable state.

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

## Known publication blockers

- SKILL.md references claude.ai-environment tools: `present_files`, `ask_user_input_v0`, `view`. Claude Code has no equivalents (the closest are writing a file, AskUserQuestion, Read). For publication, the wording needs to become platform-neutral or offer alternatives.
- The plugin manifests already exist (`.claude-plugin/plugin.json`, `.claude-plugin/marketplace.json`). The remaining packaging step, moving the skill files into the plugin directory the marketplace manifest points to, is tracked in `docs/superpowers/plans/2026-08-09-plugin-packaging.md`. Check the current format against the official Claude Code plugins documentation, not from memory.

## Checking changes

There are no automated tests. The smoke test is to activate the skill on a real request ("write a prompt for...") and confirm that routing picks the right type, the template loads, and the result passes self-check. For a systematic quality run there is the user's `autoresearch` skill (iterative optimization of the skill against evals).
