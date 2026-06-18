# REST API HTTP Status Codes One-Pager (v66.0)

> HTTP status codes Salesforce REST APIs return, what each means, and the common Salesforce cause. Full detail in **[../04-Inbound-APIs/01-standard-rest-api.md](../04-Inbound-APIs/01-standard-rest-api.md)**.

---

## Success (2xx)

| Code | Meaning | Typical Salesforce cause |
|:--:|---|:--|
| **200** | OK | `GET` / `PATCH` / `HEAD` succeeded; body returned |
| **201** | Created | `POST` created a record; `Location` + new `Id` returned |
| **204** | No Content | `DELETE` or `PATCH` succeeded with no body to return |

---

## Redirection (3xx)

| Code | Meaning | Typical Salesforce cause |
|:--:|---|:--|
| **300** | Multiple Choices | External ID in upsert matched **more than one** record |
| **304** | Not Modified | Conditional `GET` with `If-Modified-Since` / `If-None-Match`; resource unchanged, use cache |

---

## Client errors (4xx)

| Code | Meaning | Typical Salesforce cause |
|:--:|---|:--|
| **400** | Bad Request | Malformed request, invalid field/SOQL, bad JSON/XML, validation rule failure |
| **401** | Unauthorized | Session expired or invalid OAuth token (`INVALID_SESSION_ID`); re-authenticate |
| **403** | Forbidden | Valid session but no permission, or `REQUEST_LIMIT_EXCEEDED` (24h API cap) |
| **404** | Not Found | Record/resource gone, wrong `Id`, wrong API version, or no object access |
| **405** | Method Not Allowed | HTTP verb not supported on that resource (e.g. `POST` to a record URL) |
| **409** | Conflict | Concurrent update conflict; cannot complete due to a conflicting change |
| **412** | Precondition Failed | An `If-*` header condition was not met |
| **415** | Unsupported Media Type | Bad `Content-Type` / entity format; sent XML where JSON expected (or vice versa) |
| **431** | Request Header Fields Too Large | Headers (often the auth/cookie header) exceed the allowed size |

---

## Server errors (5xx)

| Code | Meaning | Typical Salesforce cause |
|:--:|---|:--|
| **500** | Internal Server Error | Salesforce-side error, often an unhandled Apex exception in a trigger/REST class |
| **503** | Service Unavailable | Server busy / too many requests; back off and retry with exponential backoff |

---

## Quick reading guide

- **2xx** = done. **204** means success but check there is no body before parsing.
- **300** on upsert = your external ID is **not unique**; fix the data or the matching field.
- **401 vs 403**: 401 = *who are you* (bad/expired token); 403 = *not allowed* or *over your limit*.
- **4xx** = fix the request (yours). **5xx** = retry (theirs), with backoff.
- Error body returns an array of `{ "message", "errorCode", "fields" }` - log the `errorCode`, not just the HTTP status.

*Source: [REST API Developer Guide - Status Codes and Error Responses](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/errorcodes.htm). Verified June 2026. See [../04-Inbound-APIs/01-standard-rest-api.md](../04-Inbound-APIs/01-standard-rest-api.md).*
