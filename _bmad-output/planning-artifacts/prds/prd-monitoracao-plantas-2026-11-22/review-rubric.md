# PRD Quality Review — Monitoracao Plantas — Active Plant Inventory and Adaptive Care Agenda

## Overall verdict

This is a substantively strong, coherent chain-top PRD: the thesis is explicit, the safety posture is conservative, the user journeys are named and concrete, and the FRs, metrics, glossary, and non-goals mostly line up. It is not yet a green-light-ready contract because several launch and implementation decisions remain open, particularly account/data ownership, initial coverage, climate freshness, accessibility targets, and the operational meaning of some safety and reliability requirements. Overall verdict: **adequate for UX and architecture discovery; conditionally ready for story decomposition after the high findings are resolved or explicitly accepted.**

## Decision-readiness — adequate

The PRD makes consequential choices plainly: manual-first responsive web, deterministic guided care rather than photo diagnosis, external APIs as enrichment rather than runtime dependencies, and Safety Gates that outrank convenience. The Vision states the trade-off directly: the product “must block over-care, surface under-care before irreversible damage,” and the addendum explains that IoT and native applications are deferred to validate whether a safe, explainable agenda changes behavior.

The decision surface is still incomplete for a release decision. The five Open Questions include account/authentication/data export, minimum species and municipality coverage, climate freshness, recommendation overrides, and accessibility/browser support. These are not cosmetic details: they affect trust, privacy, safety behavior, acceptance testing, and MVP feasibility. The PRD also records that discovery was unavailable in A-3, so several audience and product assumptions are inherited rather than confirmed.

### Findings

- **high** Launch decision inputs remain unresolved (§10 Open Questions; §11 A-3) — Authentication/data export, geographic/species coverage, climate freshness, override policy, and accessibility support are all deferred despite the document being intended for UX, architecture, and story creation. *Fix:* assign an owner and decision date to each question, or explicitly mark the affected capability as blocked/deferred before approving MVP scope.
- **medium** Trade-offs are stated but not decision-recorded (§1 Vision; §8.2 MVP Out of Scope; addendum “Deferred Alternatives”) — The rationale for manual-first and responsive web is clear, but no explicit success/failure threshold says when native apps, sensors, or photo diagnosis become eligible. *Fix:* add revisit criteria and a decision owner for each deferred alternative.

## Substance over theater — strong

The content is earned rather than template furniture. Four journeys each have a named protagonist, an actual path, a climax, a resolution, and an edge case. The differentiator is specific—“adaptive, explainable care” using Brazilian location, microclimate, container, substrate, and observed state—not generic catalog breadth. The addendum also keeps implementation detail out of the main PRD while preserving provenance, source precedence, and safety boundaries.

Most NFRs are product-specific: 2-second inventory/agenda rendering, 3-second recalculation for up to 200 Plants, offline availability of synchronized inventory and deterministic agenda, provenance on critical alerts, and privacy handling for location and household context. The remaining vagueness is limited to a few adjective-based requirements rather than a pervasive pattern.

### Findings

- **medium** Accessibility language is partly boilerplate (§5 Cross-Cutting NFRs; §10 Open Question 5) — “Readable contrast,” “semantic labels,” and “visible focus” are directionally useful but lack a target standard or supported-browser boundary, which the PRD itself acknowledges is unresolved. *Fix:* choose an accessibility target (for example, WCAG level and applicable flows), define measurable acceptance expectations, and close the browser-matrix question.

## Strategic coherence — strong

The PRD has a clear thesis: reliable care comes from personal context plus conservative, explainable agronomic rules. The feature arc follows that thesis from Plant Inventory and context (FR-1–4), to deterministic scheduling and safety (FR-5–9), to routine fit (FR-10–12), and finally explainability and governed enrichment (FR-13–17). The MVP is a problem-solving experience rather than an unbounded platform, and its exclusions—IoT, image diagnosis, social features, and broad species coverage—protect that focus.

Success Metrics validate the thesis rather than merely measuring activity. SM-1 measures activation into an agenda, SM-2 measures completion inside a Safe Window, SM-3 measures comprehension, and SM-5 verifies authoritative provenance and Safety Gate integrity. Counter-metrics explicitly prevent optimizing watering volume, catalog breadth, or notification volume at the expense of safety and comprehension.

### Findings

- **low** Coverage scope is not yet connected to the thesis metrics (§8.1; §9 SM-1/SM-2) — The PRD says “covered profiles” and leaves minimum species/municipality coverage open, but does not state how metrics are segmented when a user’s plants are outside coverage. *Fix:* define eligibility/coverage cohorts for the primary metrics and exclude or separately report unsupported profiles.

## Done-ness clarity — adequate

FR-1 through FR-17 consistently include “Consequences (testable)” and usually specify the data shown, the safety behavior, and the audit result. Examples include FR-11’s original and proposed windows plus safety boundary, FR-12’s per-task batch completion records, FR-14’s distinction between confirmed/inferred/unknown, and FR-17’s retained conflict provenance. This gives downstream story creation a strong behavioral foundation.

Several requirements still rely on terms that are not operationally bounded. “Basic conditions,” “prominent warnings,” “usable content,” “normal supported network conditions,” “critical,” and “sufficient to investigate” leave room for materially different implementations. Reliability also says synchronized data remains viewable but does not define synchronization freshness, local-data retention, or behavior when a recalculation cannot run. Safety-critical flows need especially explicit acceptance boundaries.

### Findings

