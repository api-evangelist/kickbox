# Kickbox

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Kickbox is an email verification and list cleaning service that helps senders improve deliverability by detecting invalid, disposable, role-based, and risky email addresses before they enter mailing lists. The platform offers real-time single verification, bulk batch verification, and a deliverability monitoring suite. Kickbox provides a simple HTTPS REST API authenticated by API key returning structured verification results with reason codes.

**Areas:** Email Verification · Deliverability · Data Quality · Email

## Kickbox on APIs.io

Everything profiled for Kickbox is published on APIs.io rather than in this repository. Start at the provider page — **[apis.io/providers/kickbox/](https://apis.io/providers/kickbox/)** — or go straight to one of the APIs or properties below.

### APIs (6)

- [Kickbox Email Verification API](https://apis.io/apis/kickbox/verification-api/) — REST API for verifying the deliverability of email addresses in real time. Returns a result (deliverable, undeliverable, risky, unknown), a reason code,…
- [Kickbox Account API](https://apis.io/apis/kickbox/kickbox-account-api/) — Account balance and metadata.
- [Kickbox Batch API](https://apis.io/apis/kickbox/kickbox-batch-api/) — Bulk CSV email verification.
- [Kickbox Open API](https://apis.io/apis/kickbox/kickbox-open-api/) — Free disposable-domain lookup (no auth).
- [Kickbox Verification API](https://apis.io/apis/kickbox/kickbox-verification-api/) — Real-time email verification.
- [Kickbox Disposable API](https://apis.io/apis/kickbox/kickbox-disposable-api/) — The Disposable API from Kickbox — 1 operation(s) for disposable.

### Published properties

Each property below is a page on APIs.io carrying the machine-readable artifact and what it says.

#### AsyncAPI (1)

Event-driven and webhook contracts.

- [Kickbox Batch Webhooks](https://apis.io/asyncapis/kickbox/kickbox-batch-webhooks/)

#### MCP servers (1)

Model Context Protocol servers that expose the API to agents.

- [kickbox-docs](https://apis.io/servers/kickbox/kickbox-docs/)

#### Agentic access (1)

Which operations an agent can call, which act, and which need a human in the loop.

- [Kickbox Agentic Access](https://apis.io/agentic-access/kickbox/kickbox-agentic-access/)

#### Collections (5)

Postman and Open Collection request collections.

- [Kickbox Email Verification API](https://apis.io/collections/kickbox/open-kickbox/)
- [Kickbox Email Verification Account API](https://apis.io/collections/kickbox/open-kickbox-account-api/)
- [Kickbox Email Verification Account Batch API](https://apis.io/collections/kickbox/open-kickbox-batch-api/)
- [Kickbox Email Verification Account Open API](https://apis.io/collections/kickbox/open-kickbox-open-api/)
- [Kickbox Email Account Verification API](https://apis.io/collections/kickbox/open-kickbox-verification-api/)

#### Overlays (4)

OpenAPI overlays, including conversational phrasing for each operation.

- [Kickbox Account Api Overlay](https://apis.io/overlays/kickbox/kickbox-account-api-overlay/)
- [Kickbox Batch Api Overlay](https://apis.io/overlays/kickbox/kickbox-batch-api-overlay/)
- [Kickbox Open Api Overlay](https://apis.io/overlays/kickbox/kickbox-open-api-overlay/)
- [Kickbox Verification Api Overlay](https://apis.io/overlays/kickbox/kickbox-verification-api-overlay/)

#### Security (3)

Authentication, trust centre, and vulnerability disclosure.

- [Kickbox Authentication](https://apis.io/security/kickbox/kickbox-authentication/)
- [Kickbox Domain Security](https://apis.io/security/kickbox/kickbox-domain-security/)
- [Kickbox Trust Center](https://apis.io/security/kickbox/kickbox-trust-center/)

#### Plans and pricing (1)

Published plans, tiers and prices.

- [Kickbox Plans Pricing](https://apis.io/plans/kickbox/kickbox-plans-pricing/)

#### Rate limits (1)

Published request limits.

- [Kickbox Rate Limits](https://apis.io/rate-limits/kickbox/kickbox-rate-limits/)

## Where to find this API

Links to the company's own pages. This repository publishes no copy of their specifications — the canonical document is theirs and lives at the URLs below.

- [Website](https://kickbox.com)
- [Documentation](https://docs.kickbox.com)
- [Getting started](https://docs.kickbox.com/docs/using-the-api)
- [API reference](https://docs.kickbox.com/docs/single-verification-api)
- [Pricing](https://kickbox.com/pricing)
- [Sign up](https://app.kickbox.com/signup)
- [Support](https://articles.kickbox.com/en/collections/1840339-kickbox-help-articles)
- [Blog](https://blog.kickbox.com)
- [Compliance](https://docs.kickbox.com/docs/security-and-compliance)
- [Trust centre](https://trust.kickbox.com/)
- [Terms of service](https://docs.kickbox.com/docs/terms-of-service)
- [Privacy policy](https://docs.kickbox.com/docs/privacy-policy)
- [GitHub](https://github.com/kickboxio)
- [Status](https://status.kickbox.com/)

## API Evangelist rating

**Kin Score 55.8** — strong · rubric 0.25.0 · scored 2026-10-23

| Facet | Score |
|---|---|
| Discoverability | 66.7 |
| Contract quality | 54.4 |
| Contract governance | 4.5 |
| Operational transparency | 47.4 |
| Developer ergonomics | 72.0 |
| Access clarity | 69.7 |

**Agent readiness 40.3** — agent-ready (gated down from agent-native)

| Agent-readiness dimension | Result |
|---|---|
| Spec Presence | yes |
| Agentic Access | derived (partial credit) |
| Reversibility Documented | — |
| MCP Server | verified |
| Auth Clarity | bearer (partial credit) |
| Idempotency | — |
| Error Semantics | documented (partial credit) |
| OpenAPI Examples | partial |
| Rate Limit Signal | documented (partial credit) |
| Event Surface Described | yes |
| Agent Skills | derived (partial credit) |
| Well Known Catalog | — |
| Consent Identity | — |
| Agent Card | — |
| Dry Run Mode | — |
| Delegated Identity | — |
| Protected Resource Metadata | — |
| Dynamic Client Registration | — |
| Agentic Commerce | — |
| Pagination Ratio | na |
| Operation Distinctiveness | distinct |

**Regulatory alignment 23.0** — Horizontal (data, software, accessibility, platform)

The rubric is published at [github.com/api-evangelist/kin-score](https://github.com/api-evangelist/kin-score). The per-check breakdown behind these numbers — which checks were earned, partial or missed — along with score history and how this provider compares to its peers, is available at [apis.io/providers/kickbox/](https://apis.io/providers/kickbox/).

## How this profile is made

API Evangelist profiles a company's public API surface the way a developer would find it: the website, developer portal and documentation, the specifications it publishes, its public repositories, and its pricing, status and changelog pages. From that it derives the properties above — contracts split per API, workflows, schemas, collections, governance rules, plans and rate limits — and scores them against the published [Kin Score rubric](https://github.com/api-evangelist/kin-score).

The artifacts are published on [APIs.io](https://apis.io), not in this repository. This repository is the public record for Kickbox: the place to say something is wrong, missing or out of date, and to ask for a re-score or a removal.

## Open an issue

- **Something about this listing** — a wrong description, a missing API, a stale artifact or a question about the rating: [open an issue on this repository](https://github.com/api-evangelist/kickbox/issues/new).
- **A new API, or one that is not in the catalog yet** — [submit it on APIs.io](https://apis.io/add/).
- **Anything else** — [the APIs.io inbox](https://github.com/api-search/inbox/issues/new) or [info@apievangelist.com](mailto:info@apievangelist.com).
