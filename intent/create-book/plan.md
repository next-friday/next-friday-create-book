# Plan: next-friday-create-book

Status: draft. Based on [intent.md](intent.md) and [spec.md](spec.md).

Not started. Work starts once the owner answers open questions 1 to 3 in the intent.

## Approach

A smaller model agent does the building; the main Claude session plans and reviews. Every step ends with a few sample pages for the owner to review. Nothing is built at full length before approval.

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
| 7 | An independent reviewer agent scores the samples against the approved reference | The score is no lower than the approved reference. Before opening the PR, scan the whole repository for trade secrets and real names, then let the owner merge |

## Cost and time (estimate)

- Steps 1–3: USD 3–6
- Steps 4–7: USD 2–4
- Total: USD 5–10, about 1–2 hours of work, not counting time waiting for the owner's review

## Risks

| Risk | Response |
| --- | --- |
| The prototype script is lost before work starts | The spec describes the rules fully enough to rebuild, at higher cost |
| A newer Vivliostyle or Chromium lays pages out differently | Pin versions and keep reference images of the samples for comparison |
| A document with a new table shape gets the wrong block style | Tables that match no pattern stay tables, and authors can pick the style with a comment |
| Real content or a trade secret is committed to the open-source repository | The skill forbids it, `.gitignore` excludes PDFs and output folders, and a forbidden-words check runs before commit |
