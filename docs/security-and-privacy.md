# Security and privacy

This page explains what data MCP Server for AI Agents sends, who receives it, and how the module keeps AI assistants inside the limits you set. It is written for the people who approve the module: owners, IT and data protection staff. For the settings themselves, see the [admin guide](admin-guide.md).

## Who receives your data

- **The AI assistant your user connects.** When a user connects an AI client, such as Anthropic's Claude or OpenAI's ChatGPT, the records and fields that the AI asks for, and your Access rules allow, are sent to that client. What happens to the data there is governed by that AI provider's terms and your agreement with them.
- **Nobody else.** The module has no telemetry, no licence check and no usage reporting. TechHive receives no data.

The module runs entirely inside your Odoo. It only makes outgoing connections in three cases, all started by you or your users:

1. **Webhooks** in an Action Custom tool that an admin set up. They go to the address in the server action, and only after the call is confirmed and saved.
2. **Client ID metadata documents.** When an app such as Claude or ChatGPT signs in with Odoo, the module fetches the app's public description from the app's own https address, to show its verified name on the consent page. Private and internal network addresses are refused. You can turn this off, or limit it to hosts you list, in Settings.
3. **Test connection.** It calls your own Connection URL, as a client would.

## Nothing is sent until you turn it on

- MCP access is switched off after install. It is switched on only when an admin finishes the setup wizard or ticks **MCP enabled** in Settings.
- Even when it is on, nothing is sent until a user in **MCP User** connects a client with a Credential.
- A model with no Access rule can't be reached at all. After install, no model has one.
- Switching **MCP enabled** off stops every AI call to that database at once.

## Who can connect

- Only users in the **MCP User** group can connect an AI assistant. Only users in **MCP User: Write** can let it make changes.
- The AI always acts as the user who connected it. It works under that user's own Odoo access rights and record rules, and your Access rules narrow that further. It can never do more than the user could do in Odoo.
- The user, their groups and their companies are checked on every request. Removing someone from **MCP User**, archiving them, or removing a company from them takes effect on their next request, for every Credential they have.
- Odoo passwords and Odoo's own API keys are refused. Only MCP keys and OAuth apps made for AI connections work.

## How Credentials are scoped

A Credential is what an AI client signs in with: an MCP key or an OAuth app. Each one can be limited to:

- **Read only**, which blocks every change. A read-only Credential can never be given write access afterwards; the user has to create a new one.
- **Companies**, always within the user's current companies.
- **IP allowlist**, one address or CIDR range per line.
- **Access overrides** per model, and a **Custom tools** list.

Overrides and Credential limits only ever take access away. There is no setting on a Credential that gives it more than the Access rule and the user already allow.

Other protections:

- MCP keys are shown once, when created, and Odoo stores only a hash of them.
- Keys expire, up to the **Longest key lifetime** you set (1 year by default). Rotating a key keeps the old one working for 7 days, then it stops.
- OAuth apps are allowed by the user on an Odoo consent page, where they choose **Read only** or **Read and write** and which companies. They expire at the lifetime cap or after 90 days without use.
- Revoking a key or app stops it on the next request.
- Every sign-in is tied to the database in the Connection URL. A Credential from one database doesn't work on another.
- Rate limits apply per Credential, and to failed sign-ins per IP address.
- With the **Origin allowlist** on (the default), browser-based clients must come from this Odoo or a known AI client.
- On a neutralized copy of a database, such as an Odoo.sh staging build, every Credential from production is revoked.

## Hidden fields

When an admin hides a field on an Access rule, the module removes it from what the AI receives, and refuses any call that tries to use it:

- It is left out of search and read results, exports, dry-run previews and the model description the AI reads.
- Filters, sorting and grouping on it are refused, so the AI can't work its value out by searching.
- Related fields that lead to it are hidden too.
- Writes to it are refused.

These checks run on the server for every call, whichever client or tool makes it. They also run a second time inside Odoo while read tools run, as a backstop.

Some things are outside the hidden-field rules, and the admin screens say so:

- **Reports.** Odoo renders a PDF report as it is, so a report can print a hidden field. Reports are off unless an admin allows them per model, and the Access rule names the hidden fields each allowed report prints.
- **Email, SMS and activity templates and webhooks** in an Action Custom tool run as configured, so they can send a hidden field to the recipient or the webhook's address. The tool form names any hidden fields they use. The AI doesn't receive them: a webhook's dry run masks them.
- **Code Custom tools** run Python as the calling user under Odoo's own access rights. Access rules and hidden fields don't apply to them. Only system administrators can create them.
- **Copies of data** in other models, such as the public employee directory. The Access rule warns you to hide the same fields there.
- **Sensitive models**, such as chatter messages, attachments and Odoo's security settings, hold copies of other data. They are blocked unless an admin unlocks each one, and every write to them is confirmed.

## Confirmation guarantees

Deletes, changes to more than 10 records, method calls, and Custom tools that are destructive or reach outside Odoo are held as a Pending confirmation until someone confirms them. Admins can require confirmation for all writes on a model.

- A Pending confirmation is stored on the server. It is bound to the user, the Credential, the tool, the exact arguments and the company context.
- It can be used once. Confirming with different arguments, from a different Credential, or after it expires doesn't run anything; a new confirmation is needed.
- **In chat** confirmations expire after 5 minutes. The AI client is responsible for asking the user.
- **Confirmation in Odoo** is the stronger option. The approval is given by the signed-in Odoo user on an Odoo page, not asserted by the AI client or the model. Only the user whose Credential asked can approve, and the page expires after 15 minutes. Admins can require it per model or per Custom tool.
- Any write can be run as a dry run first, which shows the effect and changes nothing.

The module does not undo AI changes once they are made. Confirmation and dry runs are there to stop mistakes before they happen.

## What is logged

Every AI call is recorded in **MCP Server > Activity**, with the time, user, Credential, IP address, tool, model, outcome, duration and, when refused, the reason.

- Only the argument **names**, the fields touched and the record IDs are kept. Field values and the content of the call are never logged.
- Activity is deleted after **Activity retention** (90 days by default). Admins can change this, or set 0 to keep everything.
- Only MCP Server Administrators can see the Activity log.
- Everything the AI posts in chatter, including change tracking, carries an **AI** badge naming the key or app. Only an AI call can set the badge.

## Where it runs

The module runs on Odoo.sh and on your own servers. It does not run on Odoo Online (SaaS). Your data stays in your Odoo database; the module stores its settings, rules, Credentials and Activity log there too.

Need help? Email info@techhivesolutions.com
