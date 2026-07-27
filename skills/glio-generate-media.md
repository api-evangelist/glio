---
name: Generate AI media with Glio
description: Create a video, image, or audio generation job on Glio, poll it to completion, and read the result.
api: openapi/glio-openapi-original.json
operations: [createJob, getJob, deleteJob]
---

# Generate AI media with Glio

Glio is a unified API for AI media generation. You submit a job for a model
(video, image, or audio), then poll until it finishes. Billing is pay-per-use in
GL tokens (1 GL = $0.01 USD).

## Auth
Send `Authorization: Bearer <API_KEY>` on every request. Keys are issued from the
dashboard at https://glio.io/app/. See `authentication/glio-authentication.yml`.

## Steps
1. **Pick a model.** Use a model alias such as `kling-v2.6-pro-t2v` (video),
   `gpt-image-2-t2i` (image), or `suno` (audio). Exact per-model parameters live
   at `https://api.glio.io/openapi/models/{alias}.json`.
2. **Create the job** — `createJob` (`POST /v1/jobs`) with body
   `{"model": "<alias>", "params": {...}}`. A `202` returns `{"id": "job_...",
   "status": "pending"}`. Keep the `id`.
3. **Poll** — `getJob` (`GET /v1/jobs/{job_id}`) every 10-15 seconds until
   `status` is `completed` or `failed`. On `completed`, read `result`.
4. **(Optional) Clean up** — `deleteJob` (`DELETE /v1/jobs/{job_id}`) removes a
   completed or failed job. Deleting a still-running job returns `409`.

## Rules
- Job creation is **not** idempotent — do not blindly retry `createJob`; retry
  by re-polling the existing `id`.
- Handle `402 Insufficient credits` (top up GL balance) and `404` (bad model
  alias or job id). See `errors/glio-problem-types.yml`.
- List/paginate with `GET /v1/jobs?limit=&offset=&status=` (offset pagination).
