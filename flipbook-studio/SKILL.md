---
name: flipbook-studio
description: >-
  Run Flipbook Studio, the local visual editor for Flipbook MD decks. Covers
  install, where files live, images, import, present, export, version history,
  and optional AI. Use when the user mentions Studio, flipbook-studio, the
  visual editor, or wants to edit a deck in the browser instead of by hand.
when-to-use: >-
  open studio, flipbook-studio, edit this deck in the app, visual editor,
  import a word file, version history
argument-hint: folder of decks, or a question about Studio
---

# Flipbook Studio

Studio is the visual editor for Flipbook MD. Decks stay as files in a folder the user chooses, so the `flipbook` CLI reads the same files. Studio 0.3 listens only on `127.0.0.1`. It does not upload decks.

When the user wants the files written or changed by hand, use explain-deck, convert-deck, or theme-deck instead of describing clicks.

## Install and run

Node.js 20 or newer.

```bash
npm install -g https://github.com/uminocelo/flipbook-studio/releases/latest/download/flipbook-studio.tgz
flipbook-studio [folder]
```

Keep the terminal open. Ctrl+C stops Studio.

| Option | What it does |
| --- | --- |
| `[folder]` | Folder of decks. Defaults to the current directory. |
| `--port <n>` | Port to try first. The default is 4321. Studio picks a free port when that one is busy. |
| `--no-open` | Print the address and do not open a browser. |
| `--library <dir>` | Themes, templates, blueprints, and kits. Defaults to `~/Documents/flipbook`. `FLIPBOOK_HOME` replaces that default. |

Run the install command again to update. Uninstall with `npm uninstall -g @flipbook-md/studio`.

## Where things are

| What | Where |
| --- | --- |
| Decks | The folder passed to `flipbook-studio` |
| Themes, templates, blueprints, kits | `~/Documents/flipbook`, or `--library`, or `FLIPBOOK_HOME` |
| Settings, including API keys | `~/Library/Application Support/flipbook-studio` on macOS, `~/.config/flipbook-studio` on Linux, or `FLIPBOOK_STUDIO_CONFIG_DIR` |
| Import and AI scratch files | `.flipbook-studio-tmp/` in the open folder, removed when the run finishes |

The settings file is mode `0600`. Keys are never written into a deck.

## What the app does

- **Write.** New deck, a form or Markdown, live preview. Add, reorder, and delete slides. Undo and redo keep the last 50 steps for the open deck and clear when Studio stops. Ctrl/Cmd+Z and Ctrl/Cmd+Shift+Z work outside text fields. Studio will not undo over a file that changed outside Studio.
- **Images.** `assets/` in the deck (next to the file for a single-file deck). Upload, drop, or paste PNG, JPEG, GIF, WebP, or SVG, up to 8 MB. Names become kebab-case. A taken name gets `-2`, `-3`, and so on.
- **From a file.** New deck → From a file accepts `.md`, `.txt`, or `.docx` up to 25 MB. Text must be UTF-8. `.doc`, password-protected Word, and PDF are not supported. Import as is keeps the words. Convert with AI reshapes them into the kit's slide types.
- **Present and export.** Full screen, or PNG carousel and MP4. Carousel and video need Chromium (`npx playwright install chromium`). Video also needs ffmpeg. Presets and the CLI export command are in the export-deck skill.
- **Duplicate.** A deck copy is saved next to the original as `<name>-copy`. `dist/`, hidden files, and symlinks are not copied. A kit piece is copied into the library. `flow`, `cover`, and `summary` cannot be duplicated.
- **History.** Needs git 2.30 or newer. Save version commits only that deck's files, with an optional note. Studio never pushes, pulls, amends, rewrites history, creates branches, or changes git config. Without git, History is off and the rest of Studio works.
- **AI is optional.** Settings can use Ollama, LM Studio, or any local OpenAI-compatible URL. Paid providers are Anthropic, OpenAI, or another `https://` base URL. **Settings → Model for each task** assigns a model to Rewrite a slide, Generate a deck, Draft a kit piece, and Chat. Deck content is sent to a paid provider only for a task assigned to it. Every AI edit is a diff the user accepts or rejects.

Built-in kits and the slide file format are in the explain-deck skill, section **Kits and chrome**.
