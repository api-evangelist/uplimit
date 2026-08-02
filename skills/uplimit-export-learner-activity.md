---
name: Export learner activity
description: Run an asynchronous learner-activity export for one or more sessions
  and download the results.
api: openapi/uplimit-organization-openapi-original.yml
operations:
  - POST /v1/GetLearnerActivity/start
  - GET /v1/GetLearnerActivity/get
  - GET /v1/ListEnrollmentsInSession
generated: '2026-07-21'
method: generated
note: The published spec declares no operationIds; operations are referenced as
  METHOD path (see overlays/uplimit-organization-overlay.yaml for suggested ids).
---

# Export learner activity

All requests use `https://uplimit.com/api/organization/` with
`Authorization: Bearer <org token>`.

1. **Start the job** — `POST /v1/GetLearnerActivity/start` with an optional
   `sessionIds` array (omit it to export all sessions). The response returns a
   `jobId`.
2. **Poll for the result** — `GET /v1/GetLearnerActivity/get?jobId=<jobId>`.
   When the job completes, the response contains a `url` to download the
   exported data. Poll with backoff; a 404 means the job was not found.
3. **Spot-check completion state** — for a live view without an export,
   `GET /v1/ListEnrollmentsInSession?uplimitSessionId=...` returns each
   enrollment with `sessionCompletionStatus` (PENDING | COMPLETED), paginated
   by `skip`/`take` with `totalCount`.

Errors arrive as `{ "error": "<message>" }` (400/401/404/405).
