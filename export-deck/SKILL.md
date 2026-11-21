---
name: export-deck
description: >-
  Export an existing Flipbook deck to PNG carousel images or an MP4 with the
  flipbook CLI. Use when the user asks to export, render, or post a deck that
  already exists, or names Instagram, X, LinkedIn, YouTube, TikTok, a carousel,
  or a video.
when-to-use: >-
  export this deck, make the carousel, render the PNGs, export a video, MP4,
  instagram, linkedin, tiktok
argument-hint: deck path, plus where they will post or that they want video
---

# Export a deck

Export a deck that is already on disk. Do not rewrite slides unless `flipbook check` fails or the export warning says a slide is cut off.

Read the explain-deck skill, section **Export**, and follow it: the preset table, the commands, `configuration.yml`, Chromium, ffmpeg, and the rule that an X post shows at most four images.

## Before exporting

```bash
flipbook check <path>
```

`flipbook export` refuses a deck that fails its blueprint. Success contains `<n> slide(s) match the blueprint`, or `match <group>` when the deck sets `group:`. No blueprint prints `no blueprint` and exits 0.

Install the tools once if export says they are missing:

```bash
npx playwright install chromium
```

Video also needs `ffmpeg` on `PATH`.

## After exporting

Images land in `dist/carousel/` unless `--output` says otherwise. Each file is named in slide order (`01-….png`) plus `README.txt`. Video lands in `dist/video/<title>.mp4`, with the details in `<title>.mp4.txt`. `--output` may be a directory or a path that ends in `.mp4`.

When the command prints `Warning: slide(s) N do not fit`, those slide numbers are cut off in the picture. Shorten those slides or pick a taller preset (`instagram` or `tiktok` rather than `linkedin-landscape`). Do not change the blueprint to hide the warning.

Tell the user the output path, the preset, and the slide count. Do not paste the slides. Upload only when they ask, and in numeric order.
