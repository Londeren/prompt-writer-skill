# Claude Plugins

Plugins by [Londeren](https://github.com/Londeren) for Claude Code, claude.ai and any other agent that reads skills. The repository is a plugin marketplace: add it once and install whatever you need from it.

## Plugins

| Plugin | What it does | Docs |
|---|---|---|
| **prompt-writer** | Turns "write me a prompt" into an engineered prompt: routes the request into one of five prompt types, applies 24 master rules derived from the Claude system prompts, then audits the draft against a self-check list | [plugins/prompt-writer](plugins/prompt-writer/README.md) |

## Installation

Every route below installs from this repository. Swap `prompt-writer` for any plugin from the table.

### Claude Code: plugin marketplace

```
/plugin marketplace add Londeren/claude-plugins
/plugin install prompt-writer@Londeren
```

The marketplace registers under the repository owner, `Londeren`, which is why the plugin id ends in `@Londeren`. If the install summary says `Run /reload-plugins to activate.`, run that command.

The same thing without the interactive panel, for scripts and dotfiles:

```bash
claude plugin marketplace add Londeren/claude-plugins
claude plugin install prompt-writer@Londeren
```

Add `--scope project` to the install to share it with everyone working on the current repository.

### Any agent: npx skills

The [skills.sh](https://www.skills.sh) CLI installs into whatever agents it finds (Cursor, Copilot, Codex, Gemini, Cline, Amp, Antigravity and a dozen more):

```bash
npx skills add Londeren/claude-plugins --skill prompt-writer
```

Without `--skill` the CLI lists everything in the repository and asks which skills to install; `-l` lists them without installing. Files land in `.agents/skills/<name>` for the current project, symlinked into each agent's own skills directory. Add `-g` to install for your user instead of the project, and `--copy` if you would rather have real files than symlinks.

### Claude Code: manual copy

Each plugin directory carries its own `plugin.json`, so Claude Code loads it as a skills-directory plugin:

```bash
git clone https://github.com/Londeren/claude-plugins.git /tmp/claude-plugins
cp -r /tmp/claude-plugins/plugins/prompt-writer ~/.claude/skills/prompt-writer
```

Use `.claude/skills/` inside a repository instead of `~/.claude/skills/` to scope it to that project.

### A team on one repository

Commit this to the repository's `.claude/settings.json`. Claude Code offers the marketplace and the plugin to everyone who trusts the folder:

```json
{
  "extraKnownMarketplaces": {
    "Londeren": {
      "source": {
        "source": "github",
        "repo": "Londeren/claude-plugins"
      }
    }
  },
  "enabledPlugins": {
    "prompt-writer@Londeren": true
  }
}
```

### claude.ai

1. Open [Settings → Customize → Plugins](https://claude.ai/new#settings/customize-plugins).
2. Switch to the **Added** tab and press **Add Marketplace**.
3. Choose **Add from a Repository** and paste the repository URL:

   ```
   https://github.com/Londeren/claude-plugins
   ```

4. Press **Sync**. The marketplace appears in the list; install the plugin from it and its skill triggers on its own in any chat.

Enable "Code execution and file creation" in Settings → Capabilities if it is off.

## Repository layout

```
.claude-plugin/
  marketplace.json              - marketplace manifest, one entry per plugin
plugins/<name>/                 - a plugin, this is what ships to users
  .claude-plugin/plugin.json    - plugin manifest
  SKILL.md                      - entry point, the only file loaded on activation
  README.md                     - the plugin's own documentation
docs/                           - specs, plans and development notes, not shipped
CLAUDE.md                       - instructions for Claude Code working on this repository
```

## License

[MIT](LICENSE)
