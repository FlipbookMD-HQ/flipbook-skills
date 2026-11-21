# Flipbook skills

Agent skills for [Flipbook MD](https://github.com/uminocelo/flipbook-md) and [Flipbook Studio](https://github.com/uminocelo/flipbook-studio). Each folder is one skill. Copy the folders into the skills directory your agent reads.

| Skill | Use it when |
| --- | --- |
| `explain-deck` | Explain a topic as a new deck. |
| `convert-deck` | Turn a file or pasted text into a deck. Reads `explain-deck` for the file format. |
| `theme-deck` | Write `presentation.css` for a deck that already exists. |
| `present-deck` | Open a deck with `flipbook show`. |
| `export-deck` | Export PNG images or an MP4. Reads `explain-deck` for presets. |
| `flipbook-studio` | Run the visual editor. |

`explain-deck` is the contract for `presentation.yml`, slides, themes, kits, and export presets. The other skills point at it instead of repeating those rules.

The contract matches the `flipbook` CLI 0.4 (`flipbook-md` on npm, `@flipbook-md/core` 0.13) and Studio 0.3 (`@flipbook-md/core` 0.14). The slide files are the same.

## Install

Copy the skill folders into the directory your agent loads:

| Agent | Directory |
| --- | --- |
| Grok Build | `~/.grok/skills/` |
| Claude Code | `~/.claude/skills/` |
| Codex | `~/.cursor/skills/` |

A project can carry its own copy in `.cursor/skills/` or `.claude/skills/` at the project root. Copy the folders. Do not copy this README into a skill directory.

These skills are [MIT](LICENSE) licensed, the same as Flipbook MD.
