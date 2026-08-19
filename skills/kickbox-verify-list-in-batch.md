---
name: Verify an email list with the Kickbox batch API
description: >-
  Submit up to a million addresses as a single asynchronous job, track it to completion by callback
  or polling, and retrieve the results CSV before its signed URL expires.
api: openapi/kickbox-batch-api-openapi.yml
operations: [verifyBatch, getBatchResults, getBalance]
generated: '2026-08-13'
method: generated
source: >-
  openapi/kickbox-batch-api-openapi.yml, openapi/kickbox-account-api-openapi.yml,
  asyncapi/kickbox-batch-webhooks.yml, conventions/kickbox-conventions.yml,
  https://docs.kickbox.com/docs/batch-verification-api
---

# Verify an email list in batch

Use this for lists. Do not loop the single-verification operation over a list — the batch endpoint
exists precisely so you do not have to, and looping will collide with the 25-parallel and
8,000/minute ceilings.

## Step 1 — submit the job with `verifyBatch`

`PUT /v2/verify-batch`

- **Content-Type must be `text/csv`.** This endpoint is the one exception to the API's
  `application/json` rule; sending JSON returns HTTP 415.
- Body is one email address per line, or a CSV file.
- Limits: **1,000,000 addresses** and **250MB** per job.
- Auth: `apikey` query parameter, or `Authorization: Bearer <key>`.

```http
PUT https://api.kickbox.com/v2/verify-batch?apikey=YOUR_API_KEY
X-Kickbox-Callback: https://your.app/hooks/kickbox
X-Kickbox-Filename: october-newsletter-list
Content-Type: text/csv

bill.lumbergh@initech.com
peter.gibbons@initech.com
milton.waddams@initech.com
```

Optional headers:

| Header | Effect |
|---|---|
| `X-Kickbox-Callback` | Kickbox POSTs the completion payload to this URL when the job finishes |
| `X-Kickbox-Filename` | Names the job and the file you download |

If you send a CSV, Kickbox asks for: one email per line, comma separators (not tabs or semicolons),
all values double-quoted, internal double quotes escaped with a backslash
(`"Matthew \" The Rock \" Smith"`), and newline row separators.

The response gives you the job id — keep it:

```json
{ "id": 123, "success": true, "message": null }
```

**There is no idempotency key on this endpoint.** A retried submission creates a second job and
consumes credits a second time. Record the returned `id` before you retry anything, and treat a
timed-out submission as *possibly succeeded* rather than failed.

## Step 2 — wait for completion

Two ways. Prefer the callback; keep polling as the fallback.

### Callback (if you set `X-Kickbox-Callback`)

Kickbox POSTs JSON to your URL on completion. Your endpoint **must** return a 2xx status.

```json
{
  "id": 123,
  "name": "Batch API Process - 05-12-2018-01-58-08",
  "download_url": "https://url.to.your.csv",
  "stats": { "deliverable": 2, "undeliverable": 1, "risky": 0, "unknown": 0,
             "sendex": 0.35, "addresses": 3 },
  "created_at": "2018-05-12T18:58:08.000Z",
  "status": "completed", "error": null, "duration": 42
}
```

**The callback is not signed.** No signature header, shared secret or timestamp is documented, so you
cannot verify it came from Kickbox. Treat it as an untrusted nudge: use it to wake up, then confirm
state with `getBatchResults` before acting on it.

### Polling with `getBatchResults`

`GET /v2/verify-batch/{id}` returns one of four statuses.

- `starting` — accepted, not begun.
- `processing` — includes a `progress` object with `deliverable`, `undeliverable`, `risky`,
  `unknown`, `total` and `unprocessed`. Drive a progress bar from `unprocessed` against `total`.
- `completed` — includes `stats` and `download_url`.
- `failed` — `error` carries a description string.

Poll on a sane interval and back off; there are no rate-limit headers to guide you, and the same
25-parallel / 8,000-per-minute ceilings apply to these calls.

## Step 3 — download the results

`download_url` is a signed URL **valid for one hour**. If it expires, do not retry the URL — call
`getBatchResults` again to mint a freshly signed one. Download promptly, or fetch the URL only at the
moment you are ready to stream it to storage.

The per-address results exist only in that CSV. They are never addressable as API resources, so
whatever you do not persist is gone.

## Step 4 — reconcile

`stats` gives you `deliverable`, `undeliverable`, `risky`, `unknown`, `addresses` and an aggregate
`sendex`. Reconcile `addresses` against the number of lines you submitted before you act on the
result — a short count means rows were dropped at parse time.

Addresses that came back `unknown` are refunded, so re-submit them in a later job rather than writing
them off. Check your balance with `getBalance` (or the `X-Kickbox-Balance` header) before submitting
a large list: at zero credits the API returns HTTP 403 with `"Insufficient balance"`. Enable
auto-recharge if you run batches unattended.
