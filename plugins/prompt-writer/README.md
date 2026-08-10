# prompt-writer

Claude Code plugin. A skill that turns "write me a prompt" into an engineered prompt: routing by task type, modality registers, XML-tagged examples, and a draft to audit to final loop.

## Install

```
/plugin marketplace add Londeren/prompt-writer-skill
/plugin install prompt-writer@Londeren
```

## Use

The skill triggers on its own when you ask for a prompt, a system prompt, an agent instruction, or an improvement of an existing prompt. A detailed request goes straight to a draft. A vague one gets 2-3 clarifying questions first.

## Layout

`SKILL.md` is the entry point and the only file loaded on activation. `templates/`, `reference/` and `checklists/` are loaded on demand.

Full documentation, methodology background and the development notes: https://github.com/Londeren/prompt-writer-skill
