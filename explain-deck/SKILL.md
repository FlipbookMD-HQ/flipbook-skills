---
name: explain-deck
description: >-
  Explain any topic as a Flipbook deck for an agent that has not seen
  flipbook-md. Writes presentation.yml, slides, a matching .slide.md, and a
  theme. Before writing, asks whether to use an existing theme or generate a
  new one. Use when the user asks to explain something, make slides, a
  carousel, a deck, presentation.yml, or a .slide.md file.
when-to-use: >-
  explain this, explain <topic>, write this as slides, make a deck, carousel,
  presentation.yml, .slide.md, existing theme, new theme
argument-hint: topic or content to explain
---

# Explain as a deck

Write the explanation as a Flipbook deck. The deliverable is files, not a long chat write-up. The topic can be anything the user names. Write it in the language they used, or in a language they name.

Flipbook MD is a local tool that turns one folder of Markdown into slides in a browser. You do not need its source code. A deck is that folder. This contract is the `flipbook` CLI 0.4 (`flipbook-md` on npm). That CLI installs `@flipbook-md/core` 0.13. Flipbook Studio installs core 0.14. The slide files are the same in both.

Show it with:

```bash
flipbook show <deck-directory>
```

When the `flipbook` command is missing, run `node bin/flipbook.js show <deck-directory>` from a checkout of flipbook-md.

## Ask about the theme first

Ask before writing any deck files. Wait for the answer.

Offer two choices:

- An existing theme.
- A new theme.

List the existing themes in the question:

- Built-in palettes: `midnight` (dark blue), `light` (white), `paper` (warm paper), and `forest` (dark green).
- Saved themes: every `*.css` file in `~/Documents/flipbook/themes/`, by filename without `.css`. Skip that directory when it is missing. `FLIPBOOK_HOME`, when set, replaces `~/Documents/flipbook`.
- Library themes that ship with core: `marvel-herbie`, `marvel-jarvis`, `marvel-sentinel`, and `marvel-ultron`.

When the user already names one of those themes, or already asks for a new theme and gives colors, a mood, or a font, use that choice and do not ask again.

### Existing built-in

Set `theme` in `presentation.yml` and in the single-file frontmatter to `midnight`, `light`, `paper`, or `forest`. Do not write `presentation.css`.

### Existing saved or library theme

Set `style: <name>` in `presentation.yml` and in the single-file frontmatter. Flipbook loads `<name>.css` from `~/Documents/flipbook/themes/`, then from core's library. Do not write `presentation.css`: when that file exists, it wins over `style`.

When the user wants the deck to carry its theme with it (to share the folder or move it to another machine), copy the theme file unchanged to `<deck>/presentation.css` instead and leave `style` out. If that CSS uses a relative `url()`, copy the file it points at into the deck at the same relative path.

Set `theme` to the built-in palette closest to the theme's background: `midnight` or `forest` for dark, `light` or `paper` for light. Export uses `theme` for the page behind the slide. Any other name falls back to `midnight`.

### New theme

Write `presentation.css` in the deck directory. Follow colors, a mood, or a font the user names. Otherwise choose a palette that fits the subject. Text, muted copy, and accent need to stay distinct on the background. On a dark background, make the text light.

Save the same CSS to `~/Documents/flipbook/themes/<name>.css`. `<name>` is a short kebab-case name for the palette (lowercase letters, digits, and `-`). Create `themes/` when it is missing. When that file already exists, pick another name and say what you named it.

Set `theme` the same way as for a saved theme.

The file themes the slide surface in the player and in exported images. Previous, play, and next, the fullscreen button, and the keys ←, →, Space, P, Home, End, F, and Esc stay on Flipbook's stylesheet.

`body`, `html`, `.controls`, `.nav-button`, `.icon-button`, `#previous`, `#next`, and `#fullscreen` sit outside the slide. Rules for them never reach the controls.

Write the theme with these selectors:

- `:host` sets the `--slide-*` variables and, when the user names a font, `font-family`. A `:root` block in this file is applied as `:host`.
- `:host::after { content: none; }` removes the default decorative ring on a solid background.
- `h1`, `h2`, `h3`, `p`, `li`, `blockquote`, `pre`, `code`, `table` style slide content. `.callout` and `.callout-label` style labelled callouts.

An installed font needs only its family name. A font file they supply goes in the deck, then:

```css
@font-face {
  font-family: "Deck Face";
  src: url("assets/face.woff2") format("woff2");
}

:host {
  --slide-bg: #f3ecdf;
  --slide-text: #241c14;
  --slide-muted: #5e564c;
  --slide-accent: #9c4228;
  --slide-mint: #2d6a4f;
  --slide-heading: #241c14;
  --slide-line: #e4d8c8;
  --slide-code: #fffaf3;
  font-family: "Deck Face", Palatino, Georgia, serif;
}

:host::after { content: none; }
```

Font files are `.woff2`, `.woff`, `.ttf`, and `.otf`. Skip a font file they did not ask for. Keep `presentation.css` under 512 KB. A local `url()` has to be a font or image inside the deck (those font types, plus `.png`, `.svg`, and the other common image types). A path that leaves the deck is ignored.

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

