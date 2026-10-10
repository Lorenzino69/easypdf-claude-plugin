# Changelog

## 1.2.0 - 2026-10-10

- `easypdf` skill: when EasyPDF is not connected, Claude now gives a direct
  link to the plugin's Connectors tab on Claude.ai and Cowork, and the
  matching easypdf.fr page so the user can finish the task in the browser
  right away instead of leaving empty-handed.
- `easypdf` skill: in Claude Code and Cowork, Claude tries the tool first and
  only asks to connect when a tool asks for it.

## 1.1.0 - 2026-10-04

- `easypdf` skill: new first step when EasyPDF is installed but not
  connected. Claude now tells the user how to connect (`/mcp` in Claude
  Code, Customize > Plugins > EasyPDF > Connectors on Claude.ai and Cowork)
  instead of silently doing the work another way.
- `easypdf` skill: when the code sandbox cannot reach easypdf.fr (the
  default on Claude.ai), Claude switches to the upload link at once instead
  of retrying.
- `easypdf` skill: Claude Code and Cowork can use EasyPDF without an account
  for a few files a day; the skill says which tools need one.
- `easypdf` skill: on Claude.ai, the user drops the PDF in the upload box
  shown in the chat instead of opening a separate page.
- `compress-for-portal` and `application-packet`: compress straight to the
  portal's limit with the new `target_size_mb` option of `compress_pdf`,
  keeping the best quality that fits.

## 1.0.0 - 2026-10-01

First release.

- Hosted EasyPDF MCP server (`https://www.easypdf.fr/mcp/`), 17 tools. The trailing slash matters: Claude Code rejects the OAuth resource without it.
- Skills: `easypdf`, `fix-pdf-text`, `compress-for-portal`,
  `application-packet`, `translate-pdf`, `pdf-to-office`, `batch-pdf`,
  `protect-and-share`.
- Local files are uploaded and results saved next to the originals when
  Claude can run commands.
- Published PDF upload limits of 119 portals, from the pdf-upload-caps
  dataset (CC BY 4.0).
