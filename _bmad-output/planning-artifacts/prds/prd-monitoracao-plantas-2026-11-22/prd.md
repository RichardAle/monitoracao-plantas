---
title: "Monitoracao Plantas — Active Plant Inventory and Adaptive Care Agenda"
status: draft
created: 2026-11-22
updated: 2026-11-25
---

# PRD: Monitoracao Plantas — Active Plant Inventory and Adaptive Care Agenda

## 0. Document Purpose

This PRD defines the product behavior for a responsive web application that helps Brazilian urban plant owners manage a personal Plant Inventory and follow a safe, climate-aware Care Agenda. It is intended for product, UX, architecture, and story-creation work. Requirements are grouped by capability and use stable FR IDs; domain terms are defined in the Glossary. The prior product brief and authoritative domain rules are source inputs, not duplicated implementation specifications: [brief.md](../../briefs/brief-monitoracao-plantas-2026-11-22/brief.md), [02-watering-algorithms.md](../../../domain/02-watering-algorithms.md), [06-bonsai-management.md](../../../domain/06-bonsai-management.md), and [07-external-apis-contract.md](../../../domain/07-external-apis-contract.md).

## 1. Vision

Monitoracao Plantas turns a scattered collection of plant-care reminders into an active management system. A user registers what they own, where each Plant lives, what conditions it experiences, and what care has already happened. The product then produces a Care Agenda that fits the user's available routine without silently trading one risk for another.

The product's thesis is that reliable care comes from combining personal context with conservative, explainable agronomic rules. The Agenda may move a safe task window to a user's available day or use local weather to adjust urgency, but it must block over-care, surface under-care before irreversible damage, and explain the rule or observation behind every critical recommendation. Public APIs enrich coverage; they never replace Brazilian agronomic or household-safety rules.

## 2. Target User

### 2.1 Jobs To Be Done

- Keep plants alive and healthy despite a busy urban routine and inconsistent free time.
- Know which plant needs attention today, why it needs it, and what action is safe.
- Avoid both overwatering/root rot and delayed care during heat, dryness, or sensitive growth stages.
- Consolidate recurring pruning and fertilization into manageable batches.
- Record care once and trust that future recommendations account for it.
- Make safe decisions around pets, children, edible crops, unknown toxicity, and household preparations.

### 2.2 Non-Users (v1)

- Commercial growers requiring field-scale irrigation or agronomic certification.
- Users seeking automated photo diagnosis or continuous IoT sensor control.
- Veterinary, medical, or legally certified toxicology users.
- Community, marketplace, or social-content users.

### 2.3 Key User Journeys

- **UJ-1. Ana turns a mixed apartment collection into a trustworthy inventory.**
  - **Persona + context:** Ana, a beginner with twelve ornamental plants spread across a sunny balcony and a shaded living room, wants a single place to stop guessing.
  - **Entry state:** Authenticated on the responsive web app; Plant Inventory is empty or partially populated.
  - **Path:** Ana searches or selects a plant name; records its room, light exposure, container type, and basic substrate conditions; repeats for the collection; reviews missing or uncertain fields.
  - **Climax:** The app shows each Plant with an initial care profile and clearly marks any unknown toxicity or missing environmental data instead of presenting it as safe.
  - **Resolution:** Ana has an inventory with actionable next steps and can open the Care Agenda.
  - **Edge case:** If a common name maps to multiple species, the app requests confirmation or stores an uncertain identity without inventing a canonical species.

- **UJ-2. Carlos reorganizes care around the time he actually has.**
  - **Persona + context:** Carlos works long hours and has only Wednesday evening and Sunday morning available for plant care.
  - **Entry state:** His Plant Inventory contains plants with different base watering, fertilization, and pruning windows.
  - **Path:** Carlos configures available care windows; reviews a weekly Agenda; accepts safe shifts; sees tasks grouped by location and care type; records completion and observations.
  - **Climax:** The Agenda consolidates compatible tasks without moving a task beyond its safe physiological boundary and explains which tasks could not be shifted.
  - **Resolution:** Carlos completes a smaller batch, and the next due dates incorporate the recorded events.
  - **Edge case:** If no available window is safe before a critical deadline, the app escalates the task rather than silently postponing it.

