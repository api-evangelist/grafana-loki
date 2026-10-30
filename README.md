# Grafana Loki

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

Grafana Loki is Grafana Labs' open source log aggregation system — "like Prometheus, but for logs." Rather than full-text indexing log contents, Loki indexes only a small set of labels per log stream and stores the compressed lines in object storage, which is what makes it cheap to run at scale. It is queried with LogQL, a Prometheus-inspired query language, over an HTTP API that covers ingestion, querying, label and pattern discovery, live tailing over WebSocket, a Prometheus-compatible ruler for alerting and recording rules, and a cancellable log deletion API. Loki accepts native pushes and OpenTelemetry OTLP/HTTP logs, publishes proto3 gRPC service definitions for its push and query paths, and ships a first-party CLI (LogCLI), Helm charts, a Kubernetes operator, and Docker images. It is licensed AGPL-3.0 and self-hosted; the same engine is sold as the managed Grafana Cloud Logs service and as the self-managed Grafana Enterprise Logs distribution.

**Areas:** Company · Logs · Logging · Log Aggregation · Observability · Monitoring · Open Source · LogQL · OpenTelemetry · Telemetry · Kubernetes · Cloud-Native

## Grafana Loki on APIs.io

Everything profiled for Grafana Loki is published on APIs.io rather than in this repository. Start at the provider page — **[apis.io/providers/grafana-loki/](https://apis.io/providers/grafana-loki/)** — or go straight to one of the APIs or properties below.

### APIs (3)

- [Loki HTTP API](https://apis.io/apis/grafana-loki/loki-http-api/) — The Grafana Loki HTTP API — 40 documented endpoints for pushing logs (native and OTLP), querying with LogQL over instants and ranges, discovering labels,…
- [Grafana Cloud Logs API](https://apis.io/apis/grafana-loki/grafana-cloud-logs-api/) — Grafana Cloud Logs is the fully managed Grafana Loki service. It serves the same Loki HTTP API on a per-stack host of the form…
- [Grafana MCP Server — Loki tools](https://apis.io/apis/grafana-loki/grafana-mcp-loki-tools/) — Grafana Loki reaches MCP clients through the Grafana Labs MCP servers, which register a Loki tool family (query_loki_logs, list_loki_label_names,…

### Published properties

Each property below is a page on APIs.io carrying the machine-readable artifact and what it says.

#### MCP servers (1)

Model Context Protocol servers that expose the API to agents.

- [Grafana MCP server (Loki tools)](https://apis.io/servers/grafana-loki/grafana-mcp-server-loki-tools/)

#### Security (4)

Authentication, trust centre, and vulnerability disclosure.

- [Grafana Loki Authentication](https://apis.io/security/grafana-loki/grafana-loki-authentication/)
- [Grafana Loki Domain Security](https://apis.io/security/grafana-loki/grafana-loki-domain-security/)
- [Grafana Loki Trust Center](https://apis.io/security/grafana-loki/grafana-loki-trust-center/)
- [Grafana Loki Vulnerability Disclosure](https://apis.io/security/grafana-loki/grafana-loki-vulnerability-disclosure/)

#### OAuth scopes (1)

The permissions the API asks for.

- [Grafana Loki Scopes](https://apis.io/scopes/grafana-loki/grafana-loki-scopes/)

#### Plans and pricing (1)

Published plans, tiers and prices.

- [Grafana Loki Plans Pricing](https://apis.io/plans/grafana-loki/grafana-loki-plans-pricing/)

#### Rate limits (1)

Published request limits.

- [Grafana Loki Rate Limits](https://apis.io/rate-limits/grafana-loki/grafana-loki-rate-limits/)

## Where to find this API

Links to the company's own pages. This repository publishes no copy of their specifications — the canonical document is theirs and lives at the URLs below.

- [Website](https://grafana.com/oss/loki/)
- [Documentation](https://grafana.com/docs/loki/latest/)
- [Getting started](https://grafana.com/docs/loki/latest/get-started/)
- [API reference](https://grafana.com/docs/loki/latest/reference/loki-http-api/)
- [Pricing](https://grafana.com/pricing/)
- [Sign up](https://grafana.com/auth/sign-up/create-user)
- [Support](https://community.grafana.com/)
- [Blog](https://grafana.com/blog/)
- [Security](https://grafana.com/security/)
- [Compliance](https://grafana.com/legal/security-compliance/)
- [Terms of service](https://grafana.com/legal/terms/)
- [Privacy policy](https://grafana.com/legal/privacy-policy/)
- [GitHub](https://github.com/grafana)
- [Status](https://status.grafana.com/)

## API Evangelist rating

**Kin Score 66.1** — strong · rubric 0.25.0 · scored 2026-10-23

| Facet | Score |
|---|---|
| Discoverability | 80.0 |
| Contract quality | 24.0 |
| Contract governance | 18.2 |
| Operational transparency | 84.2 |
| Developer ergonomics | 80.4 |
| Access clarity | 92.1 |

**Agent readiness 49.8** — agent-native

| Agent-readiness dimension | Result |
|---|---|
| Spec Presence | yes |
| Agentic Access | — |
| Reversibility Documented | — |
| MCP Server | verified |
| Auth Clarity | served (partial credit) |
| Idempotency | documented (partial credit) |
| Error Semantics | documented (partial credit) |
| OpenAPI Examples | — |
| Rate Limit Signal | documented (partial credit) |
| Event Surface Described | — |
| Agent Skills | derived (partial credit) |
| Well Known Catalog | — |
| Consent Identity | — |
| Agent Card | — |
| Dry Run Mode | — |
| Delegated Identity | served (partial credit) |
| Protected Resource Metadata | verified |
| Dynamic Client Registration | yes |
| Agentic Commerce | — |
| Pagination Ratio | na |
| Operation Distinctiveness | na |

**Regulatory alignment 45.1** — Horizontal (data, software, accessibility, platform)

The rubric is published at [github.com/api-evangelist/kin-score](https://github.com/api-evangelist/kin-score). The per-check breakdown behind these numbers — which checks were earned, partial or missed — along with score history and how this provider compares to its peers, is available at [apis.io/providers/grafana-loki/](https://apis.io/providers/grafana-loki/).

## How this profile is made

API Evangelist profiles a company's public API surface the way a developer would find it: the website, developer portal and documentation, the specifications it publishes, its public repositories, and its pricing, status and changelog pages. From that it derives the properties above — contracts split per API, workflows, schemas, collections, governance rules, plans and rate limits — and scores them against the published [Kin Score rubric](https://github.com/api-evangelist/kin-score).

The artifacts are published on [APIs.io](https://apis.io), not in this repository. This repository is the public record for Grafana Loki: the place to say something is wrong, missing or out of date, and to ask for a re-score or a removal.

## Open an issue

- **Something about this listing** — a wrong description, a missing API, a stale artifact or a question about the rating: [open an issue on this repository](https://github.com/api-evangelist/grafana-loki/issues/new).
- **A new API, or one that is not in the catalog yet** — [submit it on APIs.io](https://apis.io/add/).
- **Anything else** — [the APIs.io inbox](https://github.com/api-search/inbox/issues/new) or [info@apievangelist.com](mailto:info@apievangelist.com).
