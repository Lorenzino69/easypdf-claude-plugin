# Security policy

## Reporting a vulnerability

Please report security issues privately to **hello@easypdf.fr** with
"Security" in the subject, not in a public issue. Include the steps to
reproduce and the impact you observed. You will get an answer within five
business days.

This covers the plugin in this repository and the EasyPDF MCP server it
connects to (`https://www.easypdf.fr/mcp`).

## What the plugin does on your machine

- It runs no background process, hook or binary.
- Claude runs `curl` to upload the files you ask it to process and to
  download the results next to the originals. Originals are never
  overwritten.
- Authentication is OAuth 2.1 with PKCE, handled by your Claude client.
  The plugin stores no token or password.
