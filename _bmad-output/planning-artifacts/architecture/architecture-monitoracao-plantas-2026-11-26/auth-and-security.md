# Authentication, Privacy, and Security

## Identity and guest boundary

- `Encontrar planta` search and pre-purchase fit work without authentication. Do not create an anonymous account for guest use; keep submitted routine/location context transient and do not persist it server-side.
- Saving a Plant or synchronizing `Minhas plantas` requires Supabase Auth with e-mail/password and mandatory e-mail confirmation.
- Use passwordless OTP/magic link only for password recovery, not routine sign-in. Authentication e-mails are transactional; no care reminders are sent by e-mail, SMS, or push.
- Preserve the pending Plant draft through the sign-up/sign-in step so the user does not lose entered context. Discard it when the user cancels or the short-lived browser session ends.
- Use Supabase's supported PKCE flow, allowlisted redirect URLs, secure password reset, and rate-limited auth endpoints. Configure a custom SMTP provider before public launch; the default SMTP is for development only, currently limited to team addresses and 2 messages/hour with no delivery SLA.

## Request authorization and secrets

- The Supabase publishable key may be in the static client; it is public by design. It does not grant access to private rows without valid user authorization and correct RLS.
- The authenticated user's JWT is sent over HTTPS to `api-worker`, which forwards it to Supabase Data API/RPC. RLS checks ownership on every private read/write. Never log the JWT or return it in errors.
- `api-worker` has no `service_role`, external provider credentials, or R2 binding. `cron-worker` alone has the service-role secret and R2 binding, stored as provider secrets rather than source or build variables.
- `cron-worker` has no public route. The API Worker uses Cloudflare Service Binding for a narrow internal method only; validate its payload and require an existing owner-scoped job/media reservation.
- Restrict CORS to the production and approved preview origins; use TLS, CSP, output escaping, input validation, size limits, and explicit error responses. Do not render provider strings as HTML.

## RLS and sensitive data

- Enable RLS on every user-owned table and deny by default. Policies bind `owner_user_id` to `auth.uid()` for select/insert/update/delete.
- Expose reviewed catalog data through published views with read-only `anon` grants. Never grant anonymous access to profiles, pet/child presence, user locations, observations, private media metadata, or worker jobs.
- Collect the coarsest location adequate for weather, with explicit consent. Do not store exact GPS/address for climate when a municipality/coarse grid suffices.
- Treat pet/child presence, toxicity exposure, cultivation context, and user observations as private. Logs contain IDs only when necessary and omit content, auth tokens, exact location, and photo bytes.

## Export

On explicit request, return an authenticated, human-readable JSON export plus CSV tables for the user's profile/context, Cultivation Locations, Plants, observations, Agenda tasks, Care Batches, and Care Events. Include media metadata and authorized short-lived per-object download links for the user's photos. Generate on demand through RLS-scoped reads; do not e-mail or retain an export file by default. Return a clear recoverable error if any part fails.

## Definitive deletion

R2 and PostgreSQL cannot commit one distributed atomic transaction. Use an idempotent deletion saga:

1. Require re-authentication/confirmation and create a deletion request through the user's JWT/RLS. Mark the account deletion-pending and block new writes.
2. The Cron Worker claims the job with a lease, deletes the owner's R2 objects in bounded batches, and records progress without logging personal content.
3. Keep a service-only `deletion_tombstones` row keyed by an opaque `deletion_id`, without an Auth foreign key or direct identifier. It is the durable status record and survives the active account cascade.
4. After media deletion succeeds, delete the Supabase Auth user through the Admin API; active user-owned PostgreSQL rows cascade. Treat an already-absent Auth user as an idempotent success only after verifying that owner-scoped active rows and R2 objects are absent.
5. Mark the tombstone complete only after the Auth deletion/cascade succeeds. If the Auth Admin request times out, retain `pending` and retry; if the user is already absent, verify cleanup then complete the tombstone. On any failure, never present deletion as complete.
6. Remove any remaining subject linkage from the tombstone at completion; retain only the opaque deletion ID, terminal state, and minimal timestamps. The deleted user cannot poll by account ID; the initiating session displays `pending` until completion or a recoverable failure, then confirms success without restoring account access.
7. State when provider backups age out. Supabase Pro daily backups are retained for 7 days as checked on 2026-11-26; active data is deleted immediately after the saga succeeds, while backup copies expire under the disclosed provider retention, subject to applicable legal retention obligations.

The R2 cleanup also reconciles old pending uploads and unreferenced objects weekly. A failed R2 write or database finalize is retried/compensated; no cross-system operation is described as atomic.

## Photo upload controls

- Hard cap: WebP, max 1200 px, 2 MiB per file, 5 active photos per Plant, 50 per account, and 10 upload attempts per account per hour.
- The API Worker authenticates the owner, checks streamed byte count, declared type, and the RIFF/WEBP signature. Never trust a browser-provided MIME or length alone.
- A transactionally reserved Postgres slot prevents concurrent requests from exceeding account/Plant caps. R2 keys are deterministic and owner-scoped: `users/{user_id}/plants/{plant_id}/{photo_id}.webp`.
- Send the bounded image bytes, reservation ID, and idempotency key through the private Cron Worker Service Binding. Cron derives the key and owner from the reservation; never accept an arbitrary path or owner ID from the caller.
- Finalize the Postgres media reference only after R2 confirms success. On finalization failure, delete the object immediately and leave a retryable cleanup state.
- At the cap, allow deletion of an older image or replacement of the cover; do not silently increase quotas. Revisit limits after the pilot.
