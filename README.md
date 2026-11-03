![EasyPDF](assets/icon.png)

<h1 align="center">EasyPDF for Claude, Grok Bot and Cursor</h1>

<p align="center">
  <b>Ask your assistant to fix, shrink, merge or translate a PDF. Get the finished file back.</b><br>
  A plugin for Claude Code, Claude Cowork, Grok Bot and Cursor, built on EasyPDF's hosted MCP server (the <a href="https://claude.ai/directory/easypdf">EasyPDF connector</a> on Claude).
</p>

<p align="center">
  <a href="LICENSE"><img alt="License: MIT" src="https://img.shields.io/badge/license-MIT-blue.svg"></a>
  <a href="https://github.com/Lorenzino69/easypdf-claude-plugin/actions/workflows/validate.yml"><img alt="Plugin validation" src="https://github.com/Lorenzino69/easypdf-claude-plugin/actions/workflows/validate.yml/badge.svg"></a>
</p>

https://github.com/user-attachments/assets/7eae0cd7-aea4-478e-b06c-91234c613fa4

*Two edits written into the original invoice, fonts and layout kept, then the download.*

## What it does

```text
> compress ~/Documents/transcript.pdf for my UCAS application

  UCAS accepts up to 5 MB per document (ucas.com, checked 2026-09-15).
  transcript.pdf: 6.8 MB -> 1.4 MB, saved as transcript-compressed.pdf
```

```text
> in invoice-0412.pdf the due date is wrong, it's 15/10/2026, and the total is 1 240,00 €

  2 edits written into the original PDF, fonts kept:
    30/09/2026 -> 15/10/2026 (page 1)
    1 180,00 € -> 1 240,00 € (page 1, 2 occurrences)
  Saved as invoice-0412-edited.pdf
```

```text
> make one PDF for the scholarship with my letter, my CV and the three transcripts in ./docs

  merge 5 files -> number pages -> compress (3 operations)
  Smith_Jane_Scholarship_application.pdf, 14 pages, 3.1 MB
```

The plugin gives Claude the EasyPDF tools **and** the know-how to use them well:
it uploads your local files itself, chains operations without re-uploading,
checks sizes on disk instead of guessing, names the results sensibly and
never overwrites an original.

## Skills

| Skill | Use it for |
| --- | --- |
| `easypdf` | Any PDF job. Moves local files to EasyPDF and the results back to disk |
| `fix-pdf-text` | Change a date, an amount, a name or a typo inside the PDF, original fonts kept |
| `compress-for-portal` | Fit a file under a portal's limit. Ships the published PDF caps of 119 portals (USCIS, Common App, UCAS, ANTS...) with sources |
| `application-packet` | One clean, numbered, size-checked PDF out of a pile of documents |
| `translate-pdf` | Translate a whole document and keep its layout |
| `pdf-to-office` | Word, Excel (tables into cells, checked) or PowerPoint |
| `batch-pdf` | The same operation on every PDF in a folder, with a summary table |
| `protect-and-share` | AES-256 password, watermark, keep only the pages to send |

Skills trigger on their own from what you ask. You can also call one
directly, for example `/easypdf:compress-for-portal`.

## Install

### Claude Code

```bash
claude plugin marketplace add Lorenzino69/easypdf-claude-plugin
claude plugin install easypdf@easypdf
```

It works right away, without an account, for a few files a day. For more, or
for translation and Office conversions, run `/mcp`, select
`plugin:easypdf:easypdf`, choose **Authenticate** and sign in with Google.
There is no API key and nothing to run locally.

### Claude Cowork

