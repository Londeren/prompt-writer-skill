# Prompt Writer

A skill for Claude that turns "write me a prompt" into an engineered prompt. The methodology is based on close reading of the Claude system prompts (Opus 4.7, Fable 5, Opus 5) and on how a model actually uses a prompt: not by reading it linearly, but through attention mechanics at every output token.

## What it does

When you ask Claude to write or improve a prompt, the skill:

1. Classifies the task into one of five prompt types.
2. Walks it through that type's template, a structure validated against real system prompts.
3. Applies 24 master rules (modality registers, decision-type structure, examples, XML tags, data placement).
4. Runs the result through a draft → audit → final loop: a checklist, diagnostic questions, a rewrite that closes what it found.

## Five prompt types

| Type | For | Key feature |
|---|---|---|
| **A - Character assistant** | A long-lived assistant speaking for a company or a role: a support bot, an internal knowledge assistant, an onboarding bot | Descriptive third person: "The assistant does X", more resistant to manipulation than "you" or "I" |
| **B - Person imitation** | Imitation of a specific person: a founder's ghost-writer bot, a double for social media posts | Identification framing ("You are [Name]") plus 15-25 real message examples, mandatory |
| **C - One-shot task** | A one-time linear task: summarization, translation, a specific piece of text | A short imperative prompt, no character, no extra structure |
| **D - Extraction / transformation** | A reusable prompt following a methodology: extracting insights, generating content from a brand guide, grading | Decision blocks mirroring the structure of the deliverable, input documents up top in `<document>` tags |
| **E - Agentic task** | A task for an agent that changes the state of a system itself: Claude Code, Cursor, computer-use agents | Formula: initial state + target state, file scope, forbidden actions, stop conditions, a binary Done when |

## Key methodology principles

- **Decision-type structure, not topical.** Blocks of the prompt answer the questions the model asks itself at generation time ("when do I do X", "how do I choose between A and B"), not describe topics ("About the product").
- **Modality registers.** A deliberate hierarchy of instruction strength: descriptive third person for identity, NEVER/ALWAYS reserved for real hard limits, should/can/avoids/prefers for everything else. No all-caps, no MUST, both overtrigger on current models.
- **Examples as part of the rule.** 3-5 examples in `<example>` tags for every complex rule; boundary cases matter more than central ones.
- **Duplication of critical rules.** What is an anti-pattern in code (DRY) is a pattern in a prompt: attention weighs a rule's proximity to its point of application more than emphasis.
- **Data up top, request at the bottom.** Large input documents go at the start of the prompt, above the instructions: up to a 30% quality gain on long context.

The full set of rules with reasoning lives in [reference/full-rules.md](plugins/prompt-writer/reference/full-rules.md); a detailed breakdown of the six modality registers lives in [reference/modal-registers.md](plugins/prompt-writer/reference/modal-registers.md).

## Installation

### Claude Code, from the marketplace

```
/plugin marketplace add Londeren/prompt-writer-skill
/plugin install prompt-writer@londeren-plugins
```

If the install summary says `Run /reload-plugins to activate.`, run that command. The skill becomes available as `prompt-writer:prompt-writer` and triggers on its own.

### Claude Code, manual

Copy the plugin directory into your personal skills folder. It carries its own `plugin.json`, so Claude Code loads it as a skills-directory plugin with no marketplace involved:

```bash
git clone https://github.com/Londeren/prompt-writer-skill.git /tmp/prompt-writer-skill
cp -r /tmp/prompt-writer-skill/plugins/prompt-writer ~/.claude/skills/prompt-writer
```

### claude.ai

Works on all plans, including Free. Enable "Code execution and file creation" in Settings → Capabilities first.

1. Build a zip with a `prompt-writer/` folder at its root containing `SKILL.md` (files placed directly at the archive root will fail validation):

   ```bash
   mkdir prompt-writer
   cp -r plugins/prompt-writer/SKILL.md plugins/prompt-writer/templates \
         plugins/prompt-writer/reference plugins/prompt-writer/checklists prompt-writer/
   zip -r prompt-writer.zip prompt-writer
   ```

2. Upload the archive on the [Customize → Skills](https://claude.ai/customize/skills) page: "Create skill" → "Upload a skill".

## Usage

The skill activates on its own for requests like:

- "Write a prompt for our product's support bot"
- "Set up a Claude Project that writes posts in my voice"
- "Improve this system prompt" + the prompt text
- "Set up an assistant that answers questions about our docs"
- "Напиши промпт для..." (the skill triggers across languages)

If the task is described in detail, the skill writes the prompt right away, no extra questions. If it is vague, it asks 2-3 clarifying questions and offers a choice of type.

## Repository structure

```
.claude-plugin/
  marketplace.json              - marketplace manifest, points at plugins/prompt-writer
plugins/prompt-writer/          - the plugin, this is what ships to users
  .claude-plugin/plugin.json    - plugin manifest
  SKILL.md                      - entry point: routing, master rules, process
  templates/
    character-frame.md          - template for Type A
    identification-frame.md     - template for Type B
    one-shot-task.md            - template for Type C
    extraction-prompt.md        - template for Type D
    agentic-task.md             - template for Type E
  reference/
    full-rules.md               - the full rule set with reasoning
    modal-registers.md          - the six modality registers in detail
  checklists/
    self-check.md               - checklist for the audit step
    input-triage.md             - taxonomy of input-prompt defects
docs/, CLAUDE.md                - development notes, not part of the plugin
```

Only `SKILL.md` loads on activation; Claude pulls in templates and reference files as needed (progressive disclosure).

## License

[MIT](LICENSE)
