---
name: fix-pdf-text
description: Change text inside an existing PDF without recreating it - fix a typo, update a date, price, name, address or phone number, refresh a CV, correct an invoice, delete a line. The original fonts, sizes, colors and layout are kept. Use when the user wants their PDF corrected or updated rather than a new document.
---

# Edit text in place in a PDF

`edit_pdf_text` rewrites text inside the original PDF and keeps its fonts and
layout. EasyPDF runs no AI on its side: you decide the exact changes, it
applies them. Use the `easypdf` skill for getting the file in and out.

## 1. Read the PDF before editing

If you can read the file (local file in Claude Code or Cowork), read it and
find the exact strings as printed. The `find` value must match the document:
same spelling, accents, punctuation and spacing. If you cannot read it, ask
the user to paste the exact text, or use `chat_with_pdf` to quote it.

## 2. Plan the edits

- One edit per change: `{ "find": "...", "replace": "...", "page": n }`.
- Keep `find` short and specific: a line or a few words. Long multi-line
  strings are the main cause of misses.
- Every occurrence is replaced. Add `page` when the same text appears
  elsewhere and must stay (a name in a signature block, a date in a footer).
- An empty `replace` deletes the text.
- Dates and amounts written differently in the PDF ("12/08/2026" vs
  "August 12, 2026", "1240" vs "1,240.00") are matched and rewritten in the
  document's own format.
- Keep replacements about the same length as the original when the text sits
  in a tight box (a table cell, a form field). Warn the user if a replacement
  is much longer.

For anything that changes meaning (amounts on an invoice, dates on a
contract, a signature line), show the planned edits as a short before/after
list and get a yes before calling the tool.

## 3. Apply and verify

Call `edit_pdf_text` once with all the edits. The response reports, for each
edit, how many occurrences were replaced, or the closest text found when it
missed. For a miss, correct `find` from that hint and run only the missed
edits again on the **edited** file's link, not on the original.

Save the result as `<name>-edited.pdf`. If you can read PDFs, open the result
and check the changed lines. Report each change as `old -> new (page n)`.

The response also carries `edit_url`, a link that opens the result in the
EasyPDF editor: offer it when the user wants to adjust something by hand.

## Not for

- Scanned PDFs (no real text): say so, and suggest OCR first.
- Writing a new document: use `generate_pdf`.
- Filling form fields: tell the user that the EasyPDF website has a dedicated
  Fill & Sign tool at https://www.easypdf.fr.
