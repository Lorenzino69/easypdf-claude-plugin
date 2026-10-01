---
name: pdf-to-office
description: Convert a PDF into an editable Word document, an Excel spreadsheet (tables extracted into cells) or a PowerPoint deck. Use when the user wants to edit, reuse or analyze a PDF's content in Office, pull tables out of a PDF, or turn a PDF into slides.
---

# Convert a PDF to Word, Excel or PowerPoint

Use the `easypdf` skill for getting the file in and out.

| The user wants... | Tool | Save as |
| --- | --- | --- |
| to edit the text, reuse a document | `convert_pdf_to_word` | `<name>.docx` |
| the tables as data (invoices, statements, reports) | `convert_pdf_to_excel` | `<name>.xlsx` |
| slides to present or rework | `convert_pdf_to_powerpoint` | `<name>.pptx` |

If the user only wants a few words changed in the PDF, do not convert:
`edit_pdf_text` keeps the PDF and its layout (see `fix-pdf-text`).

## After an Excel conversion

When you can run code, open the `.xlsx` and check the result before handing
it over: number of sheets, the header row of each table, row counts. Fix what
is cheap to fix and say what is not, for example numbers stored as text,
merged header cells, a total row picked up as data. If the user's next step
is analysis, offer to do it on the extracted table directly.

## Limits worth saying

- Scans convert as images, not text: say so and suggest OCR first.
- Complex layouts (multi-column magazines, forms) may need touch-ups in Word.