Install **EasyPDF** from the plugin directory. A few files a day work without
an account; for more, connect EasyPDF in the plugin's
[Connectors tab](https://claude.ai/customize/plugins/id/8c9a384a-9a5d-4f58-b341-aa1c7d74638b%40anthropic-plugin-directory/connectors).

### Grok Bot

Type `/add-plugin easypdf` in a Bot chat, or open **Plugins** in the sidebar,
search for **EasyPDF** and choose **Add** *(once the marketplace listing is
approved)*. The first time the Bot uses an EasyPDF tool, it shows a
**Connect** card: choose **Authenticate** and sign in with Google in your
browser. There is no API key to copy and nothing to store as a secret; the
OAuth token stays on the host's connector backend and the Bot never sees it.

Files attached in the chat are saved on the Bot's computer: the `easypdf`
skill uploads them with `curl`, runs the tool, then attaches the result to
the reply.

### Cursor

Type `/add-plugin easypdf` in the Agent chat *(once the marketplace listing is
approved)*. Until then, install it from this repository: **Settings >
Plugins**, paste `https://github.com/Lorenzino69/easypdf-claude-plugin`, then
**Add**. Sign in with Google when Cursor asks to authenticate the `easypdf`
MCP server.

### Only want the tools?

The connector alone works in claude.ai, the desktop and mobile apps:
https://claude.ai/directory/easypdf

## Requirements

- `curl` on the machine, to upload local files and download results. It
  ships with macOS, Linux and Windows 10 or later.
- Nothing for a first try in Claude Code or Cowork: a few files a day work
  without an account. Beyond that, an EasyPDF account, created on first
  sign-in. Every processed file counts toward your plan, the same counter as
  on [easypdf.fr](https://www.easypdf.fr): the free plan includes a small daily
  allowance, paid plans are unlimited.

## How it works

```text
local file --upload--> EasyPDF (https://www.easypdf.fr/mcp) --link--> next tool --link--> download next to the original
```

- The MCP server is hosted by EasyPDF. This repository contains no server
  code: only the plugin manifest, the server address and the skills.
- Files go over HTTPS, are processed for the operation you asked for, then
  deleted. Upload and result links expire after one hour.
- `edit_pdf_text` runs no AI on EasyPDF's side: Claude decides the changes,
  EasyPDF writes them.

## Where your data goes

Everything goes to EasyPDF, and nowhere else. The skills reach three
addresses, all operated by EasyPDF over HTTPS:

| Address | What is sent or fetched | When |
| --- | --- | --- |
| `https://www.easypdf.fr/mcp` | Tool calls: file links and operation parameters | Every operation (the server declared in the MCP files listed below) |
| `https://www.easypdf.fr/api/ai-uploads/<token>` | The local PDF you asked Claude to process | Only when Claude can run commands and the file is on your disk |
| `https://easy-pdf-backend.up.railway.app/api/chatgpt/download/...` | The result file, downloaded with `curl` | After each operation, to save the result next to the original |

The upload link is signed, holds one file and expires after one hour, like
the result links. No other service is contacted by the plugin.

## Plugin layout

| File | Read by |
| --- | --- |
| `.claude-plugin/plugin.json`, `.claude-plugin/marketplace.json`, `.mcp.json` | Claude Code and Cowork |
| `.cursor-plugin/plugin.json`, `.cursor-plugin/mcp.json`, `.cursor-plugin/marketplace.json` | Cursor and Grok Bot |
| `.grok-plugin/plugin.json` (with `.mcp.json`) | Grok Build |
| `plugin.json`, `mcp.json` | Other clients that read the Agent Plugins format |
| `skills/` | Every host |
| `assets/logo-plate.png` | Marketplace logo, 512×512, white background |

Every MCP file points to the same server, `https://www.easypdf.fr/mcp/`, with
OAuth sign-in and no headers or secrets.

## Privacy and data

See [PRIVACY.md](PRIVACY.md) and the full policy at
https://www.easypdf.fr/confidentialite. Terms of use:
https://www.easypdf.fr/conditions.

## Support

- Issues and ideas: [GitHub issues](https://github.com/Lorenzino69/easypdf-claude-plugin/issues)
- Email: hello@easypdf.fr
- Security reports: see [SECURITY.md](SECURITY.md)

## License

The plugin (manifest, skills, documentation) is released under the
[MIT License](LICENSE). The EasyPDF service it connects to is a proprietary
hosted service operated by LORENET SYSTEMS and governed by its
[terms of use](https://www.easypdf.fr/conditions).

The portal limits table in `skills/compress-for-portal/references/` comes
from the [pdf-upload-caps](https://github.com/Lorenzino69/pdf-upload-caps)
dataset, licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).
See [NOTICE](NOTICE).
