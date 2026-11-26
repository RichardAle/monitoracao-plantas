# Database Schema

PostgreSQL owns durable user state, reviewed catalog profiles, provenance, generated schedule projections, and worker/privacy job state. Use SQL migrations in `supabase/migrations/`; keep migrations portable and reviewable. The exact Supabase-supported PostgreSQL major is pinned before the first migration.

## Ownership and access

- `auth.users` is owned by Supabase Auth. `user_profiles.id` references its primary key with `ON DELETE CASCADE`.
- Every user-owned table carries `owner_user_id`; all private parent/child links use composite `(owner_user_id, parent_id)` foreign keys or equivalent same-owner constraints. RLS policies require `auth.uid() = owner_user_id` for reads and both the existing and resulting row on mutations. Clients cannot update ownership.
- System-owned operational rows (`integration_jobs` and deletion tombstones) are not user-owned rows: they have no end-user grants/policies, are accessible only to narrowly scoped server RPCs, and never contain unnecessary personal data.
- Anonymous access is limited to published catalog views and narrow public search/fit reads. Do not grant `anon` direct access to user tables, unpublished source facts, or worker/privacy jobs.
- The Cron Worker uses `service_role` only for bounded scheduled catalog/weather/privacy operations. The public API Worker forwards the user's JWT and does not bypass RLS.
- Household presence, toxicity context, precise location, and user observations are private and never appear in public catalog views.

## Core tables

| Table | Responsibility and key invariants |
| --- | --- |
| `species` | Canonical taxon identity; unique versioned normalized `canonical_scientific_name` when resolved; identity and support/review states |
| `species_names` | Scientific/common names, locale, normalized search form, and source; Portuguese display names are reviewed before publication |
| `species_profiles` | Reviewed local facts, confidence, support status, profile revision, and a code-defined `rule_profile_key`; executable rules remain in `packages/domain` |
| `species_field_provenance` | One source claim per field: source/provider record ID, original scalar, normalized scalar, retrieval time, translation/review state, merge outcome, and conflict marker |
| `user_profiles` | Account preferences, weekly Available Care Windows, consent flags, and privacy workflow state |
| `cultivation_locations` | User-owned names, light/ventilation/environment context, and only the coarse climate location the user consented to share |
| `user_plants` | User-owned Plant instance; nullable species link for unresolved identity; location, pot, substrate, household context, and user-supplied label |
| `plant_observations` | User-selected symptoms/conditions, observed time, uncertainty, and follow-up context; photos are attachments, never automated diagnosis inputs |
| `care_tasks` | Current task projection with Plant, task type, due/Safe Window, canonical status/criticality, `rule_id`, immutable rule revision, exact package version, and optional climate proposal |
| `care_batches` / `care_batch_items` | Confirmed group by owner, location, task type, and compatible window; item membership is explicit and auditable |
| `care_events` | Append-only completion, skip, postpone/dismiss reason, treatment, wash/wait, schedule acceptance, or other user event; every event has a unique owner-scoped `request_id` idempotency key |
| `treatment_instructions` | Reviewed instruction/version, affected crop/Plant scope, waiting interval, required washing instruction, and governing rule reference shown before a treatment event is confirmed |
| `climate_snapshots` | Normalized weather per consented coarse location/provider with `observed_at`, `collected_at`, and `expires_at` |
| `media_assets` | Owner, Plant/observation, R2 object key, file size/type, upload state, cover flag, and lifecycle timestamps |
| `photo_upload_attempts` | Account-scoped hourly rate-limit events, including failed attempts; retain only as long as needed for enforcement/audit |
| `integration_jobs` | Finite provider/weather work queue, idempotency key, cursor, lease owner/expiry, attempts, next retry, and explicit result state |
| `privacy_jobs` | Active export/deletion workflow status and retry metadata; user-linked rows are removed by account deletion |
| `deletion_tombstones` | Minimal service-only deletion state keyed by opaque `deletion_id`, without an Auth FK; survives account cascade and contains no direct identifiers after terminal completion |

Use composite foreign keys and unique constraints for owner-scoped relationships, including Plant/location, task/event, batch/item, observation/media, and user/profile links. Children that contain active account data cascade on Auth user deletion. Deletion tombstones have no `owner_user_id`, no end-user visibility, and no identifying subject field; do not model them as a child of `auth.users`.

