# Spec: next-friday-create-book

Status: draft. Based on [intent.md](intent.md).

This document says what the tool must do and what the result must look like. Every number is a default that has been tested on a long document of about 90 pages, including Thai text. The sizes, margins and pitches were measured from a reference layout (section 6a). All of them can be changed in the design-system file.

## 1. Repository layout

```
brand/
  tokens.json          every design value (section 3)
  fonts/               .woff2 font files under the SIL OFL only, with the licence file of each font
  logo/                logo files, if any; otherwise the wordmark is set in the logo font
tool/
  nf_docs/             code that turns Markdown into HTML, and CSS generated from the tokens
  package.json         Vivliostyle CLI, version pinned
art/
  shapes/              base shapes for the hand-drawn art (section 6)
  gen_art.js           art generator
skills/nf-docs/
  SKILL.md             instructions for Claude (section 8)
examples/
  sample.md            made-up document that uses every page type
README.md              installation and use
.gitignore             covers the local fonts folder and the local config (below)
intent/                intent, spec and plan
```

Commercial fonts are "bring your own". The user points to a local folder outside the repository (or a folder listed in `.gitignore`) from a local config file, and that config file is itself git-ignored. The tool never copies commercial font files into the repository or into examples. `.gitignore` must cover the local fonts folder and the local config file.

## 2. Commands

| Command | Output |
| --- | --- |
| `nf-docs build <file.md> [-o out.pdf]` | The whole document as PDF |
| `nf-docs sample <file.md> --pages cover,toc,<section>` | PDF and PNG of the listed pages only, for review before the full build |
| `nf-docs preview` | A 1–2 page PDF showing the whole design system: colours, fonts, type sizes, spacing, badges, blocks and art |
| `nf-docs check <out.pdf>` | Runs the checks in section 9 and reports |
| `nf-docs art <shape> --bg <colour>` | Hand-drawn art as SVG |
| `nf-docs compare <reference.pdf> [--pages ...]` | Measures the reference with text bounding boxes (left and right edges, line pitches, title sizes, footer position, contents row pitch), writes the measured values next to the current tokens, and renders side-by-side images (reference left, ours right) for each page type |
| `nf-docs fonts --role serif\|sans\|mono --candidates a,b,c [--reference ref.pdf]` | Renders the same strings at the same cap height in each candidate and in crops from the reference. Measures x-height and cap height, set width per cap height, stem thickness per cap height and stroke contrast. Prints a score table (mean relative difference, lower is closer) and writes a comparison image. For Thai, which a Latin reference cannot measure, it renders mixed Thai and Latin lines for a visual choice and runs the mark-collision check (section 5.5) |

Every command works offline after installation.

## 3. Design tokens (`brand/tokens.json`)

Every visual value comes from this file. Code and CSS hold no hard-coded values.

| Group | Values | Default |
| --- | --- | --- |
| color | ink, ink2, ink3, paper, panel, line, accent, accentText, cover | #1E1C1B, #5e5d59, #8f8d87, #FAF9F5, #f0eee6, #dedcd3, #d97757, #c25f3f, #1E1C1B |
| color.opener | Background, rule and number colour of each part opener, in order | dusty blue #C5D3E0, dusty lavender #cfcddc, then repeat |
| color.priority | Fill, border and text of each badge level | must: orange tint; should: grey tint; extra: outline only |
| font | Three roles (serif, sans, mono) plus logo. Each of serif and sans is a pair: a Latin face and a Thai face. Each face has weights for display (light), text (regular) and bold, and its own `sizeAdjust` and ascent, descent and line-gap overrides (section 5.5) | serif: Source Serif 4 (optical size around 20) + Noto Serif Thai; sans: Inter + Noto Sans Thai; mono: JetBrains Mono; logo: Barlow Condensed |
| size | body, lead, chapterTitle, h2, minorHeading, table, label, caption, partNumber, partTitle, coverTitle | 12.5, 17, 32-36 (light), 22, 9.5-10 (bold sans), 10.5, 9, 9, 135 (light), 57, 50-60 (light) pt |
| lineHeight | body, lead, table | 18.75, 23.5, 14.3 pt |
| contents | Row pitch | 51.7 pt |
| space | Spacing scale | 4, 8, 12, 16, 24, 32, 48 pt |
| page | size, marginX, firstLineTop, footerText, footerRule | US Letter (612 x 792 pt), 72 pt, about 61 pt from the top (first text line on continuation pages), about 751 pt, hairline at about 742 pt |
| block | hang (ID column), gap, badgeColumn | 20 mm, 16 pt, 14 mm |
| art | stroke, strokeWidth, wobble, paper | #1f1e1d, 9, 3.2, #f4f3ee |

