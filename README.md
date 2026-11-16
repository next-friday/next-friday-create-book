# next-friday-create-book

A tool, planned and not yet built, that turns a Markdown file into a book-style PDF in one consistent company style. It also includes a Claude skill, so Claude follows the same style in any project.

## What it will do

- Build a PDF from Markdown with one command, with a cover, contents, part openers, chapter openers and editorial blocks made from tables.
- Keep every design value in one file, `brand/tokens.json`: colours, fonts, type sizes, spacing and hand-drawn art. A single document can override values in its front matter.
- Set English and Thai correctly. Thai vowels and tone marks must never collide or clip, and Thai text is never letter-spaced.
- Show a few sample pages for review before building a whole document.
- Measure a reference layout and compare it side by side with our pages (`nf-docs compare`). Choose fonts by measurement (`nf-docs fonts`).
- Ship only open-licence (SIL OFL) fonts. Commercial fonts are brought by the user and kept outside the repository.

## Status

Planning only. Nothing is built yet. The documents are in [`intent/create-book/`](intent/create-book/):

| File | What it holds |
| --- | --- |
| [`intent.md`](intent/create-book/intent.md) | The problem, the outcome, the constraints and the open questions |
| [`spec.md`](intent/create-book/spec.md) | What the tool must do and what the result must look like, with every default value |
| [`plan.md`](intent/create-book/plan.md) | The order of the work, its risks and the estimated cost |

## Rules for this repository

This repository is open source.

- English only.
- No company documents, trade secrets, product names or personal data. Examples are made up.
- No commercial font files. Only SIL OFL fonts with their licence files.

## Licence

See [`LICENSE`](LICENSE).