- **UJ-3. Beatriz responds to weather and over-care risk before damage occurs.**
  - **Persona + context:** Beatriz cares for a bonsai and edible herbs in a hot, dry city and tends to water whenever she sees an alert.
  - **Entry state:** Local weather data is available; recent watering and fertilization events are recorded.
  - **Path:** Beatriz opens a high-priority alert; sees the weather signal, the last care event, the applicable rule, and the safe action; records a soil observation or skips watering when the substrate is already wet.
  - **Climax:** The app blocks a redundant watering or warns about heat-driven urgency while preserving a clear human decision path.
  - **Resolution:** Beatriz knows what to monitor next and why the alert priority changed.
  - **Edge case:** If weather data is stale or unavailable, the system falls back to the deterministic base rule and labels the uncertainty.

- **UJ-4. Diego batches seasonal pruning and fertilization safely.**
  - **Persona + context:** Diego maintains a small balcony garden and bonsai collection but wants one planned maintenance session per week.
  - **Entry state:** Plants have care history, species profiles, and seasonal windows.
  - **Path:** Diego opens the monthly view; filters pruning and fertilization; reviews grouped batches; checks prerequisite and waiting-period warnings; completes a batch and records exceptions.
  - **Climax:** The system groups compatible work while separating tasks that conflict with post-transplant, post-treatment, or species-specific restrictions.
  - **Resolution:** The inventory reflects completed work and the next safe window is recalculated.

- **UJ-5. Marina checks whether a plant fits before buying it.**
  - **Persona + context:** Marina is a novice plant buyer at a store and can consult the catalog as a guest. Her account profile may already contain available care times and home Cultivation Locations; if not, she wants a quick, non-technical summary.
  - **Entry state:** Marina searches by the common or scientific name on the plant label.
  - **Path:** The app shows a simple care-demand summary; when profile data exists, it compares task needs, light, and ventilation with her availability and Cultivation Locations; if the plant is a poor fit, it suggests similar-looking or similarly sized plants with more compatible care needs.
  - **Climax:** Marina understands whether the plant appears to fit her routine and why, without needing botanical knowledge or creating an account.
  - **Resolution:** If she decides to buy it, she can add the Plant to her synced Plant Inventory after signing in or creating an account; if the comparison used incomplete profile data, the app clearly labels the result as a quick estimate.
  - **Edge case:** If the plant identity is ambiguous or the Species Profile is unsupported, the app explains that it cannot confirm fit and does not invent a care level.

- **UJ-6. Marina follows a guided path to help a struggling plant recover.**
  - **Persona + context:** Marina notices visible changes on a Plant and wants a practical, non-judgmental next step that fits her available time.
  - **Entry state:** Marina opens the Plant detail and selects predefined observations; she may attach a photo as a record, not as an automated diagnosis input.
  - **Path:** The app asks for distinguishing observations, presents one or more possible causes with their evidence and uncertainty, then shows an intervention supported by the reviewed Species Profile and Authoritative Rule set.
  - **Climax:** Marina understands what to check next and can choose a safe intervention window that fits her routine.
  - **Resolution:** The app records the observation and action, then offers a follow-up check; its tone stays welcoming if the Plant does not improve.
  - **Edge case:** If observations do not distinguish supported causes, the app preserves uncertainty and offers a safe observation or professional-help path instead of a confident diagnosis.

## 3. Glossary