- **high** Safety-critical UI outcomes are not measurable enough (§4.1 FR-4; §4.4 FR-13; §5 Explainability/Accessibility) — “Prominent precaution,” “distinguishable,” and “human-readable reason” do not define placement, required fields, blocking behavior, or acceptance evidence. *Fix:* specify the minimum alert/block content and testable UI/state rules for unknown toxicity, restricted preparations, and critical overdue tasks.
- **medium** Schedule and weather freshness behavior is underspecified (§4.2 FR-7–FR-9; §10 Open Question 3; §5 Reliability) — The PRD requires freshness metadata and fallback but leaves the threshold and user-visible staleness behavior open. *Fix:* define freshness classes/thresholds, timezone semantics, and the exact fallback state for stale, missing, or conflicting signals.
- **medium** Reliability and performance boundaries need operational definitions (§5 Performance and Reliability) — “Normal supported network conditions” and “previously synchronized” are not measurable, and no cold-start/offline or persistence boundary is stated. *Fix:* define test environment, payload/data-size assumptions, synchronization age, and expected offline/read-only behavior.

## Scope honesty — adequate

The PRD is unusually explicit about boundaries: Non-Goals names automated watering, IoT, photo diagnosis, social/commerce, professional replacement, external runtime dependence, and complete Brazilian coverage. MVP In Scope and Out of Scope are consistent with the manual-first thesis, and the addendum preserves deferred alternatives without pretending they are current capabilities. Inline assumptions are indexed in A-1 through A-4.

The main weakness is unresolved scope that has safety and delivery consequences. Account and data-export expectations are absent from scope, geographic/species coverage is not bounded, and recommendation overrides are undecided. The assumptions index honestly notes inherited assumptions and unavailable discovery, but those assumptions are still carrying primary-audience and source-authority decisions into downstream work.

### Findings

- **high** Data ownership and account scope are a release blocker (§10 Open Question 1; §5 Privacy; §8 MVP Scope) — The product stores plant history, location, and sensitive household context, yet account/authentication, export, deletion semantics, and continuity across devices are unanswered. *Fix:* decide the MVP data-lifecycle contract and either add it to requirements or explicitly defer affected scenarios.
- **medium** MVP coverage is silently variable (§10 Open Question 2; §8.1 “covered profiles”) — The PRD promises deterministic guidance but does not define which species, municipalities, or climate contexts qualify, making “MVP” scope and success denominators unstable. *Fix:* publish a minimum coverage list/criterion and the fallback UX for unsupported profiles.

## Downstream usability — strong

This is explicitly a chain-top PRD (§0), and it is structured for extraction by UX, architecture, and story creation. The Glossary defines the central domain nouns; UJ-1 through UJ-4 have named protagonists; FR-1 through FR-17 and SM-1 through SM-5 are contiguous and unique; metric cross-references resolve to existing FRs. The addendum cleanly separates source governance and technical ingestion decisions from capability requirements.

The remaining downstream risk is not structural breakage but underspecification at boundaries: NFRs have no IDs, some terms such as “critical” and “covered profile” lack definitions, and the open questions can change story acceptance criteria. These are manageable if resolved before decomposition.

### Findings

- **medium** Undefined gate and coverage vocabulary can fragment downstream interpretation (§3 Glossary; §4 FR-4, FR-7, FR-13; §8.1) — “Critical task/alert,” “covered profile,” “supported network,” and “prominent warning” are used as decision-bearing terms without glossary definitions or thresholds. *Fix:* define them in the Glossary or in explicit acceptance rules before splitting stories.

## Shape fit — strong

The responsive web application serves a consumer/meaningful-UX product with safety-sensitive, multi-context behavior, so named journeys are load-bearing rather than overhead. The four journeys cover onboarding, routine planning, weather/safety response, and batching; each has a protagonist and edge case. The capability-oriented FR structure is appropriate for a PRD that feeds UX, architecture, and story creation, while the operational metrics and guardrails fit a safety-aware personal tool.

The product is not forced into a social, marketplace, or sensor-platform shape: those are explicitly excluded. The manual-first choice also avoids over-formalizing an internal operator workflow while preserving enough operational detail for provenance and auditability.

### Findings

- **low** Household collaboration is explicitly deferred but may affect journey assumptions (§8.2 Out of Scope; UJ-1–UJ-4) — All journeys assume one owner and one inventory, which is coherent for MVP but should be stated as a deliberate single-user model in the target-user section. *Fix:* add “single-user ownership in MVP” to the scope/target-user boundary and revisit trigger.

## Mechanical notes

- **Glossary drift:** Core terms are mostly stable and capitalized consistently. “Climate Signal” is defined, but lower-case variants (“climate signal,” “weather signal,” “local weather data”) appear in the journeys and prose; normalize these where they carry the same domain meaning. “Agenda” is sometimes shorthand for “Care Agenda.”
- **ID continuity:** FR-1–FR-17, UJ-1–UJ-4, and SM-1–SM-5 are contiguous and unique. Counter-metrics are intentionally labeled SM-C1–SM-C3 and do not conflict. No broken metric-to-FR references were found.
- **Assumptions Index roundtrip:** A-1–A-4 correspond to the inline assumptions and no unindexed inline `[ASSUMPTION: …]` tags were found. A-3 accurately records the unavailable discovery response, but it should remain a visible approval risk.
- **UJ protagonist naming:** All four UJs name Ana, Carlos, Beatriz, and Diego and carry context inline.
- **Required sections:** The document purpose, vision, target users/JTsBD, journeys, glossary, features/FRs, NFRs, constraints, non-goals, MVP scope, metrics/counter-metrics, open questions, and assumptions are present and fit a chain-top consumer PRD.