## Ask before you decide

When you write or change a theme, a template group, or a blueprint, do not pick a visual or a file the user did not name. Ask, then wait. One question can list every open choice. Do not fill the gap and mention it afterward.

Ask about each of these when the user has not already named it:

- The image or GIF on a cover or a media page. Copy the file they give into `assets/`. Do not draw, generate, or invent a stand-in to satisfy a template that requires an image. A cover that needs a picture waits until they point at the file.
- Header, footer, and the page counter (see **Kits and chrome**). Do not add a band of shapes, a line, or a counter ornament they did not ask for.
- The font. Use the family they name. For a Google font, put `@import` on the first line of the theme file, then set `font-family` on `:host`.
- Type size. Leave Flipbook's sizes until they ask. When they want the type larger or smaller, set `em` on `h1`, `p`, and `li`.

## Attachments

Use an image or other file only when the user provides one or asks for one. If a slide requires an image and they have not given a file, ask. Do not create one. Save it at `assets/<kebab-name>.<ext>` in the deck directory.

Reference it from the deck root, in both the directory slides and the single-file deck:

```markdown
![Short caption](assets/btree.png)

[Handout](assets/notes.pdf)
```

One attachment on a slide, after the opening sentence or after the body. Images use `![caption](assets/name.png)`. PDF, audio, and video use a link. Skip `assets/` when the deck has no files.

## Where to write

If the user names a directory, write there.

Otherwise, when `examples/` exists in the current project, write `examples/<slug>/`.

Otherwise write `~/Documents/flipbook/deck/<slug>/`.

`<slug>` is a short kebab-case name taken from the deck title. In that directory write:

- `presentation.yml`
- `presentation.css` when they chose a new theme, or a copied saved theme
- `slides/01-intro.slide.md`, `02-next.slide.md`, …
- `assets/<file>` when a slide uses an image or other file
- `<slug>.slide.md`

Write both the directory slides and `<slug>.slide.md`. The words in each slide match across the two files.

Reply with the title, the slide count, the theme choice, and the paths. Do not paste every slide into the chat.

## Export

Export only when the user asks. Pick the preset for where they will post:

| Preset | Size | Use |
| --- | --- | --- |
| `instagram` (default) | 1080 × 1350 | Instagram carousel |
| `x` | 1080 × 1080 | X multi-image post (at most four images) |
| `linkedin` | 1080 × 1350 | LinkedIn portrait post |
| `linkedin-square` | 1080 × 1080 | LinkedIn square post |
| `linkedin-landscape` | 1200 × 627 | LinkedIn landscape or link preview |
| `youtube` | 1080 × 1080 | YouTube community post |
| `tiktok` | 1080 × 1920 | TikTok photo post |

```bash
flipbook export <deck-directory> --preset instagram
flipbook export <deck-directory> --preset x --output dist/x-carousel
flipbook export <deck-directory> --format video --duration 60 --transition fade --transition-duration 0.5
```

Images go to `dist/carousel/` unless `--output` says otherwise. Video needs `--duration` (the whole MP4, in seconds, greater than 0 and at most 1800) and ffmpeg on `PATH`. The default frame rate is 30; `--fps` is an integer from 1 to 60. `--transition` is `none`, `fade`, or `slide`. `--transition-duration` is each fade or push, in seconds, greater than 0 and at most 5 (default 0.4). The transitions have to fit inside `--duration`. Carousel and video export need Chromium (`npx playwright install chromium`).

A flag you pass overrides `configuration.yml`. A flag you leave out keeps the file's value. An absent file is not an error.

```yaml
export:
  preset: instagram
  format: carousel
presentation:
  interval: 5
  transition: fade
```

Write `configuration.yml` only when the user asks for saved export or playback settings.

Export prints `Warning: slide(s) N do not fit the <preset> image and are cut off` when a slide overflows the picture. Shorten that slide or pick a taller preset. The player does not clip; it lets long text overflow the slide.

**Cut-off rule:** an X post shows at most four images. When they post to X, the first four slides (cover included) must stand on their own. Tell them when the deck is longer than four.

## What the deck teaches

Explain the subject they named. Use its real terms. Do not swap in a different topic.

Write 6 to 9 slides:

