# Adversarial seam review — Architecture Spine

**Target:** `ARCHITECTURE-SPINE.md`  
**Scope:** Constructed implementation/story pairs that both satisfy the spine's ADs but can still produce incompatible persisted contracts, behavior, or failure outcomes. Companion shards were consulted as context; findings below identify seams not fully closed by the spine's ADs.

## Findings

### 1. Canonical-species normalization is not a shared algorithm

- **Location:** AD-3; Consistency Conventions — Identifiers and ownership
- **Trigger condition:** One catalog story normalizes scientific names by trimming and lowercasing; another applies Unicode normalization, accent/punctuation folding, or a different taxonomic string convention. Both can call the result “normalized” and enforce uniqueness only when resolved.
- **Guard snippet:** Define and version one canonicalization algorithm, including Unicode, whitespace, punctuation, authorship, and synonym handling; make all writes and lookup keys use it. If the domain has not selected one, defer canonical deduplication rather than imply compatible normalized keys.
- **Potential consequence:** The same taxon receives different unique keys across imports, migrations, and search, causing duplicates or failed merges that cannot be repaired consistently.

### 2. Owner-scoped foreign keys can permit cross-owner relationships

- **Location:** AD-3; Consistency Conventions — Identifiers and ownership
- **Trigger condition:** A story gives every private row an `owner_user_id` and RLS, but relates a child row to a parent using only `parent_id`; another story uses a composite `(owner_user_id, parent_id)` constraint. The former still has individually owner-protected rows.
- **Guard snippet:** Require owner-scoped composite foreign keys (or equivalent database constraints) for every private parent-child relationship, and require same-owner checks for batch membership, media references, and similar joins.
- **Potential consequence:** A user's row can reference another user's Plant or task; APIs that join via the foreign key may leak or misattribute private data despite row-level ownership checks.

### 3. RLS does not explicitly make ownership immutable

- **Location:** AD-3
- **Trigger condition:** One implementation uses an owner predicate for existing-row access but omits an equivalent post-update check; another checks both the existing and resulting `owner_user_id`. Both describe private rows as RLS-protected by the authenticated subject.
- **Guard snippet:** Require `USING` and `WITH CHECK` ownership predicates for mutations, prohibit clients from changing `owner_user_id`, and constrain ownership changes to an explicit privileged lifecycle operation if one is ever supported.
- **Potential consequence:** An authenticated user can transfer a row out of their own scope or create an ownership state inconsistent with its related records.

### 4. Care-task statuses and legal transitions are not canonical

- **Location:** AD-5; Consistency Conventions — Mutation and errors
- **Trigger condition:** Agenda and task-action stories choose different status vocabularies (`done`/`completed`, `skipped`/`dismissed`, etc.) or disagree on whether a postponed task returns to `pending` or creates a replacement. AD-5 bars postponing/dismissing critical work but does not define the complete task state machine.
- **Guard snippet:** Publish one task-status enum and transition table, including critical and noncritical paths, terminal states, permitted actors, and retry/replay behavior; make the domain package/API/RPC use that contract.
- **Potential consequence:** UI, API, scheduler, and reports interpret the same task differently, and one implementation may permit a transition another considers invalid.

### 5. Safe Window endpoint and local-time semantics can diverge

- **Location:** AD-4 and AD-5; Consistency Conventions — Time and locale
- **Trigger condition:** For a Safe Window such as 09:00–11:00, one story treats the end as inclusive and another exclusive; separate implementations can also resolve a local wall-clock window across a daylight-saving/time-zone transition differently. Both store timestamps in UTC and claim to fit work inside the rule-derived window.
- **Guard snippet:** Specify interval closure (for example `[start, end)`), the authoritative time zone for each window, conversion of local dates to UTC, and behavior for ambiguous/nonexistent local times. Defer scheduling against such windows until those rules are defined.
- **Potential consequence:** A task can be scheduled at a boundary or instant that one surface considers safe and another rejects, or land on a different local day.

