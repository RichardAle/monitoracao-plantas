# Rules and Scheduling Engine

## Rule ownership

- The local domain documents remain the authority for agronomic/safety content. Executable rules live in the typed, versioned `packages/domain` TypeScript package and are tested before deployment.
- Postgres `species_profiles` supplies reviewed facts and a `rule_profile_key`; the profile cannot execute arbitrary expressions or load API-supplied code.
- External enrichment is context only. It cannot change a rule, dose, Waiting Period, toxicity clearance, or Safety Gate.
- An unsupported, ambiguous, or unreviewed profile returns `não suportado` or `não resolvido`; it does not produce species-specific claims. Unknown toxicity remains `N/A` with precaution.
- Plant identification never implies edibility, medicinal safety, or authorization to use a treatment. Do not recommend unregistered pesticides or unsafe mixtures. Humidity alone is never sufficient evidence for misting advice.
- Until the missing authoritative urban-horticulture source is reviewed, edible-crop profiles remain `não suportado` for care and contamination decisions. They may be listed in Inventory, but no crop-specific care/pot/substrate guidance or contamination clearance is generated.

## Execution contract

The API Worker calls pure functions with normalized inputs: reviewed Species Profile, Plant context, location, household safety context, care history, user Available Care Windows, and an explicit clock/time zone. Equal inputs, rule package revision, and clock produce equal outputs.

Canonical taxon keys use the one shared versioned normalizer: accepted `genus + species`, Unicode NFC, lowercase, trimmed/collapsed whitespace, and no authorship. Keep accepted synonyms as aliases. Do not resolve a common name to a canonical key without a reviewed identity match.

Every decision result carries:

- Rule IDs and package version.
- Stable rule ID, immutable rule revision, exact package version, and task/proposal revision.
- Relevant input facts and their source/review state.
- Deterministic base recommendation and Safe Window.
- Uncertainty or missing data, including `N/A`.
- Portuguese explanation, safety severity, and next action.

No rule function makes network calls or writes directly to the database. The API boundary validates inputs, invokes the engine, then persists projections/events through authenticated RPCs.

## Care Agenda

1. Generate deterministic needs from local rules and recorded Care Events.
2. Apply Safety Gates and treatment/food-crop Waiting Periods before fitting tasks to user availability.
3. Fit movable tasks only inside a rule-derived Safe Window. If no window fits before a critical deadline, escalate; do not silently postpone.
4. Persist the original due window, proposed window, applied rule ID/revision/package version, task revision, and reason.
5. Climate may modulate watering urgency and suggest thermal-protection actions conservatively inside the rule-derived Safe Window. Show the standard schedule, signal/freshness, and proposal; any timing/schedule change applies only after explicit user acceptance. `Manter agenda padrão` leaves the deterministic result intact. Climate cannot authorize an otherwise blocked action.
6. If the signal is missing, expired, or conflicting, use and label the deterministic base schedule.

Canonical task statuses are `pending`, `completed`, and `dismissed`. A pending task can be completed; only a non-critical pending task can be dismissed. Postponement leaves the task pending and changes only its proposed window. Completed and dismissed are terminal except for an explicit correction event. Critical care and safety tasks cannot be postponed or dismissed.

All task actions and batch confirmations carry an opaque caller-generated `request_id`. The mutation RPC uses `(owner_user_id, request_id)` as a unique idempotency key and writes the task projection plus Care Event in one transaction. Repeating the same key returns the original outcome; reusing it for different input is a conflict. An action against an already-terminal task is rejected unless it is an explicit correction. Each task-derived event copies the rule ID/revision/package version and task/proposal revision; purely user-authored observations may have null rule provenance.

Available Care Windows and Safe Windows use the user's IANA time zone and half-open intervals `[start, end)`. Store event instants in UTC. If a local wall-clock time is ambiguous or nonexistent due to a time-zone transition, do not move the task into that window; preserve the deterministic schedule and ask for another window.

Recovery guidance is welcoming and non-punitive. When a safe intervention is supported, fit the next action to the user's next Available Care Window; do not shame a missed task or assert a diagnosis beyond the reviewed matrix.

These mutations change the task projection and append a Care Event; they never edit the Species Profile. A rule-changing package release must ship a reviewed migration plan for affected pending projections; completed tasks and history are never rewritten.

Before confirming a treatment on an edible crop, show and persist the affected crop/Plant, practical waiting interval, washing instruction, and governing rule. If any required instruction or authoritative profile is absent, do not offer treatment confirmation; preserve the unsupported/unknown state.

## Compatible Care Batches

Batch only compatible pruning/fertilization work by owner, Cultivation Location, care type, and overlapping safe opportunity. Before confirmation, list every Plant/task and explain each exclusion in Portuguese; do not silently omit a Plant. Exclude conflicts involving:

- Species-specific windows or incompatible task types.
- Active treatment or treatment waiting/washing periods.
- Recent transplant or recovery restrictions.
- Toxicity, household safety, or other active Safety Gates.
- A task whose Safe Window cannot contain the proposed batch.

Completing a batch records one Care Event per completed task. Skipped items remain explicit and are not represented as completed.

## Diagnosis and emergency path

Guided diagnosis accepts user-selected observations from the local diagnostic matrix; an optional photo is a record only. Return supported possibilities and discriminating observations, not an unsupported certainty. An inconclusive result remains explicit and routes to safe observation or professional help.

Suspected toxic exposure is a separate, high-priority path. It instructs the user to remove the source, preserve an identifiable plant/product sample, and contact appropriate medical, veterinary, or poison-control services immediately. It is not converted into a routine Care Task or replaced by automated diagnosis.

## Guest fit and offline behavior

- Guest fit runs against published local catalog data without creating an account or persisting submitted routine/location context.
- Edge cache may cover public name-search candidates only. Fit output is computed from current published profile and is never edge-cached.
- Offline Agenda/Inventory cache is a display snapshot only; it does not run a new schedule calculation, accept a climate change, complete a task, or create an event.
- At more than 30 days since synchronization, show only dated historical information with `obsoleto`; do not present it as today's Agenda or allow writes.

## Verification floor

Unit tests cover fixed-clock determinism, rule precedence, `N/A`, ambiguous/unsupported states, each Safety Gate, Safe Window boundaries, stale weather fallback, user acceptance/retention, critical task non-overridability, batch exclusion reasons, and idempotent generation. No external provider is contacted by these tests.