A4 is allowed as an option for `page.size`. A document can override any token in its front matter (section 4). Tokens it does not override come from this file.

All fonts in the default are SIL OFL and were chosen by measurement (sections 2 and 6a). The default Thai serif is a fallback: a looped Thai text face with a true light weight and low stroke contrast is recommended when available. Commercial fonts are supplied by the user from a local folder (section 1).

## 4. Markdown front matter

```yaml
title: Meeting Room Booking          # required (made-up example)
kicker: Product requirements document
subtitle: …                          # line under the title on the cover; \n breaks the line
owner: Jane Doe
role: Product Manager
approved: 1 Jan 2027                 # without it the cover says "Draft"
footer: Meeting Room Booking · PRD   # footer text on content pages
openers:                             # if omitted, colours and art follow the order in the tokens
  - { art: chart, color: "#c6d2de" }
  - { art: checklist }
tokens: { color.accent: "#2f7d5b" }  # overrides for this document only
```

If a document has no front matter but has a metadata table under its title (status, owner, date), the tool reads the values from that table instead.

## 5. Page types and layout rules

### 5.1 General rules

- Everything aligns to the same left edge of the text block. Left and right margins are equal.
- Vertical spacing uses `space` values only. Elements of the same kind have the same spacing on every page.
- English is the default language and the defaults are tuned for it. Thai text, in a Thai document or mixed into an English line, keeps the same size and baseline as the Latin text around it.
- No letter-spacing and no justification on Thai text. Letter-spacing up to 0.06em is allowed on Latin-only text.
- Numbers use `tabular-nums`.
- No single word alone on the last line of a paragraph (orphan word). A heading stays on the same page as at least two of the blocks after it.
- Vertical centring of contents rows and blocks is measured from the actual ink, including Thai vowels and tone marks above and below the letters. The space from the rule above to the highest ink must equal the space from the lowest ink to the rule below, within 1 px at 300 dpi.

### 5.2 Page types

| Page | Layout |
| --- | --- |
| Cover | Full-bleed cover colour with wordmark, kicker, title and subtitle. Footer with a hairline: owner and role on the left, "approved <date>" on the right. No page number |
| Contents | Three columns: number, title and a one-line sub-topic list. Every row has the same height and a sub-topic line. For a section with no `###` headings, the sub-topics come from the section's own words. No page numbers |
| Part opener | Made from a `## Part N …` heading. Full-bleed tint, hairline, large "0N", part title, intro paragraph, and hand-drawn art at bottom right. No footer |
| Chapter opener | Every `## N. Title` starts a new page with a "Section N" label, the title, and the first paragraph as a lead, at the same position in every chapter |
| Content page | Footer with a hairline: footer text on the left, page number on the right |

The part and section heading words are configurable, so documents in other languages can use their own words.

### 5.3 Turning tables into blocks

The block style is chosen from the table header. Cell wording never changes.

| Table header | Block style |
| --- | --- |
| ID \| Rule | ID in the left column, then the rule as a paragraph. A lead-in ending in ":" at the start of the rule is set bold |
| ID \| Name \| Description \| Priority | ID, bold name with the description below it, and the priority badge in its own right-hand column. Text never runs into the badge column |
| # \| Topic \| field … | Number and topic as the heading, then each field as a row with a small grey label. The first field is the most prominent |
| Any other table | Stays a table: equal cell padding, column widths from measuring the real text, short label columns never break inside a word |

