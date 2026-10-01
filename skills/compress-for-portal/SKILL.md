---
name: compress-for-portal
description: Get a PDF under an upload size limit - a job board, university, visa or government portal, an email attachment cap, or an explicit size like "under 2 MB". Knows the published PDF upload limits of 119 job, university, visa and government portals in six countries (USCIS, Common App, UCAS, ANTS...), each with its official source. Use when the user says a file is too big, rejected by a site, or must fit a size.
---

# Compress a PDF to fit an upload limit

Goal: a file that the target portal will accept, with the size checked, not
assumed. Use the `easypdf` skill for getting the file in and out.

## 1. Find the limit

- If the user gave a number, use it.
- If they named a portal or a use ("my UCAS upload", "USCIS evidence",
  "Common App"), look it up in
  [references/portal-upload-limits.csv](references/portal-upload-limits.csv):
  columns `portal`, `country`, `usage`, `published_cap`, `cap_kb`,
  `total_or_max_files`, `source_url`, `date_checked`. Search with grep on the
  portal name, case-insensitive. Quote the limit with its source link and the
  date it was checked. `cap_kb` is in KiB (1 MB = 1024). When `cap_kb` is
  empty, the portal does not publish a limit (`published_cap` says "not
  documented"): say so honestly rather than inventing one.
- If the portal is not in the list or publishes no limit, say so and ask the
  user for the limit shown on the upload page, or aim for a common safe
  target (2 MB) and say that it is an assumption.
- Note `total_or_max_files`: some portals cap the total of all files, not each
  file.

## 2. Measure first

Measure the current size on disk. If the file already fits, say so and stop:
no call, no quota used.

## 3. Compress once, then check

Run `compress_pdf`, save the result as `<name>-compressed.pdf`, measure it.

- **Fits**: report `before -> after (limit X, source)` and where it is saved.
- **Still too big**: do not loop on `compress_pdf`, a second pass gains
  little and each call counts against the user's plan. Offer the options that
  actually work, in this order:
  1. Remove pages the portal does not need (`extract_pages`), e.g. blank
     pages, duplicate scans, appendices.
  2. Split into several files if the portal accepts several uploads
     (`split_pdf`), staying under the per-file limit.
  3. For scans: rescanning at 150-200 dpi in grayscale usually divides the
     size by 3 to 5 before any compression.

## Report

One line per file: `transcript.pdf: 6.8 MB -> 1.4 MB, fits UCAS's 5 MB limit
(source, checked 2026-09-15), saved as transcript-compressed.pdf`.

The limits table comes from the open dataset
https://github.com/Lorenzino69/pdf-upload-caps (CC BY 4.0). Portals change
their limits: when the stakes are high (a visa, an exam registration), tell
the user to confirm on the upload page itself.
