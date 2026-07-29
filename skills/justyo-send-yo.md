---
name: Send a Yo notification
description: Push a single-tap Yo notification to one subscriber or all subscribers of a Yo API account.
api: openapi/justyo-yo-openapi.yml
operations: [sendYo, sendYoAll, getSubscribersCount]
---

# Send a Yo notification

Use the Yo API to push a lightweight "Yo" notification to subscribers of an API account.

> Note: the Yo service is defunct (see `lifecycle/`). This skill documents the
> historically published contract.

## Auth
- Obtain an `api_token` for a registered API username from the Yo Developer Hub.
- Pass `api_token` as a form field on POST and as a query parameter on GET.

## Steps
1. **Check reach** — call `getSubscribersCount` (`GET /subscribers_count/?api_token=...`)
   to see how many subscribers the account has.
2. **Send to one subscriber** — call `sendYo` (`POST /yo/`) with `api_token`,
   `username`, and an optional `link`. Respect the rate limit: one Yo per user per minute.
3. **Broadcast** — call `sendYoAll` (`POST /yoall/`) with `api_token` and an optional
   `link` to notify every subscriber at once.

## Rules
- Content type for POST is `application/x-www-form-urlencoded`.
- A `401` means the `api_token` is missing or invalid.
- A `429` means you exceeded the one-Yo-per-user-per-minute limit — back off and retry.
- There is no idempotency key; do not retry a `200` send.