- **Plant** — A user-owned individual or group entry in the Plant Inventory, linked to a species identity and care context.
- **Plant Inventory** — The user's authoritative list of Plants, locations, conditions, and care history.
- **Cultivation Location** — A user-defined place in the home or garden, with its growing conditions, that can be associated with one or more Plants.
- **Care Fit** — A comparison between a Species Profile's care demands and a user's available care times and Cultivation Locations; it communicates compatibility and uncertainty, not a guarantee of plant survival.
- **Quick Care Summary** — A simplified, non-personalized view of a Species Profile's care demands shown when the user has not completed profile setup.
- **Species Profile** — A normalized plant identity and its agronomic, safety, and seasonal parameters.
- **Care Event** — A recorded action or observation, such as watering, fertilization, pruning, transplant, treatment, or substrate observation.
- **Care Task** — A pending action generated for a Plant by a Species Profile, Care Event, climate signal, or safety rule.
- **Care Agenda** — The ordered set of Care Tasks and safe windows presented to the user.
- **Safe Window** — A time interval in which a Care Task can be completed without violating a known physiological, safety, or waiting-period constraint.
- **Available Care Window** — A day and time interval the user declares available for plant care.
- **Climate Signal** — Current or forecast local weather data used only to modulate urgency or a base rule.
- **Authoritative Rule** — A deterministic agronomic or safety rule from the project's Brazilian domain knowledge that takes precedence over external enrichment.
- **Safety Gate** — A blocking or warning condition that prevents an unsafe recommendation from being presented as valid.
- **Waiting Period (carência)** — The minimum interval after a treatment, transplant, or other event before a specified care action is allowed.
- **Critical Care Task** — A Care Task whose Safe Window has a safety or irreversible-damage threshold; it cannot be deferred solely to fit routine availability.
- **Covered Profile** — A Species Profile and context combination for which the MVP has a reviewed Authoritative Rule set; unsupported combinations remain available for inventory but do not receive deterministic action claims.
- **Stale Climate Signal** — A Climate Signal older than the freshness limit configured for its signal type and source; stale data cannot modulate a schedule.
- **Prominent Warning** — A warning rendered before confirmation, adjacent to the affected action, with severity conveyed by text and icon, the risk, the required precaution, and an acknowledgment or blocking state where required.
- **Unknown (`N/A`)** — A value not confirmed by an authoritative source; it is not equivalent to safe, absent, or negative.
- **Care Batch** — A user-visible group of compatible Care Tasks scheduled together.

## 4. Features

### 4.1 Plant Inventory and Context

**Description:** Users create and maintain Plants with species identity, a linked Cultivation Location, container, substrate, household context, and care history. Users set routine availability and their Cultivation Locations in the profile. The flow is manual-first and tolerant of incomplete identification, but missing safety-critical data remains visible. Realizes UJ-1, UJ-5, and UJ-6.

**Functional Requirements:**

#### FR-1: Register a Plant

The user can create a Plant by selecting or entering a popular name or scientific name and recording its location, environment, container, and basic conditions.

**Consequences (testable):**
- A saved Plant has a stable identifier, creation timestamp, identity status, and editable context fields.
- The form supports apartment, house, office, balcony, and urban garden contexts.
- A user can create Cultivation Locations in the profile, describe available direct/indirect light and ventilation, and associate each Plant with a location.
- The system marks unresolved identity fields as unknown and does not fabricate a Species Profile.

#### FR-2: Resolve Species Identity

The system can map a user-entered name to a Species Profile or present explicit alternatives when the name is ambiguous.

**Consequences (testable):**
- A canonical scientific name is used for deduplication when confirmed.
- Ambiguous or missing identity is shown as unresolved and cannot silently inherit species-specific safety rules.
- User-facing names and instructions are presented in Brazilian Portuguese.

#### FR-3: Record Plant Context and Care History

The user can edit a Plant's linked Cultivation Location, container, substrate, household exposure, and Care Events.

**Consequences (testable):**
- Care Events preserve event type, timestamp, optional notes, and the affected Plant.
- The timeline distinguishes confirmed events from observations and unknown values.
- Editing context triggers recalculation of affected Care Tasks and records the reason for changed timing.
- Profile-level context contains Available Care Windows and user-defined Cultivation Locations; each Plant can be linked to a location.
- Cultivation Location context can include indoor, sheltered outdoor, exposed outdoor, balcony, airflow, light, container drainage, and substrate measurements when available; unavailable measurements remain explicitly unknown.

#### FR-4: Show Inventory Risk State

