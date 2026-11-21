---
name: present-deck
description: >-
  Open an existing Flipbook deck in the browser with the flipbook CLI. Covers
  install, flipbook show, the announcer view, keyboard controls, and playback
  timing. Use when the user wants to present, play, or open a deck, or asks
  about the announcer, speaker view, or flipbook show.
when-to-use: >-
  present this deck, open the slides, flipbook show, announcer, speaker notes
  view, play the presentation
argument-hint: path to a deck directory or a .slide.md file
---

# Present a deck

Open a deck that is already on disk. Do not rewrite slides, the theme, or the blueprint unless the user asks, or `flipbook show` refuses the deck because the blueprint fails.

## Find the deck

Use the path the user names. Otherwise use the current directory.

A deck is a directory with `presentation.yml`, a directory with one `*.slide.md` file, or a `*.slide.md` file. When several decks are present and the user did not pick one, ask which path to open.

## Install

`flipbook` comes from the npm package `flipbook-md` (CLI 0.4). It needs Node.js 20 or newer.

```bash
npm install -g flipbook-md
```

When the command is missing and a checkout of flipbook-md is the current project, `node bin/flipbook.js` is the same command.

## Show

```bash
flipbook show <path>
```

The server binds to `127.0.0.1` and picks a free port. The command prints two URLs and opens the deck.

| View | URL | What it shows |
| --- | --- | --- |
| Deck | `http://127.0.0.1:<port>/` | The slides for the room |
| Announcer | `http://127.0.0.1:<port>/announcer` | The same slide, plus speaker notes |

The two tabs stay on the same slide. Play in either tab and the other follows. Playback stops on the last slide and does not loop. The tab where Play was pressed owns the timer. Previous, next, Home, and End in either tab move both views.

| Key | Action |
| --- | --- |
| → or Space | Next slide |
| ← | Previous slide |
| P | Play or pause |
| Home / End | First or last slide |
| F | Toggle fullscreen |
| Esc | Leave fullscreen |

Play is disabled on the last slide until you move back. Notes are not drawn on the audience slide. A slide with no notes says "No notes for this slide."

## Timing and transitions

How long Play holds each slide comes from `presentation.interval` in `configuration.yml` at the deck root. The default is 5 seconds, greater than 0 and at most 600. That interval is not the video export duration.

A slide change is instant unless `presentation.transition` is `fade` or `slide`. With `slide`, the new slide comes in from the right going forward and from the left going back. `presentation.transition_duration` is the length in seconds, greater than 0 and at most 5. The default is 0.4. The player skips the animation when the system asks for reduced motion.

```yaml
presentation:
  interval: 5
  transition: fade
  transition_duration: 0.4
```

Write or change `configuration.yml` only when the user asks to change timing or the transition.

## Blueprint

`flipbook show` refuses a deck that fails its blueprint. Run this first when the deck has `blueprint.yml` or `group:`:

```bash
flipbook check <path>
```

Success contains `<n> slide(s) match the blueprint`, or `match <group>` when the deck sets `group:`. No blueprint prints `no blueprint` and exits 0. On failure, fix the slide, not the blueprint, unless the user asked to change the rules. The check rules are in the convert-deck skill.
