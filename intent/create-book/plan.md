# Plan: next-friday-create-book

Status: draft. Based on [intent.md](intent.md) and [spec.md](spec.md).

Not started. Work starts once the owner answers open questions 1 to 3 in the intent.

## Approach

An agent builds the tool quickly, with a smaller model doing the building and the main Claude session planning and reviewing. Speed is not the constraint. Quality is gated by sample pages and automated checks that the owner reviews. Every step ends with a few sample pages for the owner. Nothing is built at full length before approval.

The starting point is a prototype script made for one internal document. It contains parts tied to internal content, so it is not copied in. The tool is rewritten from the spec, taking the method and none of the content.

## Steps

| Step | Work | Done when |
| --- | --- | --- |
| 1 | Write the tool in the layout of spec section 1 from the prototype, without internal content. Add fonts with their licences. Pin the Vivliostyle version | `nf-docs build` rebuilds the internal reference document locally and it looks the same as the approved version. That document is never committed |
| 2 | Move every hard-coded value into `brand/tokens.json` and generate the CSS from the tokens | No hard-coded colour or size remains in the code, and `nf-docs preview` shows the whole system |
| 3 | Remove anything tied to one specific document. Implement front matter (section 4), block styles chosen from table headers (section 5.3) and the author override comments | The made-up English `examples/sample.md`, structured differently from the reference document, renders every page type correctly, and a Thai test document built from escaped test strings renders with no broken marks |
| 4 | Art generator with the eight base shapes (section 6) | `nf-docs art` produces all eight shapes, and part openers take their shape from front matter |
| 5 | `nf-docs check` (section 9) | The reference document (locally) and the sample document pass every check |
| 6 | Write the skill and the README | In another repository, Claude asked to "make a PDF from this file" follows the skill: shows samples first, runs the check, and does not touch this repository |
| 7 | Update the tokens to the measured defaults (spec section 3): US Letter, 72 pt side margins, the new sizes and pitches, the three font roles and the measured colours | `nf-docs preview` shows the new defaults, and a sample page matches the targets within 1 pt |
| 8 | `nf-docs compare` (spec section 2): measure a reference PDF, write measured values next to the tokens, render side-by-side images | Run on a made-up reference, it reports edges, pitches, title sizes, footer position and contents row pitch, and renders one image per page type |
| 9 | `nf-docs fonts` (spec section 2): candidate scoring for serif, sans and mono, and the mixed Thai and Latin visual mode with the mark-collision check | The score table and comparison image are produced, and a deliberately colliding Thai line is caught |
| 10 | Bring-your-own commercial fonts: local config, `.gitignore` entries for the local fonts folder and config, and the documentation for the skill | A font in a folder outside the repository is used in a build, and no font file or config shows up as an untracked file to commit |
| 11 | Add the new checks (spec section 9): measured values, baseline movement, Thai marks at 300 dpi, font licences and embedding | All checks pass on the sample document, and each fails on a deliberately broken input |
| 12 | An independent reviewer agent scores the samples against the approved reference | The score is no lower than the approved reference. Before opening the PR, scan the whole repository for trade secrets and real names, then let the owner merge |

## Cost (estimate)

Agent cost only, as an estimate:

- Steps 1–3: USD 3–6
- Steps 4–6 and 12: USD 2–4
- Steps 7–11: USD 3–6
- Total: USD 8–16

## Risks

| Risk | Response |
| --- | --- |
| The prototype script is lost before work starts | The spec describes the rules fully enough to rebuild, at higher cost |
| A newer Vivliostyle or Chromium lays pages out differently | Pin versions and keep reference images of the samples for comparison |
| A document with a new table shape gets the wrong block style | Tables that match no pattern stay tables, and authors can pick the style with a comment |
| Real content or a trade secret is committed to the open-source repository | The skill forbids it, `.gitignore` excludes PDFs and output folders, and a forbidden-words check runs before commit |
| A commercial font file is committed by mistake | The fonts folder and the local config are git-ignored, and the check fails if any non-OFL font file is present |
