# Asynchronous Ingestion and Weather

## Worker roles

| Component | Work | Privilege boundary |
| --- | --- | --- |
| `api-worker` | Public search/fit and user-scoped API; forwards user JWT to Supabase | Public Supabase key only; no `service_role`, provider secrets, or R2 binding |
| `cron-worker` | Scheduled provider jobs, deletion queue, R2 media operations, weekly reconciliation | Sole `service_role` and R2 binding; no public route |

`api-worker` invokes narrow `cron-worker` media methods through Cloudflare Service Binding. For upload, the request carries validated bounded bytes, a pending reservation ID, and an idempotency key; Cron obtains the owner and object key from the reservation row. The Cron Worker verifies every media operation against a pending `media_assets` row; it does not accept arbitrary object keys, arbitrary owner IDs, or general SQL requests.

## Schedules

- Every 5 minutes: claim and advance pending confirmed account-deletion jobs.
- Daily at an off-peak UTC time: refresh climate snapshots for distinct consented coarse locations and process only a capped amount of queued catalog enrichment.
- Weekly: reconcile abandoned upload reservations and unreferenced R2 objects.

Declare Cron schedules in `cron-worker`; Cloudflare Cron uses UTC. Keep the number of schedules within the account's plan limits. The daily climate job is real work, not a synthetic keepalive. It may prevent a development project from appearing inactive, but Supabase Free still has no availability guarantee.

## Finite provider queue

- Seed the reviewed pilot catalog as a controlled, versioned baseline.
- Enqueue external enrichment only for explicit unresolved user-requested species or an approved small batch.
- Never crawl the whole provider catalog continuously. Each adapter has a hard daily request budget based on the configured provider plan; when the queue is empty or the budget is exhausted, stop without retrying more work that day.
- Perenual, Trefle, Flora e Funga do Brasil/Reflora, and Embrapa AgroAPI use independent adapters, cursors, quotas, and result states. Weather adapters use INMET/Open-Meteo.
- External candidates do not resolve identity or publish a reviewed rule automatically. Human/domain review is required before unsupported or unresolved identity gains species-specific behavior.

The API Worker never calls a catalog provider synchronously. An outage, timeout, quota response, or failed translation leaves the user journey on local data and an explicit unresolved state.

## Claim, retry, and audit

`integration_jobs` uses an atomic PostgreSQL claim RPC:

- A job has a unique idempotency key, bounded cursor, attempt count, `next_attempt_at`, `locked_until`, and monotonically increasing `fencing_token`.
- Claim only when due and unleased/lease-expired; increment the fencing token in the same transaction. A duplicate `scheduled()` invocation cannot own a live lease.
- Every cursor, result, retry, and terminal-state write is compare-and-set against the active fencing token. A worker whose lease expired cannot overwrite progress written by a later claimant.
- Canonical job states are `queued`, `running`, `retry_wait`, `succeeded`, `failed`, and `cancelled`; only `queued`/`retry_wait` and expired `running` jobs can be claimed. `succeeded`, `failed`, and `cancelled` are terminal absent an explicit operator requeue that creates a new attempt generation.
- Persist a cursor after each bounded page/batch. Upserts are idempotent by provider record and canonical taxon/field.
- Honor provider `Retry-After`, use bounded backoff, and stop after a configured maximum. Exhausted jobs become visibly failed/reviewable; they are not marked successful.
- Keep user-identifying information out of provider job payloads and logs.

Use Supabase HTTPS Data API/RPC for job state and normalized writes. This avoids direct serverless database connections. If a batch later needs the PostgreSQL wire protocol, use Cloudflare Hyperdrive or Supavisor transaction mode, not a direct connection per invocation.

## Shared worker contract

`packages/contracts` is the only normative contract between the public API Worker, Cron Worker, web client, and RPC boundary. It contains versioned schemas for guest/user API requests, private media Service Binding methods, job RPC inputs/results, and API error envelopes. Both Workers validate runtime inputs against these schemas; TypeScript types alone are not runtime validation.

- Internal media calls identify a pending reservation and `request_id`; they never accept arbitrary owner IDs or R2 object keys from the public caller.
- Every result is either a complete success or an explicit typed failure. Partial R2/database work returns a recoverable operation state, never a success-shaped response.
- Error responses include stable `code`, `retryable`, `retry_after` when known, and `commit_state` (`not_committed`, `committed`, or `unknown`). Clients retry only when the contract allows it and reuse the same idempotency key.
- Stale climate fallback is a successful deterministic response with an explicit stale/fallback state; provider failures are not mislabeled as fresh climate success.

## Provenance and precedence

Persist only normalized application fields and the minimum per-field source claim needed for audit: source, source record ID, original field value, normalized value/unit, retrieval time, translation/review state, merge result, and conflict marker. Do not persist complete external JSON bodies or tokens.

- Local domain rules and reviewed profile fields always govern agronomy, dose, treatment, Waiting Periods, and safety.
- For toxicity, `04-pet-safety.md` governs. A restrictive external flag may add precaution for review; an external negative cannot clear a local warning. Unknown remains `N/A`.
- Deduplicate by the shared versioned canonicalizer: accepted `genus + species`, Unicode NFC, lowercase, trimmed/collapsed whitespace, no authorship; retain synonyms as explicit aliases, not separate canonical species.
- Translate any user-facing external text to Brazilian Portuguese and review it before publication.

## Weather snapshots

- Fetch once daily per distinct consented municipality/coarse location, not once per Plant or each page view.
- Store normalized observations/forecast values with provider, location key, `observed_at`, `collected_at`, and `expires_at`.
- `expires_at = min(collected_at + 24 hours, provider_valid_until)` when provider validity exists; otherwise use `collected_at + 24 hours`.
- Use weather only to make a conservative Agenda proposal inside the domain Safe Window. The user must accept; `Manter agenda padrão` is always available.
- If `now() >= expires_at`, data is absent, or provider results conflict, ignore the climate adjustment and use the deterministic schedule. Show the Portuguese stale/unavailable state.
- Request only the least precise location needed; require consent and store coarse location identifiers, not a home address or raw GPS coordinates.

## Cache and images

- Edge-cache only public search candidates, keyed by normalized search and published catalog revision. Do not cache fit simulation, toxicity decisions, authenticated data, or user inputs.
- Store species imagery and user photos in R2. User photos are private and served only after API Worker ownership checks and an internal Cron Worker operation.
- User upload policy is WebP, at most 1200 px and 2 MiB, 5 photos per Plant, 50 per account, and 10 attempts per account per hour. The API Worker checks streamed size, content type, and RIFF/WEBP signature; the browser's compression is not trusted.
- Reserve the quota and pending media row transactionally before R2 storage. If finalization fails, immediately delete the object and release the reservation; weekly reconciliation is the recovery net.

## Failure behavior

Provider errors are recorded with sanitized status/source/time and surfaced as failed or stale. Do not convert errors to empty-success results. Core catalog, deterministic rules, Inventory, and Agenda remain usable without any provider. Transient network/timeouts and provider `429`/`5xx` responses use bounded retry with provider guidance; invalid credentials, schema violations, and exhausted budgets stop that adapter/job for operator review. API error consumers use the shared envelope and distinguish a committed operation from an unknown outcome before retrying.