The system can show each Plant's next Care Task, urgency, unresolved data, and active Safety Gates.

**Consequences (testable):**
- A Plant with unknown toxicity is never labeled safe and displays a prominent precaution.
- A Plant with an overdue critical task is distinguishable from a Plant with a routine task.
- Inventory sorting and filtering include location, task type, urgency, and unresolved data.

### 4.2 Deterministic Care Schedule

**Description:** The system generates Care Tasks from Authoritative Rules, Plant context, Care Events, and approved Climate Signals. It covers watering, fertilization, sun exposure windows, pruning, substrate/container checks, and bonsai protocols. Realizes UJ-2, UJ-3, and UJ-4.

#### FR-5: Generate a Base Care Schedule

The system can calculate a base Care Agenda for a confirmed Plant using the applicable Species Profile and Authoritative Rules.

**Consequences (testable):**
- Each generated Care Task identifies the Plant, task type, due date or Safe Window, priority, and applied rule or reason.
- Covered rules include watering, fertilization, exposure monitoring, pruning, transplant recovery, and container/substrate constraints.
- A task cannot be generated as complete without a matching Care Event.

#### FR-6: Prevent Over-Care and Unsafe Timing

The system can apply Safety Gates that block or warn against redundant watering, fertilization during a waiting period, or actions that conflict with post-transplant and post-treatment restrictions.

**Consequences (testable):**
- A watering recommendation considers the latest recorded watering, substrate observation, container type, species rule, and relevant Climate Signal.
- Fertilization is blocked during the applicable post-transplant or post-treatment Waiting Period, including the documented 60/90-day bonsai constraints where applicable.
- Every block states the prevented risk, such as waterlogging, root rot, salt stress, phytotoxicity, or unsafe exposure.

#### FR-7: Escalate Under-Care Before Irreversible Damage

The system can raise task priority when a Plant is approaching or beyond a critical care threshold.

**Consequences (testable):**
- Critical alerts identify the deadline, affected Plant, required action, and escalation reason.
- The system never lowers a critical task below its safe priority merely to fit an Available Care Window.
- If an input is stale or unavailable, the alert states the fallback rule and uncertainty.

#### FR-8: Apply Climate Signals Conservatively

The system can use local temperature, humidity, radiation, and precipitation data to suggest an adjustment to the base schedule without automatically changing it or overriding an Authoritative Rule.

**Consequences (testable):**
- A Climate Signal has source, observation/forecast time, location basis, freshness, and uncertainty metadata.
- When a Climate Signal suggests a timing change, the user sees the possible schedule impact and can accept the suggestion or retain the standard schedule.
- An accepted climate adjustment remains within the species-validated Safe Window and other safety boundaries.
- Climate suggestions are advisory; the system does not apply them without user acceptance.
- A stale, missing, or conflicting signal falls back to the deterministic base schedule.
- External climate data cannot change toxicology, dosage, Waiting Period, or a safety block.

#### FR-9: Recalculate After Care Events

The system can recalculate affected Care Tasks after the user records watering, fertilization, pruning, transplant, treatment, or a relevant observation.

**Consequences (testable):**
- Recalculation changes only tasks affected by the event and keeps an auditable before/after reason.
- A completed task is not recreated solely because a schedule recalculated.
- The user can see the next Safe Window after recording an event.

### 4.3 Routine Fit and Care Batching

**Description:** Users declare Available Care Windows and the system fits movable tasks into them, while preserving safety boundaries. Compatible pruning and fertilization tasks can be presented as Care Batches. Realizes UJ-2 and UJ-4.

#### FR-10: Configure Available Care Windows

The user can configure recurring or one-time days and time intervals in their profile as generally available for plant care.

**Consequences (testable):**
- The system stores timezone-aware day/time intervals and supports editing or disabling them.
- Available Care Windows apply across the user's Plant Inventory; a Plant's linked Cultivation Location supplies its place-specific light and ventilation context.
- An unavailable period cannot be treated as an Available Care Window.
- The user can distinguish a preferred window from a hard availability constraint.

