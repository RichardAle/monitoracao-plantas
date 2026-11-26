# PRD Quality Review — Monitoracao Plantas — Active Plant Inventory and Adaptive Care Agenda

## Overall verdict

The revised PRD is a coherent, safety-conscious chain-top product definition: the account/data lifecycle, Taboão da Serra pilot, climate-advisory boundary, and unsupported-profile behavior are now explicit, and the remaining source, freshness, and legal-retention decisions are assigned to architecture/privacy or domain owners with delivery gates. It is suitable for UX and architecture work, but not yet a complete build contract: the recorded task-override policy and browser target are not carried into requirements, and several measurement and export details still need acceptance criteria. **Overall: adequate; close the high finding before story decomposition and resolve the medium findings before implementation sign-off.**

## Decision-readiness — adequate

The Vision states the product bet and its safety-versus-convenience trade-off. The six former decision areas now read as follows:

| Decision area | PRD status |
| --- | --- |
| Account, synchronization, export, and deletion behavior | Defined in §5.1: account required, cross-device inventory sync, user export, and active-data deletion after confirmation. The identity provider, backup purge timing, and legally required retention are correctly left to architecture/privacy in Open Question 1 (§10). |
| Pilot geography and unsupported profiles | Defined in §§6.3 and 8.1: Taboão da Serra and named launch groups; unsupported plants may be inventoried but receive no deterministic action claims. The reviewed profile/city-condition register is correctly assigned to the domain reviewer before launch-scope approval (Open Question 2, §10). |
| Climate influence and user acceptance | Defined in FR-8: climate is advisory; the user may accept the suggested schedule change or retain the standard schedule. Stale, missing, or conflicting signals fall back to the base schedule. |
| Climate safety boundary | Defined in FR-8: accepted adjustments must remain within the species-validated Safe Window and cannot alter safety blocks or authoritative constraints. Source selection, source-specific freshness, and timezone behavior are correctly assigned to architecture/domain before climate integration stories (Open Question 3, §10). |
| Postponing or dismissing tasks | **Not carried into the requirements.** FR-11 prevents silent deferral outside a Safe Window, but does not permit postponing/dismissing non-critical recommendations with a recorded reason or explicitly make critical safety blocks non-overridable. |
| Supported browser target | **Not carried into the requirements.** §5 leaves the browser matrix “agreed before implementation,” despite a recorded target of current and previous major versions of Chrome, Safari, Firefox, and Edge on desktop and mobile. |

### Findings

- **high** Task recourse and critical-block policy are missing (§§4.3 FR-11–FR-12; 4.4 FR-13) — The requirements do not specify the recorded product decision that users may postpone or dismiss non-critical recommendations with a reason, while critical safety blocks cannot be bypassed. FR-13's “block or warn” leaves the hard-block boundary unclear. *Fix:* add testable consequences for non-critical postpone/dismiss actions, reason capture, and a non-overridable state for critical safety blocks.
- **medium** The decided browser target is absent (§5 Accessibility) — “Supported browser matrix agreed before implementation” reopens a settled target and cannot guide browser-specific UX or acceptance testing. *Fix:* state the supported desktop/mobile browsers and current/previous-major-version policy in the PRD.

## Substance over theater — strong

The four named journeys exercise materially different product behaviors, and the differentiator—personal context plus conservative, explainable care—is supported by the requirements rather than asserted as generic novelty. The addendum usefully preserves domain constraints and source governance without turning the PRD into an integration design. The metrics include behavioral outcomes and counter-metrics rather than only activity measures.

### Findings

- **medium** Metric method is not reproducible for critical-alert comprehension (§9 SM-3) — “Can identify the reason and next action in usability testing” has a target but no defined test task, scoring rule, or participant eligibility, so two teams could report different pass results. *Fix:* define the test prompt/task and what counts as a correct identification before using the 80% target for a decision.
- **medium** The audit sample behind SM-5 is undefined (§9 SM-5) — “100% of sampled critical recommendations” is not repeatable without a sampling frame or cadence; a selected sample can conceal unreviewed recommendations. *Fix:* specify the registry/time period, sampling method, and minimum sample size—or use the full release registry, as SM-6 does.

## Strategic coherence — strong

The feature sequence follows the thesis: capture a plant and its conditions (FR-1–4), generate explainable safe care (FR-5–9), fit it to a routine (FR-10–12), and govern safety and enrichment (FR-13–19). The Taboão da Serra pilot and explicit exclusions protect the MVP from becoming an undifferentiated catalog or sensor platform. SM-1–SM-6 generally measure activation, safe completion, comprehension, and provenance that support this thesis.

### Findings