The header names that trigger each style are configurable, so tables written in other languages work too. Authors can choose the style themselves with a comment before the table, such as `<!-- nf:blocks rules -->` or `<!-- nf:table -->`.

### 5.4 Links

In-document links such as `[Section 14](#14-…)` must jump to their target in the PDF. Anchors use short ASCII ids, because long Thai ids make some PDF readers fail to find the target.

### 5.5 Thai typography with substituted fonts

- Set `sizeAdjust` so the Thai consonant height sits level with the Latin x-height or cap height on the same line. Set ascent and descent overrides on the Latin faces so baselines do not move when fonts change.
- Never letter-space Thai. Tracking is only for Latin-only labels.
- After any font change, check the marks above and below the letters at every line pitch in use (body, lead, table, headings, titles). If marks collide, raise the line-height only for the element that collides, to the smallest value that clears, and record it as a token.
- Some commercial Thai fonts have small x-heights and need `sizeAdjust` above 120%.

## 6. Hand-drawn art

- One thick wobbly stroke per shape, as if drawn by a child, with the line overshooting where it closes, on an irregular off-white paper shape.
- Base shapes at the start: truck, checklist, phone, card, gear, chart, people, document. All are drawn new.
- A fixed seed, so every build gives the same image.
- Output is SVG.

## 6a. Matching a reference layout

- Take only the layout system from a reference: page size, margins, type sizes and pitches, colours, rule weights and page structure.
- Never copy the reference's fonts (not even extracted from the PDF), logos, wordmarks, illustrations or text.
- Fonts in a reference PDF are usually proprietary and only partially embedded, so they cannot be reused.
- Record the measured values in the tokens (`nf-docs compare` writes them next to the current tokens). Keep the comparison images out of the repository if the reference is not openly licensed.

## 7. Open-source hygiene

- Examples, test images, reference images and strings in code are made up. No trade secrets, internal product names or real people's names.
- The repository contains no Thai text. Thai test strings that the checks need are stored as Unicode escape sequences in test code, so Thai rendering is still tested.
- Real documents are built on the user's machine or in their own project's repository and are never committed here.

## 8. Claude skill (`skills/nf-docs/SKILL.md`)

Claude must:

1. When asked to make a PDF, first build 3–6 sample pages with `nf-docs sample`, show them, and wait for approval before the full build.
2. When asked to change the look, change document-level tokens (front matter) first. Change `brand/tokens.json` only when the user says the change is for the whole company.
3. Never change the document's wording.
4. Run `nf-docs check` and report the result before handing over.
5. Never put document content into this repository.
6. Never download fonts from unofficial sources. Ask the user for licensed files, keep them outside the repository, and show a comparison image (`nf-docs fonts` or `nf-docs compare`) before switching the document's fonts.

## 9. Automated checks (`nf-docs check`)

| Check | Passes when |
| --- | --- |
| Left edges | Every line starts at an edge the system defines: text block, ID column, list indent or a table column |
| Right edge | No line overflows the text block |
| Links | Every internal link has a target |
| Fonts | All fonts are embedded, no system fallback font appears, and every Thai glyph comes from the bundled Thai font |
| Line breaks | Short label cells and IDs stay on one line |
| Contents | Every row has the same height, and space above and below the ink is equal as in section 5.1 |
| Letter-spacing | None on Thai text |
| Measured values | Edges, body pitch, contents row pitch and footer position match the token targets within 1 pt |
| Baselines | After a font change, no baseline moves by more than 0.5 pt |
| Thai marks | No mark collision at any line pitch in use (checked at 300 dpi) |
| Font licences | Every embedded font allows embedding (fsType), and no commercial font file is present in the repository |

## 10. Out of scope

- Editing in Word or Google Docs
- Slide decks
- Documentation websites
- Translation
