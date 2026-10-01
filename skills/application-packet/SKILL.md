---
name: application-packet
description: Build one submission-ready PDF from several documents - a job, university, scholarship, visa, rental or grant application. Orders the pieces, merges them, numbers the pages and fits the result under the portal's size limit. Use when the user has to send or upload several documents as a single PDF.
---

# Build an application packet

Turn a pile of documents (CV, cover letter, transcripts, diplomas, ID, proof
of address...) into one clean PDF the recipient will accept. Use the
`easypdf` skill for getting files in and out, and the `compress-for-portal`
skill for the size limit.

## 1. Collect and order

- List the files the user pointed to (a folder, a list, attachments). Only
  PDFs go into `merge_pdfs`; if there are images or Word files, say which ones
  and ask the user to export them to PDF first.
- Propose an order and confirm it before merging. Default order when the
  recipient sets none: cover letter, CV or form, then supporting documents
  from most to least important, identity documents last.
- Ask for the target: which portal or recipient, and its size limit if known.

## 2. Plan the chain before calling anything

Each call counts as one file on the user's EasyPDF plan, so plan the shortest
chain and say it in one line before starting, for example:
`merge 5 files -> number pages -> compress (3 operations)`.

- Skip page numbers if the user does not want them or the recipient forbids
  them.
- Skip compression if the merged file already fits the limit.

## 3. Run it on links

1. Upload each local file (`easypdf` skill), then `merge_pdfs` with the links
   in the confirmed order.
2. `add_page_numbers` on the merged link: `position` `"bottom-center"` and
   `format_str` `"{n} / {total}"` unless the user wants something else.
3. Download, measure, and only if it is over the limit, `compress_pdf` on the
   current link.

Pass each result link straight to the next tool; never re-upload an
intermediate file.

## 4. Deliver

Save one file, named for the recipient:
`<Lastname>_<Firstname>_<Recipient>_application.pdf` when you know the names,
otherwise `application-packet.pdf`, next to the source files. Report:

- the page count and order (one line per document with its page range),
- the final size and the limit it fits,
- where it is saved.

Never include a document the user did not list, and never drop one silently.
