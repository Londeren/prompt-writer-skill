# Translation verification report

## What was verified

The English translation of the `prompt-writer` skill, from `BASE_RU=5148d66` (the Russian baseline commit fixed at the start of the translation task) through current `HEAD` (`971165b`). Full corpus: `SKILL.md`, `reference/full-rules.md`, `reference/modal-registers.md`, `checklists/self-check.md`, `checklists/input-triage.md`, `templates/*.md`.

Method, five layers run after translation completed:

- **Layer 1 (mechanical invariants).** Structural counters (rule/section/example counts) and corpus-wide scans (Cyrillic, Ё, em dashes, header consistency, frontmatter size) against the RU baseline.
- **Layer 2 (tmp/ anchors).** Every `verbatim`/`phrase` row in `docs/superpowers/translation/reverse-assembly-map.md` traced to its cited line in `tmp/CLAUDE-FABLE-5.md` / `tmp/OPUS-5.md`, to catch fabricated or paraphrased "quotes."
- **Layer 3 (intent maps).** Adversarial section-by-section RU-vs-EN comparison of claim, rationale, examples (incl. boundary cases), register, and scope, run in ten parallel batches over the whole corpus, then re-verified in a pass-2 diff review.
- **Layer 4 (blind back-translation).** A model with no RU access back-translated scoped EN sections; the round-trip was diffed against RU for meaning shifts.

## Layer 1: mechanical invariants

All counters matched baseline: master rules 24, Quick reference 24, `full-rules.md` sections 51, closing principles 40, self-check items 63, §6.2 checklist 26, example-block counts per file. Corpus scans: zero Cyrillic outside `SKILL.md`'s frontmatter `description` and one intentional README example, zero Ё, zero em dashes. Source-attribution headers consistent across files. Frontmatter `description` is 1009 bytes / 900 characters, within the 1024-byte limit. Layer 1 converges, no open items.

## Layer 2: tmp/ anchors

38 rows checked (18 `verbatim`, 20 `phrase`, deduplicated to ~20 distinct quoted anchors), plus a 10-row sample of `free` rows for reverse-idiom checking. **0 flags**: no `verbatim` row paraphrased where an uncited original existed elsewhere, no claimed `tmp/` source failed to resolve, no `phrase` row ignored the original's idiom, no calques in the sampled `free` rows.

One correction: the reverse-assembly map cites "FABLE 274" for the "Urgency is not an exception" / "Speed does not license picking the partner" quotes; the text actually sits at FABLE:275-276. A documentation inaccuracy in the map, not a corpus or translation defect: the quoted text is verbatim and within range.

## Layer 3: intent maps

| Batch | Scope | Units | Pass-1 verdict |
|---|---|---|---|
| A | SKILL.md master rules 1-6 | 6 | 6 match |
| B | SKILL.md master rules 7-12 | 6 | 6 match |
| C | SKILL.md master rules 13-18 | 6 | 6 match |
| D | SKILL.md master rules 19-24 + Quick reference | 10 | 10 match |
| E | full-rules.md §1-3 | 16 | 15 match, 1 mismatch |
| F | full-rules.md §4.1-4.10 | 10 | 10 match |
| G | full-rules.md §4.11-4.20 | 10 | 10 match |
| H | full-rules.md §5-8 + closing note | 20 | 19 match, 1 mismatch |
| I | checklists/self-check.md + input-triage.md | 2 files | all match |
| J1 | templates/character-frame.md + identification-frame.md | 31 | 29 match, 2 mismatch |
| J2 | templates/one-shot-task.md, extraction-prompt.md, agentic-task.md | 48 | 48 match |

Five flags total, all fixed in commit `9072a3a`:

