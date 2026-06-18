# Module 12 - Cheatsheets (Quick Reference)

> **Goal**: Single-page references you keep open during real work and skim right before an interview.
> **API version**: v66.0 (Spring '26). When you're stuck mid-task, find the answer in 10 seconds, not 10 minutes.

These condense Modules 01-11 into dense, scannable cards. Each links back to its full module for the deep dive.

---

## The cheat sheets

| Card | What it gives you |
|---|---|
| [oauth-flows](oauth-flows.md) | Every OAuth flow, grant type, tokens, decision tree |
| [patterns](patterns.md) | The 6 integration patterns in one table + picker |
| [api-picker](api-picker.md) | Which API for which use case (12 APIs compared) |
| [event-comparison](event-comparison.md) | Platform Events vs CDC vs Outbound vs Streaming vs Pub/Sub |
| [callout-limits](callout-limits.md) | Every callout and async governor limit |
| [status-codes](status-codes.md) | HTTP status codes Salesforce returns + causes |
| [error-codes](error-codes.md) | Common exceptions and API errors with fixes |
| [named-credentials-syntax](named-credentials-syntax.md) | Copy-paste Named Credential / callout snippets |
| [postman-setup](postman-setup.md) | One-time Postman + Salesforce OAuth setup |
| [debug-checklist](debug-checklist.md) | "My callout failed, now what?" triage flow |

---

## The numbers worth memorizing

| Thing | Value |
|---|---|
| Callouts per transaction | **100** |
| Callout request/response | **6 MB** sync, **12 MB** async |
| Callout timeout | **120 s** max |
| Event retention (high-volume PE + CDC) | **72 hours** |
| Composite subrequests | **25** |
| sObject Collections records | **200** |
| Bulk API 2.0 | **100M** records/24h, **150 MB**/job |
| `@future` / Queueable per transaction | **50** |
| Batch Apex concurrent jobs | **5** |

---

## How to use

Skim the relevant card before you build or before an interview round. For the "why" behind any line, follow the link to the full module. Start anywhere, these are reference, not a sequence.

---

*These cards summarize official Salesforce documentation. Each card cites its specific source.*