#### FR-11: Fit Movable Tasks Into the Routine

The system can propose a new task time within an Available Care Window only when the proposed time remains inside the task's Safe Window.

**Consequences (testable):**
- The proposal shows the original due window, the proposed time, and the safety boundary used.
- Tasks outside a Safe Window remain escalated and are not silently deferred.
- The user can postpone or dismiss a non-critical task and must record a short reason for the action.
- Postponement or dismissal cannot suppress a Critical Care Task or a critical Safety Gate.
- Accepting a proposal or recording postponement/dismissal creates an auditable schedule change without altering the underlying Species Profile.

#### FR-12: Consolidate Compatible Care Tasks

The system can group compatible pruning and fertilization tasks into a Care Batch by time, location, task type, and safety compatibility.

**Consequences (testable):**
- A Care Batch lists every included Plant and task before confirmation.
- Tasks with conflicting species, waiting-period, toxicity, or post-transplant rules are excluded with a reason.
- Completing a batch records a separate Care Event for each completed task or explicitly records each skipped task.

### 4.4 Safety, Explainability, and Observations

**Description:** The product makes safety and uncertainty visible at the point of action. It covers pets, children, edible crops, unknown toxicity, contaminated urban soil/water, household preparations, and treatment waiting periods. Realizes UJ-1 and UJ-3.

#### FR-13: Enforce Safety Gates

The system can block or warn about a recommendation when authoritative safety rules identify risk to people, pets, edible crops, or the environment.

**Consequences (testable):**
- Unknown toxicity produces a fail-safe precaution and is never represented as confirmed non-toxic.
- Unsafe indoor preparations or restricted pesticides are blocked when pets or children are present.
- Euphorbiaceae latex and other known high-risk conditions receive prominent warnings before the action is confirmed.
- Critical safety blocks cannot be bypassed through postpone, dismiss, or climate-adjustment actions.

#### FR-14: Explain Recommendations

The system can show the Authoritative Rule, input evidence, uncertainty, and practical next step for every critical Care Task or Safety Gate.

**Consequences (testable):**
- A user can open an explanation from an alert without leaving the affected Plant context.
- Explanations distinguish confirmed, inferred, and unknown values.
- An external source is labeled as enrichment and cannot be presented as the governing rule when an Authoritative Rule exists.

#### FR-15: Record Observations and Treatment Follow-Up

The user can record substrate moisture, symptoms, treatment applications, washing instructions, and follow-up observations.

**Consequences (testable):**
- A treatment on an edible crop can create a follow-up reminder for the documented waiting or washing interval.
- When a treatment requires washing or waiting, the user sees the practical instruction, interval, affected crop/Plant, and governing rule before confirming the event.
- A symptom observation does not claim a diagnosis unless the deterministic diagnostic matrix supports that conclusion.
- Follow-up tasks remain linked to the original Care Event and Plant.

#### FR-16: Guide Supported Symptom Diagnosis

The user can select observable symptoms and context, and the system can map them to supported pest, disease, nutrient-deficiency, contamination, or physiological-stress possibilities from the diagnostic matrix.

**Consequences (testable):**
- The flow distinguishes an observation, a supported possibility, and a confirmed diagnosis; it never presents an unsupported conclusion as certain.
- Before suggesting a higher-risk treatment, the flow presents lower-risk environmental and substrate checks when the matrix requires them.
- An unsupported symptom returns a safe observation/follow-up state rather than a fabricated diagnosis.

#### FR-17: Respond to Suspected Toxic Exposure

The system can show an emergency response path when a user records or selects suspected ingestion or exposure to a Plant or preparation.

**Consequences (testable):**
- The response instructs the user to remove the source of exposure, preserve an identifiable plant or product sample, and contact appropriate veterinary, medical, or poison-control services immediately.
- The emergency path is visibly distinct from routine Plant care and cannot be satisfied by a generic “monitor” reminder.
- The response does not provide a diagnosis or home treatment as a substitute for professional guidance.

### 4.5 Catalog Enrichment and Provenance

