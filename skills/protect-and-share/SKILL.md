---
name: protect-and-share
description: Prepare a PDF before sending it - lock it with a password (AES-256), stamp a watermark like DRAFT or CONFIDENTIAL, remove a password the user knows, or keep only the pages to share. Use when the user is about to email, publish or hand over a PDF and wants it protected, marked or trimmed.
---

# Protect, mark or trim a PDF before sharing

Use the `easypdf` skill for getting the file in and out.

## Password protection

- `protect_pdf` encrypts with AES-256. Never invent a password silently. If
  the user asks you to choose one, generate a strong random one locally
  (at least 16 characters), show it once, and tell them to send it through a
  different channel than the file.
- Never write the password into a file name, a commit, a log or a document.
- `unlock_pdf` needs the current password. Only remove protection from files
  the user says they are entitled to open; never guess or brute-force a
  password.

## Watermark

`watermark_pdf` stamps text on every page. Defaults: `opacity` 0.3,
`rotation` -45, `font_size` 60, `color` `"#888888"`. Suggest a recipient-
specific mark for confidential copies (`CONFIDENTIAL - ACME Corp - 2026-10`),
which makes a leak traceable.

## Share only what is needed

Use `extract_pages` to keep the pages the recipient needs (for example, the
signature page and the annex, not the whole contract), then protect or
watermark that smaller file.

## Order of operations

Trim first, then watermark, then protect last: a protected file has to be
unlocked before any other tool can work on it. Save as
`<name>-protected.pdf` / `<name>-watermarked.pdf` and keep the original.
