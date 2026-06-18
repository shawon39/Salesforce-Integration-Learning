# Callout & Async Limits One-Pager (v66.0)

> Every callout and async governor limit in one place. Full detail in **[../05-Outbound-Callouts/07-callout-limits-and-testing.md](../05-Outbound-Callouts/07-callout-limits-and-testing.md)** and **[../09-Security-Limits/05-governor-and-api-limits.md](../09-Security-Limits/05-governor-and-api-limits.md)**.

---

## Per-transaction callout limits

| Limit | Value | Notes |
|---|---|:--|
| **Callouts per transaction** | **100** | Combined HTTP + Web service calls, sync **and** async |
| **Request + response size (sync)** | **6 MB** | Counts against the **6 MB sync heap**; body + headers |
| **Request + response size (async)** | **12 MB** | Counts against the **12 MB async heap** |
| **Default callout timeout** | **10 s** | Per single callout if `setTimeout` not set |
| **Max timeout per single callout** | **120 s** | Set via `req.setTimeout(ms)`; range **1 - 120,000 ms** |
| **Total callout time per transaction** | **120 s** | Cumulative across **all** callouts in the transaction |
| **Callout after uncommitted DML** | **Not allowed** | Throws `You have uncommitted work pending`; do DML-then-callout split or use async |

---

## Concurrency limits (org-wide, not per-transaction)

| Limit | Value | Notes |
|---|---|:--|
| **Concurrent long-running sync requests** | **10** | A sync request counts once it runs **> 5 s** |
| Scales by user licenses | up to **~50** | +1 per license bucket above the base; org-wide cap |
| Synchronous callouts | **excluded** from the long-running limit | A sync request waiting on a callout does **not** count (Spring '20+) |

---

## Continuation (long-running async callout)

| Limit | Value | Notes |
|---|---|:--|
| **Parallel callouts per Continuation** | **3** | Run simultaneously, suspend the request |
| **Timeout per callout** | **120 s** | Applies regardless of how many callouts |
| **Chained Continuations** | **3** | Max 3 Continuation objects in a chain |
| **Max request/response size** | **1 MB** | Per Continuation callout |

---

## Async Apex resource limits (vs synchronous)

| Resource | Synchronous | **Asynchronous** |
|---|:--:|:--:|
| **Heap size** | 6 MB | **12 MB** |
| **CPU time** | 10,000 ms | **60,000 ms** |
| **Total callouts** | 100 | 100 |
| Single callout timeout | 120 s | 120 s |

---

## Async invocation limits per transaction

| Type | Limit per transaction | Notes |
|---|:--:|:--|
| **@future** | **50** | No sObject/object params; primitives only |
| **Queueable (`System.enqueueJob`)** | **50** | From sync context; only **1** from another Queueable/async |
| **Batch (`Database.executeBatch`)** | **5** concurrent | Org-wide; holding (queued) jobs limited to 100 |
| @future + Queueable + ScheduleBatch | 50 combined-type caps | Each type capped at 50 per transaction |

---

## Quick gotchas

- **DML before callout = error.** Split into separate transactions, or move the callout to `@future(callout=true)` / Queueable / Continuation.
- **`@future(callout=true)`** is required for any callout from a `@future` method.
- **Test classes** must use `HttpCalloutMock` / `WebServiceMock` and wrap in `Test.startTest()` / `Test.stopTest()`; real callouts in tests throw.
- **6 MB / 12 MB** is the heap, and request + response share it. Stream or chunk large payloads.

*Source: [Apex Developer Guide - Callout Limits and Limitations](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_callouts_timeouts.htm) and [Execution Governors and Limits](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_gov_limits.htm). Verified June 2026. See [../05-Outbound-Callouts/07-callout-limits-and-testing.md](../05-Outbound-Callouts/07-callout-limits-and-testing.md).*