1. Cover. Title is the subject. One sentence under it. Three or four bullets are the agenda.
2. Middle slides. One idea each. The first three slides after the cover must stand alone (see the cut-off rule).
3. Last slide. Title `Takeaways` (in the deck's language). A short checklist the reader can apply.

Each slide has one `#` title, one opening sentence, then one body. The body is one of:

- 3–5 bullets
- a numbered list of 3–5 steps, for a sequence or a process
- exactly two `###` columns, with up to 3 bullets each
- a fenced code block of at most 8 lines

Add one `> **Remember:**` callout when a slide needs a conclusion. Do not put columns, a code block, and a callout on the same slide. Titles and bullets stay on one line.

## File shapes

`presentation.yml`:

```yaml
title: "Deck Title"
theme: "midnight"
slides:
  - "slides/01-intro.slide.md"
  - "slides/02-next.slide.md"
```

List every slide. Paths are relative to the deck directory. Quote `title` and each path. Add `style: "<name>"` for a saved or library theme.

One directory slide:

```markdown
# Slide title

One sentence that states the idea.

- First point
- Second point
- Third point

> **Remember:** One sentence the reader should keep.
```

Two columns replace the bullet list:

```markdown
### Left label
- Point
- Point

### Right label
- Point
- Point
```

Steps replace the bullet list:

```markdown
1. First step
2. Second step
3. Result
```

A code slide uses a real fence and real line breaks:

````markdown
```sql
CREATE INDEX users_email_idx
ON users (email);
```
````

`<slug>.slide.md` is the same metadata as frontmatter, then the same slide bodies. A line that is only `---` separates slides. Nothing else goes on that line.

```markdown
---
title: "Deck Title"
theme: "midnight"
---
# Slide title

One sentence that states the idea.

- First point

---

# Next title

Next sentence.
```

## Templates, notes and types

Write these only when the user asks for them, or when a kit or blueprint needs them. Each is an HTML comment at the top of a slide (or a key in that slide's frontmatter):

- `<!-- template: cover -->` places the slide on the 3×3 grid. Built in: `flow` (the default, no placement), `cover` (center cell), and `summary` (lower-right four cells). Frontmatter `template:` wins over the comment. A deck adds its own in `templates.yml`. `flipbook template add <name> --column <n> --row <n> --width <n> --height <n> --align start|center|end`, run in the deck, writes that file and also `~/Documents/flipbook/templates/<name>.yml`. `show` and `export` read the deck file, not the home copy. A kit adds templates named `<kit>_<name>`.
- `<!-- type: development -->` names the slide's page type for a blueprint. In a directory deck the filename `NN_<type>.slide.md` sets it too, and the filename wins.
- `<!-- notes: Speaker notes here -->` adds speaker notes. They are not shown on the slide. A directory slide can use frontmatter `notes:` instead. When both are set, the frontmatter note comes first. A `---` line inside a notes comment does not split slides.

## Kits and chrome

A kit is a template group: `~/Documents/flipbook/templates/<kit>.yml`, or one that ships with core. It bundles templates, themes, a blueprint, and chrome. A deck joins a kit with `group: <kit>` in `presentation.yml` or the frontmatter. It then gets the kit's templates and blueprint, the kit's `style` theme unless the deck has `presentation.css` or its own `style`, and the kit's `canvas` as `theme` unless the deck sets one. Use a kit only when the user names it.

Built-in kits, all using `marvel-ultron`, `marvel-jarvis`, `marvel-herbie`, and `marvel-sentinel`:

| Group | Pages |
| --- | --- |
| `marvel_content_character` | Cover with a title and image, a table of contents, then any mix of short topics (3), medium topics (5), a media page, a quote, and a callout. |
| `marvel_content_guide` | Cover with a title and image, then timeline pages of at most 5 stops. |
| `marvel_content_place` | Cover, table of contents, then one content page per place. |
| `marvel_content_item` | Cover, table of contents, then one content page per item. |

Chrome is the header band, the footer band, and the page counter. A kit sets it, and a deck overrides it with `chrome:` in `presentation.yml`:

```yaml
chrome:
  counter:
    corner: bottom-left
  header:
    kind: none
  footer:
    kind: line
    line: dotted
```

`kind` is `none`, `line`, or `shapes` (`shape: circle`, `triangle`, `square`, `pentagram`, `hexagram`, `heptagram`, `octagram`, or `nonagram`). A header or footer line is `straight`, `dashed`, or `dotted`. The counter's line is `solid`, `dashed`, or `dotted` (not `straight`). Optional counter fields: `line_align` (`top`, `middle`, or `bottom`), `line_length` (`100%` or a shorter value), and `ornament` (a short glyph or a small image in the deck). The cover does not show a page counter. Turn a kit's band off with `kind: none`. Do not add chrome the user did not ask for.

## Parser rules

`flipbook show`, `flipbook export`, and Studio render the same Markdown the same way. Write slides that survive it.

- The title is the first `# ` line. `##` is not a title.
- Everything on the slide is rendered: headings, paragraphs, lists, quotes, code, tables, and images. Nothing is dropped. In the player, long text overflows the slide. Export cuts off what does not fit the image and names the slide. Keep the opening sentence first.
- A `- ` line after a `###` reads as that column.
- A callout is `> **Label:**` with one of these labels, shown in the deck's language: Remember/Lembre-se, Tip/Dica, Reminder/Lembrete, Learn more/Saiba mais, Next/A seguir/Próximo, and CTA. Any other `> **Label:**` is a plain quote with a bold word.
- There is no diagram syntax. Write a sequence as a numbered list.
- `**bold**` and `*italic*` render. Raw HTML is removed. HTML comments other than `template`, `type`, and `notes` are ignored.
- In the single file, a line that is only `---` splits slides even inside a fence, so keep fences free of that line.
- `theme` is `midnight`, `light`, `paper`, or `forest`. Slide size comes from the export preset, not from the deck.
- A local image or file uses an `assets/` path from the deck root, as in Attachments.