## Species authority and provenance

- Store authoritative reviewed profile facts separately from external enrichment. `species_field_provenance` is an audit of claims, not the winning rule store.
- Use `JSONB` only for a provider's individual field value when its source type varies, cursor/context data that is genuinely variable, and small non-sensitive job metadata. Do not store full provider response bodies.
- Toxicity is a constrained state (`toxic`, `not_toxic`, `unknown`); render `unknown` as `N/A` with the precautionary Portuguese warning. A false/unknown external result cannot clear a local warning.
- Accepted scientific identity is the deduplication key using one versioned shared normalizer: accepted `genus + species`, Unicode NFC, lowercase, trimmed/collapsed whitespace, and no authorship. Keep synonyms as explicit aliases, not separate canonical taxa. Ambiguous common-name matches do not inherit species rules until identity is resolved.
- Public views expose only reviewed/published fields and a support state; source payloads, unreviewed facts, and user-submitted unresolved names remain private.

## Agenda and event integrity

- `care_tasks` is the mutable current projection with the only statuses `pending`, `completed`, and `dismissed`. `pending -> completed` is allowed for either criticality; `pending -> dismissed` is allowed only for non-critical tasks. Postponement leaves status `pending` and changes its proposed window. `completed` and `dismissed` are terminal except through an explicit correction event; critical tasks cannot be dismissed.
- User actions that change a task or confirm a batch require a caller-generated opaque `request_id`. A mutation RPC applies the projection update and inserts the event in one transaction, with unique `(owner_user_id, request_id)`. Replaying the same key returns the original outcome; reusing it for different input is a conflict. A second key against an already-terminal task is rejected unless it is an explicit correction.
- `care_events` are append-only for the account lifetime. They are deleted with the account, subject to disclosed backup retention and legal exceptions.
- Persist stable `rule_id`, immutable rule revision, exact domain package version, and task/proposal revision on each generated task. Every event caused by a generated task/proposal, including climate acceptance and batch completion, copies these provenance fields and references the task/proposal revision. Purely user-authored observations may leave rule provenance null.
- A treatment confirmation must reference the reviewed instruction and governing rule, affected crop/Plant, waiting interval, and practical washing instruction shown to the user before confirmation. Persist the confirmation snapshot on the treatment event so future edits to instructions do not rewrite history.
- A task occurrence is unique by `(owner_user_id, plant_id, rule_id, normalized occurrence window)` independent of the executable revision. A rule revision never rewrites completed history; a reviewed migration plan determines how affected pending projections are replaced or retained.
- `care_batch_items` must reference tasks with the same owner. A batch never changes a task's rule-derived Safe Window. Batch confirmation uses a request ID and each task event has a unique action key derived from the batch request and task ID.

## Upload reservations and strict quotas

Before any R2 write, a database RPC executed with the caller's JWT must atomically:

1. Verify the Plant belongs to `auth.uid()`.
2. Count active plus pending photos under a locked owner/Plant quota row.
3. Enforce at most 5 photos per Plant, 50 per account, and 10 upload attempts per account per hour.
4. Create a pending `media_assets` reservation with a deterministic owner-scoped object key and opaque reservation ID.

The hourly limit counts all upload attempts, including failed validation attempts, to prevent retry loops from bypassing the budget.

The API Worker sends the validated bounded image bytes, reservation ID, and idempotency key over the private Service Binding; it never supplies an object key. The Cron Worker obtains the object key and owner from the reservation row, verifies reservation state and limits, and performs the R2 write. The API Worker finalizes the row only after the Cron Worker confirms the R2 write. If finalization fails, request immediate object deletion and release the reservation. A weekly reconciliation deletes old unreferenced R2 objects and expires abandoned reservations. Replacing a cover reserves a slot without exceeding either hard count.

## Climate validity

`expires_at = min(collected_at + 24 hours, provider_valid_until)` when the provider supplies an earlier validity. Without provider expiry, use `collected_at + 24 hours`. A snapshot is valid only while `now() < expires_at`; expired snapshots cannot produce climate suggestions.