**Description:** The product expands Species Profiles through an asynchronous ingestion process. External catalog data is normalized, translated, deduplicated, and stored with provenance; the runtime user journey does not depend on catalog API availability.

#### FR-18: Ingest and Normalize Catalog Data

The system can ingest approved external catalog data and normalize names, taxonomy, light, temperature, and supporting fields into Species Profiles.

**Consequences (testable):**
- Ingestion records source, retrieval time, original value, normalized value, and translation status.
- The canonical scientific name is the deduplication key when available.
- User-facing text is translated and reviewed in Brazilian Portuguese before publication.

#### FR-19: Resolve Source Precedence

The system can merge external data with the authoritative domain layer using defined precedence rules.

**Consequences (testable):**
- Authoritative agronomic and safety data cannot be overwritten by external fields.
- When toxicity sources conflict, the most restrictive validated status wins; unknown remains `N/A`.
- A conflict is retained as provenance metadata for audit and correction.

### 4.6 Pre-Purchase Care Fit

**Description:** A prospective buyer can check care demands before deciding to buy a plant. The guest flow reduces purchase uncertainty; a saved Plant is added to the account-backed Plant Inventory only after authentication. When profile data is incomplete, the product shows a Quick Care Summary rather than implying personalized fit. Realizes UJ-5.

#### FR-20: Check Care Fit Before Purchase

The user can search for a plant by the common or scientific name on its label and compare its reviewed care demands with their profile before adding it to the Plant Inventory.

**Consequences (testable):**
- A guest can search and view a Quick Care Summary without creating an account.
- When profile data is available, the result compares care task demands and relevant light/ventilation requirements with profile-level Available Care Windows and Cultivation Locations, and explains the fit or mismatch.
- When profile data is incomplete, the result identifies missing context and is labeled as a non-personalized estimate.
- When the selected plant is a poor fit, the system may suggest alternatives with similar appearance or size and more compatible care demands; it explains why each alternative is suggested.
- Ambiguous or unsupported plant identities do not receive a definitive Care Fit claim.
- Saving a selected Plant to the Plant Inventory requires an account and uses the existing plant-registration flow.
- Care levels and task demands come from reviewed Species Profiles; the system does not hardcode illustrative frequencies or imply guaranteed survival.
- Fit results and alternatives do not suppress toxicity, edible-crop, or other Safety Gates.

## 5. Cross-Cutting Non-Functional Requirements

- **Performance:** Inventory and Agenda views should render usable content within 2 seconds at the 75th percentile under normal supported network conditions; a schedule recalculation for a user's inventory of up to 200 Plants should complete within 3 seconds at the 95th percentile.
- **Reliability:** A previously synchronized Plant Inventory and deterministic base Agenda remain viewable in read-only mode when a climate or catalog provider is unavailable. The last successful synchronization timestamp must be visible; data older than 30 days is marked stale and cannot be treated as current.
- **Safety integrity:** No external API response may override an Authoritative Rule, Safety Gate, dosage, Waiting Period, or toxicology status.
- **Explainability:** Every critical alert and blocked action includes a human-readable reason, input timestamp, and rule/source provenance.
- **Accessibility:** Core inventory, alert, and task-completion flows target WCAG 2.2 AA, including keyboard navigation, visible focus, semantic labels, minimum contrast, and non-color-only urgency indicators. MVP support covers stable current and previous major versions of Chrome, Safari, Firefox, and Edge on desktop and mobile browsers.
- **Localization:** User-facing content, dates, units, and care instructions use Brazilian Portuguese; source-language values remain metadata only.
- **Privacy:** Location data is collected at the minimum granularity needed for climate adjustment, with explicit consent and a user-visible deletion path. Household pet/child presence is treated as sensitive context and is not exposed publicly.
- **Observability:** Schedule generation and Safety Gate decisions emit structured, privacy-minimized diagnostic records sufficient to investigate a disputed recommendation.

### 5.1 MVP Data Lifecycle

