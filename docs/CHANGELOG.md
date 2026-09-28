# Changelog

## 18.0.1.0.2 (2026-09-28)

- A new icon and store banner, with TechHive branding.

## 18.0.1.0.1 (2026-09-28)

- The store page's comparison table shows its check marks and dashes again.

## 18.0.1.0.0 (2026-09-28)

First release of MCP Server for AI Agents for Odoo 18.0.

- A Connection URL per database, `https://<host>/mcp/<database>`, serving MCP 2025-11-25 and 2026-07-28, with server-wide
  loading for multi-database servers.
- Sign-in with Odoo (OAuth 2.1, including client ID metadata documents) for browser assistants, and MCP keys with
  expiry, rotation, IP limits and companies for developer tools.
- Access rules per model: operations, hidden fields, record filters, write caps and method calls, narrowed per key
  or app, with Preview as key.
- Confirmation before deletes and other risky actions, in chat, by form or in Odoo, and a dry run for every write.
- Setup wizard with presets, the Overview with Test connection, the Playground and the Activity log.
- Custom tools (Lookup, Action, Button and Code), prompts, report PDFs and CSV/XLSX exports.
- An AI badge on everything the AI posts in chatter.
- XML-RPC and REST endpoints for the `mcp-server-odoo` bridge.