1. `full-rules.md` 2.2: scope narrowed from RU's generic "in case of conflict" to EN's "when two rules collide" (implying exactly two).
2. `full-rules.md` 6.3: vocabulary-avoid item mistranslated "благодарю" ("thank you") as "kindly".
3. `templates/character-frame.md` `<escalation>` block: RU's plain-text "сигнал отказать" rendered as caps-emphasized "REFUSE", contradicting the template's own anti-caps guidance.
4. `templates/character-frame.md` felt-quality quote: RU's "как помог бы человек..." (the helping/suggesting verb) dropped in EN, reducing the analogy to passive noticing.
5. `templates/identification-frame.md` `<master_rules>` skeleton: the sanctioned Ё→em-dash merge collapsed RU's 3 concrete example rules to 2 concrete items plus a bare, content-free `[Rule 3]` placeholder.

Pass-2 re-verification (`layers34-pass2.md`, diff `7ed52b0..83c2677`) confirmed all 5 flags **ADDRESSED**, with one caveat on flag 4: the fix restored the helping verb in `character-frame.md:202` but left `full-rules.md:653` (out of scope for that fix) with the old "like a person who noticed the tool lying right there" wording, so the two files, previously verbatim identical, diverged. That follow-up is closed by commit 1 of this task, which aligns `full-rules.md:653` to `character-frame.md:202`'s wording.

## Layer 4: blind back-translation

Scope: `SKILL.md` (full), `reference/modal-registers.md` (full), `reference/full-rules.md` §§2.7/4.12/4.17/4.20, `templates/extraction-prompt.md` (full).

Three flags:

1. **Description trigger-category compression** (`SKILL.md` frontmatter): round-trip showed "customer service bots" and "team assistants" missing from the trigger list. **ACCEPTED** with justification: near-synonyms within the role-prompt trigger category, a sanctioned Task-3 tradeoff to meet the 1024-byte limit.
2. **Caps permission wording** (`reference/modal-registers.md`): RU grants a bare permission ("можно" = plain register is allowed even on hard limits); shipped EN upgraded this to an empirical equivalence claim ("often works no worse"), undercutting the section's own point. Fixed in commit `83c2677`.
3. **§4.12 generic unconditionality pattern** (`reference/full-rules.md`): RU stacks a domain-neutral template pattern plus the real system-prompt skills quote; shipped EN kept only the skills-specific quote, dropping the generalizable pattern. Fixed in commit `83c2677`.

Pass-2 re-verification confirmed both fixed flags ADDRESSED, with new-breakage checks (Cyrillic, em dashes, unwarranted caps, register, quote-nesting) clean. Follow-up: the 5.6 felt-quality verb cross-file alignment noted above, fixed in commit 1 of this task.

## Closure statement

Per the spec's closure criterion (two consecutive passes without new flags):

- **Layer 1 converges**: all mechanical invariants matched on the single verification pass, nothing to close.
- **Layers 2-4 flags closed or explicitly accepted**: layer 2 had 0 flags; all 5 layer-3 flags and both actionable layer-4 flags were fixed and pass-2-verified ADDRESSED; the one accepted layer-4 flag (description compression) carries an explicit justification rather than a fix.
- **Two consecutive passes, no new flags**: pass 1 ran full-scope across layers 2-4; pass 2 ran over all changed material (the diff from the pass-1 fixes) and found no new flags, only ADDRESSED confirmations plus the one cross-file wording note this task's commit 1 now closes.

## Accepted deviations register

From the task ledger (`progress.md`):

- **Description compression + trigger promotion**: the frontmatter `description` was allowed to compress trigger-category wording to fit the 1024-byte limit, and gained a "tasks for coding agents" item (promoting Type E into the trigger list) as an accepted trigger-coverage improvement.
- **identification-frame.md Ё/em-dash merge**: RU's separate "no Ё" and "no em-dash" example rules were merged into one EN item, since Ё has no EN referent.
- **RU h2 headings preserved 1:1**: the RU original of `full-rules.md` has no `## 3.` / `## 5.` h2 headings at those positions; preserved exactly rather than "fixed," since adding headings would be a content change, not a translation.
- **Contraction-density note**: checklists show higher n't-contraction density (10+5) than the reference layer (3 per file); flagged as a stylistic drift, deferred to final-review triage, not resolved in this verification.

## Glossary

Canonical terminology decisions: [`docs/superpowers/translation/glossary.md`](glossary.md).