### 6. Task-generation idempotency keys lack a canonical identity

- **Location:** AD-4 and AD-7
- **Trigger condition:** Two task-generation stories build keys from different combinations of rule ID/version, task type, Plant, and target window, or define the target window differently. Each can claim generation is deterministic and idempotent within its own implementation.
- **Guard snippet:** Define the canonical task identity tuple, normalization of the target window, uniqueness scope, and conflict/update policy; specify whether a rule-version change creates a new task identity or revises an existing projection.
- **Potential consequence:** Retries or parallel generation create duplicate agenda items, or one story overwrites a task that another expects to remain historically distinct.

### 7. Rule-version meaning and upgrade policy are underspecified

- **Location:** AD-4
- **Trigger condition:** One story stores a package release as `rule_version`; another stores a per-rule revision. On deployment, one regenerates active task projections immediately while another preserves existing projections until the next user action. Both persist a rule ID/version and execute versioned deterministic TypeScript rules.
- **Guard snippet:** Define whether versions identify package builds, rule definitions, or both; specify reproducibility of old decisions and the migration/recalculation policy for active tasks and proposals after a rule change. Defer automatic recalculation until that policy is approved.
- **Potential consequence:** The same persisted version may refer to different behavior, and users can see a mixture of old and new recommendations without a traceable transition.

### 8. Private Service Binding methods have no normative wire contract

- **Location:** AD-6; Structural Seed — `packages/contracts`
- **Trigger condition:** An API story asks Cron to upload using an object key and upload bytes; another expects a media reservation ID and has Cron retrieve/validate the object. Both expose only “narrow internal Service Binding methods” and keep administrative credentials in Cron.
- **Guard snippet:** Define the shared, versioned method set and request/response schemas, permitted identifiers, ownership/reservation proof, idempotency key, timeout behavior, and error mapping. Treat `packages/contracts` as the sole normative contract consumed by both workers.
- **Potential consequence:** Independently shipped workers cannot interoperate, or an API handler mistakes an ambiguous/partial Cron response for a completed media operation.

### 9. Expired job leases do not fence stale workers

- **Location:** AD-7
- **Trigger condition:** A worker continues after its lease expires; a second invocation claims the job and advances it. The first worker then writes a cursor/result or reports success using its stale claim. Atomic leases, expiry, cursors, and idempotency keys alone do not specify rejection of stale owners.
- **Guard snippet:** Require a monotonically changing lease/fencing token and compare-and-set on every cursor, result, and terminal-state write; stale tokens must be rejected. Define the allowed job states and which failures are retryable versus terminal.
- **Potential consequence:** A superseded worker can overwrite newer progress, mark incomplete work successful, or repeat provider calls and consume budget.

### 10. Explicit API errors do not define interoperable retry behavior

- **Location:** Consistency Conventions — Mutation and errors; AD-2 and AD-7
- **Trigger condition:** One endpoint reports stale climate as a successful response with a fallback marker; another returns an error. For upload or task mutation, separate stories assign different retryability to timeout, conflict, quota exhaustion, and partial completion. All can return “explicit errors and retry states” and avoid silent defaults.
- **Guard snippet:** Define shared error codes and response envelopes with retryability, retry-after guidance, and whether the operation may already have committed; define which degraded states are successful fallbacks versus failures. Defer consumer-specific handling until the envelope is fixed.
- **Potential consequence:** Clients retry committed operations, fail to retry recoverable ones, or render provider outage/stale data as equivalent to a successful fresh result.

## Overall assessment

The spine gives strong direction on authority, privilege separation, and safety, but its ADs leave several cross-story contracts implicit. The highest-risk seams are ownership integrity, care-task identity/state, and worker/job protocol semantics. Tighten these as shared normative contracts or explicitly defer the affected implementations until the relevant choices are made.