- **medium** SM-2's unsupported-profile reporting is internally inconsistent (§9 SM-2) — It promises separate reporting for Covered Profiles and unsupported profiles, then excludes unsupported profiles from the deterministic-task denominator without defining a separate unsupported-profile measure. *Fix:* name the separate unsupported cohort outcome (or remove the promise of task-completion reporting for that cohort) and define its denominator.

## Done-ness clarity — adequate

FR-1–FR-19 have concrete consequences in most cases: they specify what is recorded, shown, blocked, recalculated, or auditable. The new Prominent Warning definition and the explicit climate fallback improve testability. Remaining gaps cluster around the scope of a user-facing data export and the operating envelope for NFR validation, rather than a pervasive absence of acceptance behavior.

### Findings

- **medium** Export acceptance is underspecified (§5.1) — “Human-readable format” does not establish a verifiable export artifact or required content beyond naming Plant Inventory and Care Events. *Fix:* define the supported format(s), minimum included fields, and whether the export is a point-in-time snapshot.
- **medium** Performance and offline criteria lack a reproducible test envelope (§5 Performance and Reliability) — “Normal supported network conditions” and “previously synchronized” are not defined, and read-only availability has no stated duration or data-age limit beyond the separate 30-day stale label. *Fix:* have architecture specify the network/device and data-size profile, synchronization state, and offline/read-only test duration before implementation acceptance.

## Scope honesty — adequate

Non-Goals, MVP scope, and revisit triggers make major exclusions visible. The PRD no longer treats provider availability as a prerequisite for care, and it explicitly distinguishes an inventory-only unsupported profile from a covered one. The three remaining Open Questions are appropriately non-product decisions and have owners and gates: identity provider/retention with architecture/privacy before implementation; reviewed pilot profiles with the domain reviewer before scope approval; and climate source/freshness/timezone/fallback with architecture and domain before climate stories. These should remain tracked as launch/implementation prerequisites, not be reopened as product behavior.

### Findings

- **low** SM-4 uses an undefined active-user cohort (§9 SM-4) — “Active users” can change the adoption denominator and make the 50% target incomparable. *Fix:* define the qualifying activity and measurement window in the metric.

## Downstream usability — adequate

The PRD is explicitly intended to feed UX, architecture, and story creation. Its glossary, named protagonists, stable FRs, and traceable success metrics support extraction. All local source links resolve. FR-1–FR-19, UJ-1–UJ-4, SM-1–SM-6, and SM-C1–SM-C3 are unique and contiguous; every FR referenced by a success metric exists. No broken FR/SM references were found. Remaining downstream friction is mostly the missing task-recourse policy and acceptance detail noted above.

### Findings

- **low** NFRs lack stable requirement identifiers (§5) — Cross-cutting requirements are named but not individually addressable, which makes downstream story and verification references less precise than the FRs. *Fix:* assign stable NFR IDs if the story workflow needs to trace non-functional acceptance criteria.
- **low** Two assumptions are indexed without inline tags (§6.3; §11 A-1–A-4) — A-1 and A-2 appear as inline `[ASSUMPTION]` callouts, but A-3 and A-4 only appear in the index. *Fix:* tag the corresponding source-input and audience statements inline, or remove those entries if they are no longer assumptions.

## Shape fit — strong

This is a consumer product with consequential safety and UX behavior, so the four named journeys are appropriate and carry context rather than serving as template furniture. The functional-requirement structure, explicit scope, and outcome metrics fit a chain-top PRD. The addendum appropriately holds detailed domain and integration context outside the core requirement flow.

### Findings

- **low** Minor terminology drift appears in journey language (§2.3 UJ-3; §§3–4) — “weather data/signal” and “Agenda” are sometimes used where the glossary term is “Climate Signal” or “Care Agenda.” *Fix:* normalize domain-term capitalization where the wording refers to the glossary concept; retain generic weather language only when it is intentionally broader.

## Mechanical notes

- **Glossary drift:** Core domain terms are stable. Normalize the small “weather signal”/“Climate Signal” and “Agenda”/“Care Agenda” variants noted above.
- **ID continuity and references:** FR-1–FR-19, UJ-1–UJ-4, SM-1–SM-6, and SM-C1–SM-C3 are unique and contiguous. All success-metric FR references resolve. No broken local source links were found.
- **Assumptions Index roundtrip:** A-1 and A-2 have inline callouts; A-3 and A-4 are index-only and should be tagged inline or removed.
- **UJ protagonist naming:** All four journeys name a protagonist and provide context inline.
- **Required sections:** Purpose, Vision, target user/JTBDs, journeys, Glossary, FRs, NFRs, constraints, non-goals, MVP scope, success metrics, open questions, assumptions, and revisit triggers are present and appropriate for this chain-top consumer PRD.
- **Counts by severity:** Critical 0; High 1; Medium 6; Low 4; **Total 11**.