- MVP is single-user ownership: one account owns one Plant Inventory; household collaboration and delegated roles are deferred.
- The product requires an account and synchronizes a Plant Inventory across devices.
- The user can export their Plant Inventory and Care Events in a human-readable format.
- After the user confirms deletion, active account and inventory data are deleted; the product communicates the backup purge timeframe and honors applicable legal retention obligations.
- Authentication, export, and deletion flows must not expose household safety context or location to another user.
- The identity provider, backup purge timeframe, and exact legally required retention periods are architecture/privacy decisions, not MVP provider commitments.

## 6. Constraints and Guardrails

### 6.1 Agronomic and Safety Guardrails

- The authoritative domain layer, including [04-pet-safety.md](../../../domain/04-pet-safety.md), is the source of truth for covered Brazilian agronomic, bonsai, diagnostic, and domestic-safety rules.
- Climate data may suggest timing changes and urgency, but cannot authorize an otherwise blocked action; the user decides whether to accept a safe suggestion.
- Unknown values are represented as `N/A` and trigger precaution when safety is material.
- Container depth guidance must preserve the documented 20 cm minimum for common leafy crops/herbs and 25 cm minimum for roots/tubers, subject to Species Profile exceptions.
- Soil, substrate, and groundwater contamination checks remain visible prerequisites for edible crops or child access.

### 6.2 Integration and Data Governance

- Catalog APIs are ingestion sources, not synchronous runtime dependencies.
- API credentials remain server-side and never enter client bundles or checked-in files.
- Every enriched field carries provenance and an explicit merge result.
- Rate limits, retries, and stale-data behavior must fail back to local deterministic rules rather than success-shaped defaults.

### 6.3 Product and Platform

- **[ASSUMPTION: responsive web is the initial form factor]** Desktop and mobile browsers are in scope for MVP; native apps are not required for the first release.
- **[ASSUMPTION: manual-first operation]** IoT sensors and automatic image diagnosis are deferred until usage validates the core agenda.
- The MVP must work for a user with incomplete catalog coverage and must not require external API credentials to complete a local care task.
- The launch pilot is Taboão da Serra, São Paulo, covering reviewed common indoor ornamentals, urban edible crops and herbs, and bonsai. National Brazil expansion is a later goal.
- MVP coverage is defined as the reviewed Species Profiles within the launch groups and pilot location; unsupported entries can be inventoried but receive an explicit unsupported-state explanation.

## 7. Non-Goals (Explicit)

- Automated watering or physical actuator control in MVP.
- Continuous IoT sensor ingestion in MVP.
- Fully automated image diagnosis or claims of medical/veterinary expertise.
- Social feeds, marketplace, commerce, community moderation, or advertising.
- Replacing agronomists, veterinarians, toxicologists, or regulatory guidance.
- Runtime dependence on Trefle, Perenual, or another external catalog provider.
- Complete coverage of every Brazilian species, biome, municipality, or cultivation system.

## 8. MVP Scope

### 8.1 In Scope

- Pilot launch in Taboão da Serra, São Paulo, for reviewed common indoor ornamentals, urban edible crops and herbs, and bonsai.
- Manual Plant Inventory with species identity, environment, container, substrate, household context, and Care Events.
- Responsive web inventory and weekly/monthly Care Agenda.
- Guest pre-purchase Care Fit lookup, Quick Care Summary, and compatible similar-plant alternatives.
- Deterministic watering, fertilization, exposure, pruning, transplant recovery, container, and substrate guidance for covered profiles.
- Available Care Windows, safe schedule shifts, and compatible Care Batches.
- Climate-modulated urgency with deterministic fallback.
- Safety Gates for pets, children, edible crops, unknown toxicity, treatments, and contamination prerequisites.
- Explainable alerts, provenance, uncertainty, and Portuguese user-facing content.
- Asynchronous catalog enrichment and normalization.

### 8.2 Out of Scope for MVP

