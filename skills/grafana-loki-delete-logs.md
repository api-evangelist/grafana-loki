---
name: grafana-loki-delete-logs
description: Submit a Grafana Loki log deletion request and cancel it inside the stated 24-hour window.
api: Grafana Loki HTTP API
version: v1
generated: '2026-08-27'
method: generated
source: https://grafana.com/docs/loki/latest/reference/loki-http-api/
operations:
  - POST /loki/api/v1/delete
  - GET /loki/api/v1/delete
  - DELETE /loki/api/v1/delete
---

# Delete logs from Grafana Loki — and take it back

This is the only destructive operation in Loki that is reversible, and the reversal has a
deadline. Read the deadline before you submit.

## Preconditions

- The endpoints are served by the `compactor`, `backend` and `all` components.
- The tenant's `deletion_mode` must be `filter-and-delete` or `filter-only`. With deletion
  disabled the request is refused.
- On Grafana Cloud Logs / Grafana Enterprise Logs the access policy behind your token must
  carry `logs:delete` **for the tenant named in the Basic auth user field**. A token with
  `logs:read` and `logs:write` is not enough.

## 1. Rehearse the selector

The deletion request takes a LogQL selector. Run it as a *query* first, over the same
`start`/`end`, and look at what comes back. That is your dry run — there is no `dry_run`
parameter on the delete endpoint.

```
GET /loki/api/v1/query_range?query=<selector>&start=<ns>&end=<ns>&limit=100
```

## 2. Submit the deletion request

```
POST /loki/api/v1/delete?query=<selector>&start=<ns>&end=<ns>
X-Scope-OrgID: <tenant>
```

**Record the returned `request_id`.** It is the only handle that can cancel this. Loki mints no
other identifier anywhere in this API.

## 3. Confirm it is queued

```
GET /loki/api/v1/delete
X-Scope-OrgID: <tenant>
```

Returns both processed and unprocessed requests. It does **not** list cancelled requests —
those are removed from storage entirely, so an absent request is a cancelled one, not a lost one.

## 4. Cancel it, if you must — the window

```
DELETE /loki/api/v1/delete?request_id=<request_id>
X-Scope-OrgID: <tenant>
```

From the Loki HTTP API reference:

> Loki allows cancellation of delete requests until the requests are picked up for processing.
> It is controlled by the `delete_request_cancel_period` YAML configuration or the equivalent
> command line option when invoking Loki. To cancel a delete request that has been picked up for
> processing or is partially complete, pass the `force=true` query parameter to the API.

- **Default `delete_request_cancel_period`: 24h.**
- After the cancel period elapses and the compactor picks the request up, a plain cancel fails.
- `force=true` cancels a partially completed request — it stops further deletion; it does not
  restore lines already removed.

Confirm the real `delete_request_cancel_period` on the cluster before relying on 24 hours: like
every Loki limit it is operator-configurable.

## 5. If you missed the window

There is no restore. The remaining recourse is whatever backup the operator keeps outside Loki.
Treat an expired cancel window as permanent.

## Escalation rule for agents

Do not submit a deletion request without explicit human confirmation of the selector and the
time range. The reversal window is 24 hours by default and the operation is otherwise permanent.
