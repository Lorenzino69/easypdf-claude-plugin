---
name: easypdf
description: Process real PDF files with the EasyPDF tools - compress, merge, split, extract or rotate pages, edit text in place, translate, convert to Word, Excel or PowerPoint, protect, unlock, watermark, number pages, ask questions about a PDF. Use whenever the user wants something done to a PDF file, local or online. Covers how to get a local file to EasyPDF and how to save the result back to disk.
---

# Working on PDF files with EasyPDF

EasyPDF runs the PDF work on its servers and returns a download link for each
result. Every EasyPDF tool takes a `file_url`, never raw bytes, so the job is
always the same three moves: **get the file in, run the tool, get the result out.**

## 1. Get the file in

Pick the first case that applies.

**The user gave an `https://` link to the PDF.** Pass it as `file_url` as is.

**The PDF is a local file and you can run shell commands** (Claude Code, Cowork):
upload it yourself, the user has nothing to do.

1. Call `get_upload_link` with `file_name` set to the file's name. It returns an
   `upload_url` such as `https://www.easypdf.fr/ai-upload/<token>`.
2. Upload the file to the matching API endpoint, `/api/ai-uploads/<token>`
   (same token, `api/ai-uploads` instead of `ai-upload`):

   ```bash
   curl -fsS -F "file=@<path/to/file.pdf>;type=application/pdf" "https://www.easypdf.fr/api/ai-uploads/<token>"
   ```

   On Windows PowerShell, call `curl.exe`, not `curl` (which is an alias of
   `Invoke-WebRequest` there). Quote paths that contain spaces.
3. Pass the **`upload_url`** (not the API endpoint) as `file_url` to the tool.
   The same link works for several operations on that file for one hour.

**The PDF is local but you cannot run commands** (claude.ai chat): call
`get_upload_link`, show the link, ask the user to open it and drop the PDF,
wait until they confirm, then pass that link as `file_url`. Files attached to
the chat are not reachable by EasyPDF.

Limits: 50 MB per file, 20 uploads per minute.

## 2. Run the tool

| The user wants to... | Tool |
| --- | --- |
| Fix a typo, change a date, amount, name or address inside the PDF | `edit_pdf_text` (see the `fix-pdf-text` skill) |
| Make the file smaller | `compress_pdf` |
| Combine several PDFs | `merge_pdfs` (order of `file_urls` = page order) |
| Cut into parts / keep some pages | `split_pdf` / `extract_pages` (1-indexed) |
| Turn pages | `rotate_pdf` (90, 180 or 270, optional `pages`) |
| Number the pages | `add_page_numbers` (`position`, `format_str` like `"{n} / {total}"`) |
| Stamp "DRAFT", "CONFIDENTIAL"... | `watermark_pdf` |
| Lock / unlock with a password | `protect_pdf` (AES-256) / `unlock_pdf` |
| Translate, keeping the layout | `translate_pdf` |
| Get an editable file | `convert_pdf_to_word`, `convert_pdf_to_excel`, `convert_pdf_to_powerpoint` |
| Create a new PDF from a description | `generate_pdf` |
| Answer a question about a PDF | `chat_with_pdf` (or read the file yourself if you can) |

**Chain without re-uploading.** Every result link is a public `https://` URL
valid for one hour: pass it straight to the next tool. Merge, then number the
pages, then compress, all on links.

**Each processing call counts as one file** on the user's EasyPDF plan. On the
free plan the daily allowance is small, so do not run speculative or
duplicate calls, and prefer one well-planned chain. Paid plans are unlimited.
`get_upload_link` is free.

## 3. Get the result out

**If you can run commands, save the result next to the original** without
being asked, with a suffix that says what happened, and never overwrite the
original:

```bash
curl -fsSL -o "<dir>/<name>-compressed.pdf" "<download_url>"
```

Suffixes: `-compressed`, `-edited`, `-merged`, `-translated-<lang>`,
`-numbered`, `-protected`, `-unlocked`, `-watermarked`, `-pages-<range>`,
`-rotated`; `.docx`, `.xlsx`, `.pptx` for conversions. For `split_pdf`, which
returns a JSON array of links, save `<name>-part-1.pdf`, `<name>-part-2.pdf`...

Then report what changed in one or two lines, with numbers when there are
some: `report.pdf: 8.4 MB -> 1.9 MB (-77 %), saved as report-compressed.pdf`.
Measure sizes on disk (`stat`, `ls -l`, `wc -c`) rather than guessing.

**Otherwise** show each download link as a clickable link and say it expires
in one hour.

## Errors you may meet

- **Sign-in prompt / 401**: the first call opens a Google sign-in for EasyPDF.
  Ask the user to complete it, then retry the same call.
- **Quota message with a link to plans**: show it to the user as is. Do not
  retry, do not try to work around it.
- **"No text could be extracted"**: the PDF is a scan (images of text).
  `chat_with_pdf` and `edit_pdf_text` need real text; say so plainly.
- **Wrong password** on `unlock_pdf`: ask the user for the right one. Never
  guess passwords.
- **Upload returns 410**: the link expired. Call `get_upload_link` again.

## Privacy

Files are processed to carry out the requested operation and deleted
afterwards; result links expire after one hour. Do not send a file to EasyPDF
that the user did not ask you to process. Privacy policy:
https://www.easypdf.fr/confidentialite