- Nationwide coverage; expand beyond Taboão da Serra after pilot validation and review of regional profiles.
- Native mobile applications; revisit after responsive-web demand validation.
- IoT and sensor integrations; revisit after manual observation usage demonstrates the highest-value signals.
- Photo-first diagnosis; revisit after the deterministic diagnostic flow is validated.
- Multi-user household roles and shared inventories; revisit if ownership and delegation become material.
- Advanced personalization using machine learning; revisit after sufficient Care Event data exists.

## 9. Success Metrics

**Primary**

- **SM-1:** At least 70% of new users with three or more Plants complete an initial inventory and open the Care Agenda within their first session. Validates FR-1, FR-4, and FR-5.
- **SM-2:** At least 60% of due Care Tasks for active users are completed within their Safe Window during the first four weeks, reported separately for Covered Profiles and unsupported profiles. Unsupported profiles are excluded from the deterministic-task denominator. Validates FR-9 and FR-11.
- **SM-3:** At least 80% of users who receive a critical alert can identify the reason and next action in usability testing. Validates FR-7, FR-13, and FR-14.

**Secondary**

- **SM-4:** At least 50% of active users configure one or more Available Care Windows and use at least one Care Batch in the first month. Validates FR-10 and FR-12.
- **SM-5:** 100% of sampled critical recommendations retain an Authoritative Rule or explicit `N/A` provenance and no sampled external field overrides a Safety Gate. Validates FR-8, FR-18, and FR-19.
- **SM-6:** 100% of published safety-critical recommendations in the MVP rule registry are linked to a reviewed Authoritative Rule or explicit `N/A` fail-safe state before release. Validates FR-13, FR-14, and FR-19.
- **SM-7:** In usability testing, at least 80% of novice participants can explain whether a searched plant appears to fit their stated care availability and identify one reason for the result. **[ASSUMPTION: target]** Validates FR-20.

**Counter-metrics (do not optimize)**

- **SM-C1:** Number of completed watering events — do not maximize it; a rise paired with blocked overwatering or root-rot warnings may indicate healthier restraint. Counterbalances SM-2.
- **SM-C2:** Catalog breadth — do not maximize it at the cost of unresolved identity, translation quality, or safety provenance. Counterbalances FR-18.
- **SM-C3:** Notification volume — do not maximize it; rising alerts with falling comprehension or task completion indicates alert fatigue. Counterbalances FR-7.

## 10. Open Questions

1. **Architecture + privacy owner — before implementation:** Select the identity provider and define backup purge timing and legally required retention consistent with the account deletion promise in §5.1.
2. **Domain reviewer — before launch scope approval:** Produce the reviewed Species Profile and city-condition register for the Taboão da Serra pilot and define which profiles are supported versus inventory-only.
3. **Architecture + domain reviewer — before climate integration stories:** Select the climate source and define signal-specific freshness limits, timezone behavior, and the user-visible fallback; climate adjustments remain advisory and user-accepted.

## 11. Assumptions Index

- **A-1:** Responsive web is the initial form factor (§6.3); inherited from the prior brief and not confirmed in live discovery.
- **A-2:** Manual registration and observation are the MVP interaction model (§6.3); IoT is deferred.
- **A-3:** The prior brief and domain files are accepted source inputs (§0); the discovery response was unavailable.
- **A-4:** The primary audience is Brazilian residential and urban plant owners (§2); inherited from the prior brief.
- **A-5:** The initial Care Fit comprehension target is 80% of novice usability-test participants (§9 SM-7); the target is proposed and should be revisited after the POC baseline.

## 12. Revisit Triggers and Owners

- **Native app:** Product owner revisits after responsive-web retention and task-completion data show that mobile-web friction is a material cause of missed Safe Windows.
- **IoT sensors:** Product owner and domain reviewer revisit after manual observations show that a specific sensor signal would reduce missed critical tasks without increasing over-care.
- **Photo diagnosis:** Safety reviewer revisits after the guided diagnostic flow has measured false-positive/false-negative risk and a reviewable image-data policy exists.
- **Brazil coverage expansion:** Product owner and domain reviewer expand beyond Taboão da Serra after pilot validation and only when new location and Species Profiles have reviewed Authoritative Rules, safety status, Portuguese content, and fallback behavior.
