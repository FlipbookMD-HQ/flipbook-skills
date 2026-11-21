---
name: theme-deck
description: >-
  Write a Flipbook custom theme for a deck that already exists. Creates or
  replaces presentation.css and leaves the slides alone. Use when the user
  has a deck, slides, presentation.yml, or a .slide.md file and asks for a
  theme, a palette, colors, a look, or presentation.css.
when-to-use: >-
  theme this deck, custom theme, presentation.css, palette, restyle the
  slides, I have a deck but no theme
argument-hint: deck path, plus optional colors or mood
---

# Theme for an existing deck

Write `presentation.css` for a Flipbook deck that is already on disk. The slides stay as they are.

A request to explain a subject, or to create the deck, is a different task. This skill only themes a deck that already exists.

## Find the deck

Use the path the user names. Otherwise use the current directory.

A deck is a directory with `presentation.yml`, a directory with one `*.slide.md` file, or a `*.slide.md` file. `presentation.css` belongs in that deck directory: beside `presentation.yml`, or beside the `.slide.md` file. One deck in the directory is enough. When several decks are present and the user did not pick one, ask which directory to theme.

Read the title and the slide headings before choosing colors. Read an existing `presentation.css` before replacing it. Check whether the deck sets `style:` or `group:` (a kit): see **How the theme is picked**.

## How the theme is picked

Flipbook paints the slide with the first of these that exists:

1. `presentation.css` in the deck directory.
2. The saved or library theme named by `style:` in `presentation.yml` or the single-file frontmatter (`~/Documents/flipbook/themes/<name>.css`, then core's library).
3. The `style` theme of the deck's kit, when it sets `group:`.
4. The built-in palette named by `theme` (`midnight`, `light`, `paper`, or `forest`).

So a new `presentation.css` replaces a `style` or kit theme for this deck. Say so when the deck has one.

When the user wants an existing saved or library theme rather than a new one, set `style: <name>` and write no CSS. Library themes are `marvel-herbie`, `marvel-jarvis`, `marvel-sentinel`, and `marvel-ultron`.

## What to write

Write `presentation.css` and nothing else. Leave `presentation.yml`, the slide files, and the single-file `.slide.md` unchanged, except for `style:` as above.

The `theme` field stays as it is. It is the base palette under `presentation.css`, and export uses it for the page behind the slide. Change it only when the user names `midnight`, `light`, `paper`, or `forest`, or when the new background flips between dark and light (then pick the closest one).

Use colors, a font, or a type size the user names. When they have not named those, ask before you pick them. Text, muted copy, and accent need to stay distinct on the background.

## Ask before you decide

Do not choose a font, a type size, or a palette the user did not name. Ask, then wait. A header, a footer, and the page counter are chrome, set in `presentation.yml` under `chrome:` or by a kit, not something the theme file invents. Ask before you set them. A Google font is an `@import` on the first line of the theme file, then `font-family` on `:host`. A size change uses `em` on `h1`, `p`, and `li`.

Replace `presentation.css` when the user asks for a theme or a new look. When the file is already there and they asked to adjust it, change the parts they named and keep the rest.

When the user wants to reuse the theme in other decks, also save it to `~/Documents/flipbook/themes/<name>.css` (kebab-case; pick another name when it exists and say so).

Reply with the deck title, the path, and the palette. Do not paste the whole file.

## CSS

The file themes the slide surface in the player, in Studio's preview, and in exported images. Previous, play, and next, the fullscreen button, and the keys ←, →, Space, P, Home, End, F, and Esc stay on Flipbook's stylesheet.

`body`, `html`, `.controls`, `.nav-button`, `.icon-button`, `#previous`, `#next`, and `#fullscreen` sit outside the slide. Rules for them never reach the controls.

Write the theme with these selectors:

- `:host` sets the `--slide-*` variables and, when the subject needs it, `font-family`. A `:root` block in this file is applied as `:host`.
- `:host::after { content: none; }` removes the default decorative ring on a solid background.
- `h1`, `h2`, `h3`, `p`, `li`, `blockquote`, `pre`, `code`, `table` style slide content. `.callout` and `.callout-label` style labelled callouts (`> **Remember:**` and the others).

```css
:host {
  --slide-bg: #f3ecdf;
  --slide-text: #241c14;
  --slide-muted: #5e564c;
  --slide-accent: #9c4228;
  --slide-mint: #2d6a4f;
  --slide-heading: #241c14;
  --slide-line: #e4d8c8;
  --slide-code: #fffaf3;
  font-family: "Iowan Old Style", Palatino, Georgia, serif;
}

:host::after { content: none; }
```

| Variable | Role |
| --- | --- |
| `--slide-bg` | Background |
| `--slide-text` | Titles and default type |
| `--slide-muted` | Body copy |
| `--slide-accent` | Markers and links |
| `--slide-mint` | Eyebrow (the deck title above the slide) and the blockquote border. A callout is a blockquote. |
| `--slide-heading` | `h3` |
| `--slide-line` | Borders |
| `--slide-code` | Code background |

Keep the file under 512 KB. A local `url()` has to be a font or image inside the deck directory (`.woff2`, `.woff`, `.ttf`, `.otf`, `.png`, `.svg`, and the other common image types). A path that leaves the deck is ignored. Skip image and font files unless the user asks for them.
