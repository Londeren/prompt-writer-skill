# book-to-skill

Claude Code plugin. A skill that turns a Markdown knowledge source (a book, manual, course, transcript) into a working Claude skill: it extracts the transferable method rather than a retelling, anchors every extracted unit in a verbatim source quote, rejects what a competent specialist would know anyway, assembles a thin SKILL.md plus reference sheets, and measures the result with evals against a no-skill baseline.

## Install

Claude Code, as a plugin:

```
/plugin marketplace add Londeren/claude-plugins
/plugin install book-to-skill@Londeren
```

Any other agent (Cursor, Copilot, Codex, Gemini, Cline and more), through the skills.sh CLI:

```bash
npx skills add Londeren/claude-plugins --skill book-to-skill
```

Manual copies, team-wide project settings and the claude.ai browser route are covered in the [repository README](https://github.com/Londeren/claude-plugins#installation).

## Use

Bring a source in Markdown and ask to make a skill out of it: "make a skill from this book", "extract the methodology", "сделай скилл из книги". The pipeline starts with a go/no-go verdict on whether the source carries a repeatable method at all; refusal is a result, not a failure.

The pipeline runs five phases: go/no-go, extraction by five typed extractors, validation by three filters with a rejection log, assembly into a routed skill folder that works without the source at hand, and an eval run measuring the delta over a no-skill baseline.

## Layout

`SKILL.md` is the entry point and the only file loaded on activation. The four sheets in `references/` are loaded one per phase: extractors, validation, output format, evals.

## License

[MIT](LICENSE)
