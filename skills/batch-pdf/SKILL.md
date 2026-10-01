---
name: batch-pdf
description: Apply the same PDF operation to many files at once - compress every PDF in a folder, watermark a set of documents, number the pages of all chapters, convert a batch to Word. Use when the user points at a folder, a glob or a list of PDFs and wants each one processed.
---

# Process many PDFs in one go

Requires shell access (Claude Code, Cowork). Use the `easypdf` skill for the
upload and download steps of each file.

## Before starting

1. List the matching files with their sizes, and show the count and the total
   size. Skip files that are not PDFs and say so.
2. Each file processed counts as one file on the user's EasyPDF plan. Say the
   number of operations and get a yes before running more than 5. Free plans
   have a small daily allowance; if the user hits it mid-batch, stop and
   report what was done.
3. Decide the output: next to each original with the usual suffix (default),
   or a sibling folder such as `<folder>-compressed/` when the user prefers.
   Never overwrite originals.

## Run

- Process files one by one: upload, run the tool, download, measure. Uploads
  are limited to 20 per minute; pause if you get a 429.
- Keep going when one file fails (password-protected, corrupted, scan): note
  the reason and move on.
- For compression, skip files already under a size the user cares about,
  rather than spending an operation on them.

## Report

A compact table, one row per file: name, before, after, status. End with the
totals (`14 files, 182 MB -> 41 MB, 1 failed: scan.pdf is password-protected`)
and the output location.
