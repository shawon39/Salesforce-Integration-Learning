# Integration Errors & Exceptions One-Pager (v66.0)

> Common integration errors and exceptions, with cause and fix. Full detail in **[../05-Outbound-Callouts/07-callout-limits-and-testing.md](../05-Outbound-Callouts/07-callout-limits-and-testing.md)**.

---

## Apex callout / runtime exceptions

| Error | Cause | Fix |
|---|:--|:--|
| **`CalloutException: Unauthorized endpoint`** | Endpoint not whitelisted; no Remote Site Setting or Named Credential covers the URL | Add a **Named Credential** (preferred) or **Remote Site Setting**; call via `callout:My_Named_Cred/path` |
| **`You have uncommitted work pending`** | Callout attempted **after** a DML in the same transaction | Do the **callout before DML**, or move the callout to `@future(callout=true)` / Queueable / Continuation |
| **`System.LimitException`** | A governor limit was hit (callouts, heap, CPU, SOQL, DML rows) | Bulkify, reduce loops, move to async (**12 MB** heap, **60,000 ms** CPU), check `Limits` methods |
| **`CalloutException: Read timed out`** | Remote endpoint slower than the timeout | Raise `req.setTimeout(ms)` up to **120,000 ms**; use **Continuation** for long-running calls |
| **`System.JSONException`** | Malformed JSON, or `JSON.deserialize` type mismatch with the response | Validate response shape; use `JSONParser` / typed wrapper classes; null-check before parse |

---

## API errors (REST / SOAP / Bulk) - returned in `errorCode`

| Error | Cause | Fix |
|---|:--|:--|
| **`INVALID_SESSION_ID`** | Session/token expired or invalid (HTTP **401**) | Re-authenticate; refresh the OAuth access token, then retry |
| **`REQUEST_LIMIT_EXCEEDED`** | Org exceeded its **24-hour API request** allocation (HTTP **403**) | Throttle, batch records, use Composite/Bulk API; monitor in **Setup > System Overview** |
| **`INVALID_FIELD`** | SOQL/DML references a field that does not exist or is not accessible | Check spelling, API name, FLS, and object access for the integration user |
| **`MALFORMED_QUERY`** | SOQL syntax error (bad clause, invalid number/date format) | Fix the query; test in **Developer Console** / Query Editor first |
| **`DUPLICATE_VALUE`** | Value collides with a **unique field**, external ID, or duplicate rule | Dedupe before insert; use **upsert** on the external ID; review duplicate rules |
| **`INSUFFICIENT_ACCESS_OR_READONLY`** | No CRUD/FLS/sharing rights, or field is read-only/formula | Grant via permission set, check **OWD / sharing / role hierarchy**; do not write formula fields |
| **`UNABLE_TO_LOCK_ROW`** | Row-lock contention; another process holds the lock (waits up to **10 s**) | Serialize updates, reduce batch size/concurrency, retry with backoff; avoid parent-record contention |
| **`STORAGE_LIMIT_EXCEEDED`** | Org **data or file storage** allocation is full | Purge/archive data, raise storage, or delete attachments/files |
| **`JSON_PARSER_ERROR`** | Request body is not valid JSON, or null input to the parser | Send well-formed JSON; set correct `Content-Type: application/json`; null-check input |

---

## Triage cheat

- **`Unauthorized endpoint`** = setup gap, not code. Named Credential first.
- **`uncommitted work pending`** = the classic **DML-then-callout** ordering bug. Reorder or go async.
- **401 / `INVALID_SESSION_ID`** = refresh token. **403 / `REQUEST_LIMIT_EXCEEDED`** = back off, do not just retry.
- **`UNABLE_TO_LOCK_ROW`** and **5xx** = transient. Retry with **exponential backoff**.
- Always log the **`errorCode`** + `message` from the response body, not just the HTTP status.

*Source: [REST API Developer Guide - Status Codes and Error Responses](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/errorcodes.htm) and [Apex Developer Guide - Callout Limits and Limitations](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_callouts_timeouts.htm). Verified June 2026. See [../05-Outbound-Callouts/07-callout-limits-and-testing.md](../05-Outbound-Callouts/07-callout-limits-and-testing.md).*
