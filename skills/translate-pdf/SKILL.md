---
name: translate-pdf
description: Translate a PDF into another language and get a PDF back with the layout kept - contracts, manuals, certificates, research papers, brochures. Use when the user wants the document itself translated, not a summary of it.
---

# Translate a PDF with its layout

Use the `easypdf` skill for getting the file in and out.

1. Confirm the target language if it is not explicit. Use plain language
   names: "English", "French", "Spanish", "German", "Chinese", "Japanese".
   Leave `source_language` on `"auto"` unless the user states it or the
   document mixes languages.
2. Call `translate_pdf` once. Long documents take longer; tell the user it is
   running.
3. Save the result as `<name>-translated-<lang>.pdf` (`-translated-en`,
   `-translated-fr`...) next to the original.
4. If you can read PDFs, check the first page of the result and report the
   page count. Point out anything that stayed untranslated (text inside
   images is not translated).

For official use (immigration, courts, universities), say that many
institutions require a certified translation and that a machine translation
is a working copy, not a replacement.

Scanned PDFs: if the tool reports that no text could be extracted, the file
is a scan; say so and suggest running OCR first.
