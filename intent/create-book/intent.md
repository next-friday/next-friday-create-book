# Intent: one PDF document style for the whole company

Author: Next Friday. Status: draft.

## Problem

Every Next Friday document is laid out by hand, so no two look alike, and getting a PDF good enough to hand out takes many rounds of trial and error. The most recent long document needed several rounds before it looked right. The problems were:

- Spacing, gaps and alignment were inconsistent.
- Thai vowels and tone marks sit above and below the letters, so space above and below a line came out uneven.
- The document was mostly tables, which made it hard to read.

What was learned in that work (the spacing system, Thai fonts, turning tables into editorial blocks, chapter opener pages and hand-drawn art) now lives in throwaway scripts that work for that one document only.

## Proposed outcome

A shared repository that every project uses, so every document comes out in the same style:

1. Write the document in Markdown, run one command, and get a book-style PDF in the Next Friday style.
2. Every design value lives in one file: colours, fonts, type sizes, spacing, badges and art. Change that file and every document built afterwards follows. A single document can still override some values for itself.
3. Claude can use it in any project through a skill. When someone asks it to "make a PDF" or "change the accent colour", Claude follows this system and shows a few sample pages before building the whole document.
4. People who do not use Claude can run the command themselves.

Success: an internal document that has already passed visual review is rebuilt with the tool and looks the same (built locally only, never committed here), and a sample document with a different structure comes out consistent without extra tuning.

## Affected users and systems

- **Writers** in every team, mostly writing in English and a few in Thai, such as product engineering and operations, who write Markdown and never lay out pages.
- **Readers** inside the company, partners and investors, who get documents that are easy to read and look alike.
- **Design-system owner**, who changes a colour or font in one place.
- **Claude Code** in every company repository, through the skill.
- **Systems used:** Vivliostyle (CSS paged media), Chromium, Python, Node.js, free fonts from Google Fonts.

## Constraints

- **The repository is open source.** No trade secrets, business content, internal documents, unreleased product names, or customer or employee data. Examples and test images are made up.
- **Repository language is English.** Documentation, code comments, commit messages and pull requests are in English, with no Thai text.
- **English first, Thai always correct.** Most documents are English and the defaults are tuned for English. Thai text, whole documents or mixed into English lines, must still render without defects:
  - No letter-spacing on Thai text, because it detaches vowels and tone marks.
  - No justified Thai lines.
  - Vertical centring counts the marks above and below the letters.
  - Line breaks use the browser's Thai dictionary.
  - Thai glyphs always come from the bundled Thai fonts, at a size and baseline that match the Latin text on the same line.
- **Fonts and art must allow commercial use.** Fonts are under the SIL Open Font License, with their licence files kept. Art is drawn new and copies no other brand.
- **No logos, names or images of other brands**, even where another brand's book is the inspiration.
- **The source wording never changes.** The tool changes layout only.
- **Keep cost low:** build a few sample pages for review first, then the whole document.
- **Works offline after installation**, so the fonts are stored in the repository.

## Open questions

1. Keep the existing MIT licence? Publish the Next Friday brand (colours, logo) as the default theme, or ship a neutral default theme and keep the Next Friday theme as the company's own setting?
2. The NEXT FRIDAY wordmark currently uses the free font Barlow Condensed. Does the company have a real logo file (SVG) or a licensed font for it?
3. How is the skill installed in every project: as a Claude Code plugin from this repository, or by copying the skill folder into each repository?
4. Are scripts other than English and Thai needed, such as Japanese or Chinese?
5. Paper size: A4 only, or also Letter and a landscape layout for slides?
6. Who owns the design-system file, and who approves changes to it?
7. Are more page types needed, such as two side-by-side comparison boxes, call-out boxes, or a signature page?
