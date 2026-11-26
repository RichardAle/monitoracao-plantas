# Data-integrity review

**Target:** `ARCHITECTURE-SPINE.md`  
**Evidence consulted:** `database-schema.md`, `auth-and-security.md`, `async-ingestion-and-weather.md`, `rules-and-scheduling-engine.md`, `index.md`, the PRD/addendum/brief, and the existing architecture reviews.  
**Scope:** ownership, provenance, event/task state, queue/media lifecycle, and account deletion.  
**Exclusion:** Findings already directly enforced by AD-1 through AD-9 are not repeated.

## Verdict

**Conditional pass.** The spine and shards establish a generally coherent integrity model, but three concrete cross-document gaps can permit incompatible implementations. The account-deletion ordering issue is a release-blocking data-lifecycle contradiction; the two agenda/provenance issues require an explicit persistence contract before implementation.

## Findings

### 1. Deletion completion is ordered after the row that must record it is cascaded

**Locations:** `auth-and-security.md`, “Definitive deletion”, steps 3–4; `database-schema.md`, “Core tables”; `async-ingestion-and-weather.md`, “Schedules”.

The deletion saga says to delete the Supabase Auth user after R2 cleanup, then “mark the minimal privacy job complete.” The schema says active user-owned rows cascade on Auth deletion, and `privacy_jobs` is the PostgreSQL row holding deletion status/retry metadata. The same schema says deletion-required job records are processed before the cascade, but does not provide a durable post-cascade status location. A worker that follows the saga literally can lose the job row (or its user FK) before it can mark completion, leaving the account permanently `pending` or making completion unverifiable.

**Required guard:** Define the exact deletion-job retention boundary and ordering. Either mark terminal completion before deleting Auth (with a separately durable, non-user-linked completion record), or explicitly exempt a minimally identified job row from the cascade and specify how it is anonymized after completion. The claim that deletion is complete must be tied to that durable state.

### 2. Retried task/batch mutations can append duplicate completion events

**Locations:** `ARCHITECTURE-SPINE.md`, AD-4/AD-7 and “Mutation and errors”; `database-schema.md`, “Agenda and event integrity”; `rules-and-scheduling-engine.md`, “Compatible Care Batches”.

The documents require a mutable `care_tasks` projection and an append-only `care_events` row in the same transaction, and require one event per task when a batch completes. They do not define an idempotency key or uniqueness condition for a user action/batch completion. A client retry, API retry, or repeated batch submission can therefore append two completion events (or two conflicting state transitions) for one logical action while both transactions satisfy the stated rules. AD-7’s idempotency requirement is scoped to provider/catalog/weather/deletion jobs, not user task mutations.

**Required guard:** Give each logical task action and batch confirmation a client/request idempotency token, persist it on the event (and batch operation), and enforce uniqueness in the mutation RPC. Define the allowed transition from the current task state and the behavior when a duplicate token or already-terminal task is submitted.

### 3. Event audit provenance is required by the spine but not carried by the event schema contract

**Locations:** `ARCHITECTURE-SPINE.md`, AD-4 and “Consistency Conventions”; `database-schema.md`, `care_tasks`/`care_events` table responsibilities and “Agenda and event integrity”; `rules-and-scheduling-engine.md`, “Execution contract”.

AD-4 explicitly requires `rule_id` and `rule_version` on generated tasks/events. The database contract names those fields for `care_tasks`, but the `care_events` contract only describes event kinds/reasons and does not require the rule package revision, rule ID, or the decision/proposal revision being accepted. This permits an implementation that records a valid append-only completion or schedule-acceptance event but cannot reproduce which rule revision produced the task or proposal. The rules shard also says decisions carry rule IDs/package versions, without defining how those values survive into the event row.

**Required guard:** Make event provenance fields mandatory for every event that results from a generated task/proposal (including batch completion and climate acceptance), define nullable behavior only for purely user-authored events, and specify the foreign-key/identity relationship to the task/proposal revision.

## Explicitly not findings

- RLS, owner-scoped relationships, external-authority precedence, stale-weather fallback, bounded queue leases, and media quota reservations are already stated as ADs or enforceable companion invariants.
- The absence of a full provider payload is intentional and consistent with the provenance-minimization rule.
