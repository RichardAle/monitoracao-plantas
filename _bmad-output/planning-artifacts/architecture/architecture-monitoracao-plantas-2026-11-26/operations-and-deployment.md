# Operations, Deployment, and Cost

## Environments

| Environment | Data and service policy |
| --- | --- |
| Local development | Supabase CLI/local Postgres and Auth; `wrangler dev`; synthetic/test data; local secrets ignored by Git |
| Dev/staging cloud | Supabase Free is acceptable for development and staging only; no production user data; alert on pause, quota, and job errors |
| Production | Supabase Pro in São Paulo (`sa-east-1`), custom SMTP, RLS review, backup/restore check, and explicit provider budgets before opening to real users |

Supabase region cannot be changed after provisioning. São Paulo is selected for the Taboão da Serra pilot's latency and data-location needs; regional location is not itself a compliance certification.

The pilot includes urban edible crops/herbs as an intended cohort, but the authoritative `05-urban-horticulture.md` source is currently absent. These profiles may be inventoried as `não suportado`; crop-specific care, pot/substrate assertions, contamination clearance, and treatment recommendations remain disabled until a reviewed domain source is available. Enabling these claims is a production release gate, not a frontend workaround.

## Release path

1. Run TypeScript checks, rule unit tests, Worker contract tests, RLS/migration tests, accessibility checks, performance budgets, and static build in CI.
2. Apply reviewed SQL migrations before deploying code that depends on the new schema.
3. Deploy `cron-worker` and `api-worker` separately from the same repository; configure the API-to-Cron Service Binding.
4. Deploy the static Next.js export to Cloudflare Pages.
5. Run smoke checks for guest search/fit, confirmed account login, RLS cross-account denial, Agenda fallback, photo upload/replace/delete, export, and account-deletion retry.
6. Release-check current and previous major Chrome, Safari, Firefox, and Edge on desktop and mobile. Verify core Inventory, alert, and task-completion flows to WCAG 2.2 AA with automated checks plus manual keyboard/focus review.
7. Keep production and staging secrets separate. The Cron Worker production secret set is the only one with `SUPABASE_SERVICE_ROLE_KEY`, provider credentials, and R2 administrative access.

## Performance verification

- Measure Inventory/Agenda from route navigation to usable first render under a documented normal-network profile, including the first server response when no fresh cache exists. The p75 target is 2 seconds.
- Measure schedule recalculation from the API request reaching `api-worker` through normalized response persistence/serialization for a deterministic 200-Plant fixture. The p95 target is 3 seconds.
- Run a representative 200-Plant benchmark in CI and again against the production-like staging configuration before release. Record runtime/CPU usage alongside latency.
- If the Workers Free 10 ms CPU budget prevents the target, optimize queries and computation first; if still unmet, block release until a measured plan/runtime change is approved. Do not silently relax the PRD SLO or turn off Safety Gates.

## Cost and quota gates

As checked on 2026-11-26:

- Supabase Free has 500 MB database and 5 GB egress; projects may be paused after 7 days of low activity and downloadable backups are unavailable. A legitimate daily weather sync may keep a development project active, but is not a production uptime strategy.
- Supabase Pro starts at $25/month for a single project on Micro compute with current included compute credits; it provides daily backups with 7-day retention. Require this upgrade before public production. Recheck pricing and overages at launch.
- Cloudflare Workers Free allows 100,000 requests/day and 10 ms CPU per HTTP/Cron invocation. Keep free-tier jobs small and cursor-based. A sustained CPU requirement above this limit triggers a measured change to Workers Paid (currently $5/month minimum), a cheaper workload design, or a separately approved alternative.
- Free Workers have 5 Cron Triggers per account. Keep the two-Worker design within the account's trigger budget.
- R2 Standard includes 10 GB-month, 1 million Class A and 10 million Class B operations/month, with no egress fee. Over-limit storage and operations are billable.
- Keep only normalized API fields and field-level provenance in PostgreSQL; never store complete provider payloads. Stop enrichment at its provider budget and stop/deny new photo uploads at the configured storage cap.

Set billing alerts/spending limits where available. Over-budget enrichment must fail closed without blocking guest search, deterministic care, account access, or deletion.

## Backups and recovery

- Before production, verify daily Supabase backup status and run a restore drill into a non-production project.
- Document the 7-day provider backup retention in deletion confirmation/privacy copy; do not promise immediate physical deletion from retained backups.
- Keep SQL migrations and a human-readable export path as recovery aids. Export is not a replacement for tested database backups.
- R2 media is not included in Supabase database backups. Reconcile R2 object manifests with `media_assets`; test object restore/retention independently.

## Observability and operations

Emit structured, privacy-minimized records for API request category, rule/version, job/run ID, provider status, cursor progress, lease contention, queue age, stale climate fallback, media lifecycle, and deletion status. Do not log email, JWT, raw provider payload, exact location, household risk values, or photo data.

Monitor:

- Worker CPU and invocation errors, Cron delay, and schedule propagation.
- Integration queue depth/oldest job, lease expiry, retry exhaustion, provider quota, and translation/review backlog.
- Supabase DB size/egress, Auth email failure, RLS denials, and backup status.
- R2 storage/operation usage, photo rejection rate, pending media age, and orphan cleanup failures.
- Climate snapshot freshness and the percentage of Agenda results using deterministic fallback.

Every failure affecting user data must be visible and recoverable. Do not report a successful export, upload, deletion, ingestion, or climate suggestion until its persistence step has succeeded.

## Deferred operations

- Choose the transactional SMTP provider and sender domain before public release; care notifications remain in-app only.
- Confirm each provider's current quota/terms and enter a daily hard budget before enabling its adapter.
- Set exact legally reviewed retention exceptions and privacy wording before production; do not invent statutory retention periods in code.
- If a managed plan or free quota becomes unsuitable, reassess total cost and operator burden before self-hosting; no automatic migration or silent plan change.
