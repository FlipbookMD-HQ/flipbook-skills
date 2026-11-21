---
name: convert-deck
description: >-
  Convert existing text or a Markdown file into a Flipbook deck. Keeps the
  source's facts, order, and language, and reshapes them into a cover, summary,
  introduction, development, and conclusion that a blueprint.yml enforces with
  flipbook check. Writes presentation.yml, blueprint.yml, typed slides, a
  matching .slide.md, and a theme. Before writing, asks whether to use an
  existing theme or generate a new one. Use when the user gives a file, a
  document, notes, an article, or pasted text and asks to convert, turn, or
  transform it into slides, a deck, or a carousel.
when-to-use: >-
  convert this to slides, turn this file into a deck, make a deck from this
  markdown, transform this text into a presentation, carousel from this article
argument-hint: path to a text or Markdown file, or the pasted text
---

# Convert text into a deck

The user already has the content. Your job is to reshape it, not to write a new explanation. Use **explain-deck** instead when the user names a topic and wants you to write the content.

## Read the shared contract first

Read the explain-deck skill (`SKILL.md` in the `explain-deck` folder next to this skill) before writing anything. Follow these sections from it exactly as written there:

- **Ask about the theme first**: the theme question, built-in, saved, and library themes, and new themes.
- **Ask before you decide**: images, header, footer, page counter, font, and type size.
- **Attachments**
- **Where to write**: the directory only. Slide filenames follow **Typed slides** below.
- **Export**: the presets table and the cut-off rule.
- **File shapes**
- **Templates, notes and types**, and **Kits and chrome**
- **Parser rules**

This skill replaces explain-deck's **What the deck teaches** section with the rules below.

## Read the source

- A path: read the whole file. A path that does not exist means stop and say so.
- Pasted text: use exactly what was pasted.
- Write the deck in the source's language, unless the user names another language. Callout labels follow the same language.

Before planning slides, list the source's sections in order: headings, `---` breaks, and long runs of prose about one idea. That list is the outline.

## Stay faithful

- Every claim on a slide must come from the source. Do not add facts, examples, dates, or names that are not in it.
- Keep the source's order. Reorder only when the source has no structure at all.
- Keep the source's proper names and terms exactly as spelled.
- Shorten freely: cut connective prose, merge repeated points, and compress a paragraph into a bullet. Do not change what a sentence claims.
- If something does not fit any slide, drop it rather than inventing a slide for it. Mention what you dropped in the reply.

## Deck structure

Every deck has five parts, in this order:

| Part | Type | Slides | Content |
| --- | --- | --- | --- |
| Cover | `cover` | 1 | The subject as the title. Optionally one short sentence taken from the source. Use an image only when the user gives the file. If the blueprint requires an image and they have not given one, ask. Do not draw or generate a stand-in. No bullets. |
| Summary | `summary` | 1 | Table of contents: one bullet per development section, 3–5 bullets. Group sections when there are more than 5. |
| Introduction | `introduction` | 1 | The source's opening: its framing, thesis, or question. |
| Development | `development` | 1–8 | One section or idea per slide, in source order. |
| Conclusion | `conclusion` | 1 | The source's conclusions. When the source states none, restate its key points; do not add new ones. |

That makes 5 to 12 slides. For a longer source, merge sections. For a very short one, use one development slide; do not pad it out.

When the user supplies a blueprint, the target directory already has `blueprint.yml`, or the deck joins a kit (`group:`), use that blueprint's `pages` and `types` instead of this structure, and write slides that satisfy its rules. A deck's own `blueprint.yml` wins over the kit's.

## The blueprint

Write this `blueprint.yml` in the deck directory, next to `presentation.yml`. Set the `development` step's `repeat` to exactly the number of development slides you wrote.

```yaml
pages:
  - type: cover
  - type: summary
  - type: introduction
  - type: development
    repeat: 3
  - type: conclusion
types:
  cover:
    allow: [heading, paragraph, image]
    max_chars: 160
  summary:
    allow: [heading, paragraph, list]
    require: [list]
    max_items: 5
  introduction:
    allow: [heading, paragraph, list, blockquote]
    max_items: 4
    max_chars: 400
  development:
    allow: [heading, paragraph, list, blockquote, code, table]
    max_items: 5
    max_chars: 400
  conclusion:
    allow: [heading, paragraph, list, blockquote]
    require: [list]
    max_items: 5
    max_chars: 400
```

The rules mean:

