---
name: Verify a single email address with Kickbox
description: >-
  Verify one email address in real time at point-of-capture, read the result correctly, and handle
  the credit, rate-limit and error conditions the Kickbox API actually returns.
api: openapi/kickbox-verification-api-openapi.yml
operations: [verifyEmail, getBalance]
generated: '2026-08-13'
method: generated
source: >-
  openapi/kickbox-verification-api-openapi.yml, openapi/kickbox-account-api-openapi.yml,
  conventions/kickbox-conventions.yml, errors/kickbox-problem-types.yml,
  sandbox/kickbox-sandbox.yml, https://docs.kickbox.com/docs/single-verification-api
---

# Verify a single email address

Use this when you have one address and need a verdict now — a signup form, a CRM record, a
point-of-capture check. For lists, use the batch skill instead; do not loop this operation over a
list.

## Before you start

- You need a Kickbox API key. Production keys begin with `live_`, sandbox keys with `test_`. A key's
  mode is fixed at creation and cannot be changed.
- Base URL is `https://api.kickbox.com`. **If the account is an EU-only account** (it signs in at
  `app.eu.kickbox.com`), the base URL is `https://api.eu.kickbox.com` instead. This is a property of
  the account and cannot be discovered at runtime — it must be configured.
- You cannot call this API from browser JavaScript. Verification endpoints do not allow cross-origin
  requests; proxy the call server-side.

## Step 1 — call `verifyEmail`

`GET /v2/verify`

| Parameter | Required | Notes |
|---|---|---|
| `email` | yes | The address to verify, URL-encoded |
| `apikey` | yes | Your API key — or omit it and send `Authorization: Bearer <key>` instead |
| `timeout` | no | Milliseconds. Default `6000`, maximum `30000` |

```
GET https://api.kickbox.com/v2/verify?email=bill.lumbergh%40gamil.com&apikey=YOUR_API_KEY
```

Prefer the header form over the query parameter where your client allows it — a key in a query
string ends up in proxy and access logs.

## Step 2 — read the response correctly

This is where integrations most often go wrong.

**`success` is not the verdict.** `success` tells you whether the API *call* worked. An address that
is undeliverable still returns `success: true`. The verdict is `result`.

```json
{
  "result": "undeliverable",
  "reason": "rejected_email",
  "role": false, "free": false, "disposable": false, "accept_all": false,
  "did_you_mean": "bill.lumbergh@gmail.com",
  "sendex": 0.23,
  "email": "bill.lumbergh@gamil.com", "user": "bill.lumbergh", "domain": "gamil.com",
  "success": true, "message": null
}
```

Branch on `result`:

- `deliverable` — accept.
- `undeliverable` — reject. `reason` distinguishes bad syntax (`invalid_email`), a dead domain
  (`invalid_domain`), a rejected mailbox (`rejected_email`) and a bad SMTP response (`invalid_smtp`).
- `risky` — your policy call. `reason` is `low_quality` or `low_deliverability`; check the `role`,
  `free`, `disposable` and `accept_all` flags and the `sendex` score to decide.
- `unknown` — Kickbox could not reach a verdict (`timeout`, `no_connect`, `unavailable_smtp`,
  `unexpected_error`). **Do not treat this as a rejection.** The credit is refunded, so retry later
  rather than discarding the address.

Two fields worth using:

- `did_you_mean` — when non-null, offer the correction to the user instead of a bare rejection. This
  is the highest-value field on the response for a signup form.
- `sendex` — quality from 0 to 1. Kickbox's own guidance: for marketing email, 0.70+ is good,
  0.40–0.69 fair, below 0.40 poor. For transactional email the bands are 0.55+, 0.20–0.54, below
  0.20. Never use `sendex` alone as a reject signal.

Log the address the user actually typed. The `email` field is a *normalized* form
(`BoB@example.com` → `bob@example.com`), which is functionally equivalent but not identical.

## Step 3 — watch the balance

Every authenticated response carries `X-Kickbox-Balance` (remaining credits) and
`X-Kickbox-Response-Time` (processing ms). Read `X-Kickbox-Balance` off the response you already
have; only call `getBalance` (`GET /v2/balance`) when you need the figure outside a verification
call. Enable auto-recharge in account settings so the integration does not run dry.

## Errors

There is no RFC 9457 problem+json here — just an HTTP status and `{"success": false, "message":
"..."}`. There is no machine-readable error code, so 403 requires reading `message` to tell an
invalid key from an exhausted balance.

| Status | Meaning | Do |
|---|---|---|
| 400 | Bad request | Check `email` is present and URL-encoded |
| 403 | Invalid API key **or** balance depleted | Read `message`; recharge or fix the key |
| 415 | Wrong content type | Send `application/json` for JSON bodies |
| 429 | Rate limit exceeded | Retry immediately if you tripped the concurrency ceiling; otherwise back off |
| 500 | Server error | Retry with backoff |

## Rate limits

- **25 requests in flight per IP.** A slot frees as soon as you read the response and close the
  socket. Exceeding it returns 429 and the docs say you may retry immediately.
- **8,000 requests per clock minute.** This budget resets on the minute boundary.

Kickbox returns **no** `RateLimit-*`, `X-RateLimit-*` or `Retry-After` headers, so you must enforce
both ceilings client-side. Bound your concurrency at 25 and your rate below 8,000/min rather than
discovering the limits by hitting them.

## Test it first

Create a sandbox key (`test_` prefix) and drive every branch without spending credits. The outcome is
selected by the address you send — either `<trigger>@domain` or `anything+<trigger>@domain`:

| Send | Get |
|---|---|
| `deliverable@example.com` | `deliverable` / `accepted_email` |
| `rejected-email@example.com` | `undeliverable` / `rejected_email` |
| `invalid-domain@example.com` | `undeliverable` / `invalid_domain` |
| `accept-all@example.com` | `risky` / `low_deliverability` |
| `role@example.com` | `risky`, `role: true` |
| `disposable@example.com` | `risky`, `disposable: true` |
| `timeout@example.com` | `unknown` / `timeout` |
| `did-you-mean@example.com` | `undeliverable` with a `did_you_mean` value |
| `insufficient-balance@example.com` | HTTP 403, `"Insufficient balance"` |

Anything else returns `deliverable` by default. All sandbox results are fake — they exist to exercise
your branching, not to tell you anything about the address.
