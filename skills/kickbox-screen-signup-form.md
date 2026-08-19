---
name: Screen a signup form with Kickbox
description: >-
  Gate a registration or lead form against fake, disposable and mistyped email addresses using the
  free open disposable lookup and full verification together, without blocking the user on a slow or
  failed call.
api: openapi/kickbox-open-api-openapi.yml
operations: [isDisposable, verifyEmail]
generated: '2026-08-13'
method: generated
source: >-
  openapi/kickbox-open-api-openapi.yml, openapi/kickbox-disposable-openapi.json,
  openapi/kickbox-verification-api-openapi.yml, conventions/kickbox-conventions.yml,
  sandbox/kickbox-sandbox.yml, https://docs.kickbox.com/docs/using-the-api
---

# Screen a signup form

The goal at point-of-capture is not a perfect verdict — it is to stop obvious junk, catch typos while
the user is still on the page, and never block a real signup because an API call was slow.

## The two-stage shape

Kickbox gives you a free unauthenticated check and a paid authoritative one. Use them in that order.

### Stage 1 — `isDisposable` (free, no key, no credits)

`GET /v1/disposable/{email}` on `https://open.kickbox.com`

```
GET https://open.kickbox.com/v1/disposable/example@mailinator.com
→ {"disposable": true}
```

This endpoint requires **no authentication** — Kickbox's own published spec for it declares
`"security": [{}]`. It costs nothing and consumes no verification credits, so run it on every
submission. It accepts an address or a bare domain.

If `disposable` is `true`, you can reject or challenge immediately without spending a credit. That is
the whole point of doing this first.

Kickbox's first-party SDKs point this operation at `open.kickbox.io`; Kickbox's own published OpenAPI
uses `open.kickbox.com`. Both hosts answer. Prefer `open.kickbox.com` — it is the one in the spec the
provider publishes.

### Stage 2 — `verifyEmail` (authoritative, costs a credit)

`GET /v2/verify` on `https://api.kickbox.com` — or `https://api.eu.kickbox.com` for an EU-only
account.

Run this on everything that survives stage 1. Set `timeout` deliberately: the default is `6000` ms
and the maximum is `30000`, but a signup form should not wait six seconds. Pass something in the
1000–3000 range and decide in advance what you do when it returns `unknown`.

## Decide from the response

```json
{ "result": "risky", "reason": "low_quality", "role": false, "free": true,
  "disposable": false, "accept_all": false, "did_you_mean": null,
  "sendex": 0.5, "email": "jane@example.com", "user": "jane", "domain": "example.com",
  "success": true, "message": null }
```

A workable policy for a signup form:

| `result` | Action |
|---|---|
| `deliverable` | Accept |
| `undeliverable` | Reject, and if `did_you_mean` is set, show that correction rather than a bare error |
| `risky` | Accept but flag — use `role`, `free`, `disposable`, `accept_all` and `sendex` to decide whether to require confirmation |
| `unknown` | **Accept.** Kickbox could not reach a verdict; the credit is refunded. Queue the address for re-verification later |

Never fail a signup closed on `unknown`. `timeout`, `no_connect`, `unavailable_smtp` and
`unexpected_error` all mean "we could not tell", not "this address is bad".

Remember `success` is about the API call, not the address. Do not branch on it for the verdict.

## Two constraints that shape the implementation

**You cannot call this from the browser.** Kickbox's verification endpoints do not allow
cross-domain requests, so React/Vue/Angular code cannot hit them directly. Put the call behind your
own server endpoint. (The open disposable lookup is the free-tier surface, but treat both the same
way — routing through your server also keeps your API key out of the client.)

**A public form is a credit-spending surface.** Anyone who can submit your form can spend your
verification credits. Mitigate it:

- Do stage 1 first — the free check absorbs a large share of junk at zero cost.
- Rate-limit and bot-protect the form itself before you ever call Kickbox.
- Enable auto-recharge so a burst does not silently break the form when credits hit zero (the API
  returns HTTP 403 `"Insufficient balance"`).
- Turn on **Spike Detection Alerts** for the API key backing the form. Kickbox will email you when
  verification volume crosses a threshold you set per minute or per hour — it is built for exactly
  this abuse case. It is in beta, so request access at Settings > Alerts > Request Access.

## Respect the ceilings

25 requests in flight per IP and 8,000 per clock minute, with **no** rate-limit response headers to
tell you where you stand. On a form, that means bounding concurrency in your server-side client, not
reacting to 429s. A 429 from the concurrency ceiling can be retried immediately; a 429 from the
per-minute budget needs to wait for the minute boundary.

## Build it against the sandbox

Create a `test_` key and drive every path with no credit cost. `deliverable@example.com`,
`did-you-mean@example.com`, `disposable@example.com`, `role@example.com`, `timeout@example.com` and
`insufficient-balance@example.com` will exercise accept, typo-correction, junk-rejection, flagging,
the unknown path and the out-of-credits path respectively. The plus-tag form
(`realuser+timeout@example.com`) lets you trigger a case without changing the domain under test.