- `allow`: the only content kinds the slide may contain. The kinds are `heading`, `paragraph`, `list`, `code`, `image`, `blockquote`, and `table`. `###` column labels count as `heading`, and callouts count as `blockquote`.
- `forbid`: kinds the slide must not contain.
- `require`: kinds the slide must contain.
- `max_items`: top-level bullets or steps on the slide, across all its lists. Nested items do not count.
- `max_chars`: visible text on the slide, including the title. Markdown markers do not count.
- `template`: a type can name a template; slides of that type that set none get it.
- A page step can take `repeat: 3` (exactly three), `repeat: { min: 1, max: 8 }` (a range), or `any: [a, b]` instead of `type` (one slide of either type). A step can also override its type's limits.

Keep the blueprint as written above unless the user asks for different rules.

## Typed slides

A slide's type comes from its filename: `slides/NN_<type>.slide.md`, with an underscore before the type.

```text
slides/01_cover.slide.md
slides/02_summary.slide.md
slides/03_introduction.slide.md
slides/04_development.slide.md
slides/05_development.slide.md
slides/06_conclusion.slide.md
```

In `<slug>.slide.md`, one filename cannot type every slide, so start each slide with `<!-- type: <type> -->`. Put the same comment at the top of each directory slide too, so the two files keep identical slide bodies. When a filename and its comment disagree, the filename wins, so keep them the same.

```markdown
<!-- type: development -->
# Slide title

One sentence that states the idea.

- First point
- Second point
```

## Map the source onto slides

| Source | Slide |
| --- | --- |
| Title or main heading | Cover title |
| Opening paragraph, or the text before the first section | Introduction |
| `##` / `###` section | One development slide, or two if it holds two separate ideas |
| `####` subsections under one section | One development slide each when each has a body; otherwise bullets on the parent slide |
| Section headings, in order | Summary bullets |
| Numbered or bulleted list | Up to 5 items; merge or split to fit |
| Two contrasted items (before/after, A vs B, two exceptions) | Labelled bullets (`- **Name:** point`), or two `###` columns of up to 2 bullets each |
| ASCII diagram, arrow chart, or step sequence | A numbered list of up to 5 steps, labels shortened |
| Real code | A fenced block of at most 8 lines |
| A table | A Markdown table of at most 4 rows, only on a development slide |
| A closing judgment or "the answer is…" sentence | `> **Remember:**` in English, `> **Lembre-se:**` in Portuguese, or a conclusion bullet |
| `---` breaks in the source | Section boundaries only; never copy them into a slide |
| Raw HTML or images without a file | Drop |

A diagram in a code fence that is not real code is never copied as a fence. Turn it into numbered steps.

## Fit on the slide

The player renders `###` columns stacked, one above the other, and titles in a very large font. Nothing on a slide is cut, so text that is too long overflows. The blueprint enforces the item count, the character count, and the content kinds. It cannot check line lengths, so hold every slide to these limits as well:

- Title: one line, which is about 24 characters in a proportional font and about 19 in a monospace theme (check the theme's `font-family`). Shorten it to the key noun phrase; the opening sentence carries the rest. A title that wraps to two lines costs one bullet, so that slide holds at most 4. The cover's title is the exception and may wrap.
- Opening sentence: one sentence, at most about 75 characters.
- Bullets and steps: each on one line, at most about 65 characters.
- Two columns: at most 2 bullets per column, and no callout on that slide.
- Callout: only on slides with 3 items or fewer.

## Check before replying

Run the blueprint check on both forms:

```bash
flipbook check <deck-directory>
flipbook check <deck-directory>/<slug>.slide.md
```

From a checkout of flipbook-md, `node bin/flipbook.js check …` is the same command. Both must contain `<n> slide(s) match the blueprint` (or `match <group>` when the deck sets `group:`), with the same `<n>`. On failure, each violation names the slide and the broken rule. Fix the slide, not the blueprint, and run the check again. `flipbook show` and `flipbook export` refuse a deck that fails its blueprint.

Also confirm that:

- `presentation.yml` lists every file in `slides/`, in order
- each slide has exactly one `# ` title and no line that is only `---` inside a fence
- each slide meets the line limits in **Fit on the slide**. Count them; do not estimate.

## Reply

Give the title, the slide count, the theme choice, the paths (including `blueprint.yml`), and one line listing any source content you dropped. Do not paste the slides into the chat. Export only when the user asks, following explain-deck's **Export** section, and pick the preset for where the user says they will post.
