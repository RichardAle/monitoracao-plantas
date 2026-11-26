# Spine Pair Review — Monitoracao Plantas

## Overall verdict
The spine pair is **thin** for downstream implementation and story consumption. The EXPERIENCE and DESIGN establish a coherent responsive web vision, but named PRD journeys are incomplete, component/state contracts are under-specified or inconsistently named, and the available mockups are not linked into the spines. Fix the critical contrast/specification and high-impact cross-reference gaps before treating this pair as a downstream contract.

## 1. Flow coverage — thin
**What was checked:** All six PRD Key User Journeys were compared with the four named Key Flows and the IA surfaces in EXPERIENCE.md.

### Findings
- **high** The PRD's UJ-1 inventory onboarding, UJ-4 monthly pruning/fertilization batch, and their named failure/climax branches are not represented as distinct Key Flows (EXPERIENCE.md, Key Flows; PRD §§2.3 and 4.1–4.3). *Fix:* Add complete numbered Key Flows for UJ-1 and UJ-4, or add explicit traceability that maps them to existing flows and surface them with named climax and failure paths.
- **medium** The four existing Key Flows use renamed protagonists and omit stable UJ identifiers (EXPERIENCE.md, Key Flows; PRD §2.3). *Fix:* Name the source UJ in each flow heading or add a requirement traceability line such as `Realizes UJ-5`.

## 2. Token completeness — thin
**What was checked:** DESIGN.md's Color and Typography values were checked for defined values and downstream usability. There are no `{path.to.token}` references or token YAML blocks in the pair, and no unresolved brace references were found.

### Findings
- **critical** Load-bearing text/surface and status combinations do not specify numeric contrast targets (DESIGN.md, Colors, Typography, Do's and Don'ts; EXPERIENCE.md, Accessibility Floor). *Fix:* Add explicit WCAG 2.2 AA targets for normal text and large text for primary text on surfaces, supporting text on surfaces, and every colored status/urgency combination; include light/dark pairs if alternate themes are supported.
- **medium** Token names are prose labels rather than machine-readable frontmatter tokens (DESIGN.md, Colors). *Fix:* If downstream code is expected to mirror the spine, add a token table or frontmatter definitions with stable names, hex values, usage, and any size/contrast metadata.
- **low** Typography defines broad page/title ranges rather than a breakpoint-aware type scale (DESIGN.md, Typography). *Fix:* Add only the minimum implementation scale or explicitly mark 24–32px as a hierarchy range rather than a token.

## 3. Component coverage — thin
**What was checked:** Component names used in both spines and the PRD were compared between DESIGN.md Components and EXPERIENCE.md Component Patterns.

### Findings
- **high** Component names are inconsistent across the spines: `Search field`/`Plant search`, `Alternative row`/`Alternative card`, and `Recovery observation`/`Observation form` do not have one stable name (DESIGN.md, Components; EXPERIENCE.md, Component Patterns). *Fix:* Choose canonical names once and use the same names in both spines and downstream stories.
- **high** Several Experience components lack visual specifications in DESIGN.md: `Quick Care Summary`, `Profile context card`, `Confirmation sheet`, and `Plant search` are not fully covered as real visual component rows (EXPERIENCE.md, Component Patterns). *Fix:* Add visual rules for every component or explicitly classify an undocumented component as a composition of existing components.
- **medium** Several Design components lack complete behavioral specifications: `Primary action`, `Fit summary`, `Alternative row`, and `Recovery observation` need concrete interaction/validation behavior in EXPERIENCE.md (DESIGN.md, Components; EXPERIENCE.md, Component Patterns). *Fix:* Expand matching rows to include activation, disabled/error/loading behavior, confirmation, focus, and recovery rules where applicable.

## 4. State coverage — adequate
**What was checked:** The IA surfaces were walked against empty, cold-load, focus, error, offline, and permission-denied states as applicable.

### Findings
- **medium** Inventory, Agenda, and search lack explicit empty, first-run, cold-load/loading, and sync-failure states (EXPERIENCE.md, State Patterns and IA). *Fix:* Add per-surface treatment for zero items, initial hydration, loading, provider failure, partial sync, validation errors, and retry/recovery.
- **medium** Account deletion, export, authentication, privacy/consent, and permission-denied states are not mapped in State Patterns (EXPERIENCE.md, State Patterns; PRD §§5 and 5.1). *Fix:* Add state rows for unauthenticated save, denied location permission, export/delete confirmation, deletion failure, and post-deletion/retention feedback.
- **low** Dialog/sheet focus behavior is defined as one at a time, but open/loading/success/error focus restoration is not (EXPERIENCE.md, Interaction Primitives and Accessibility Floor). *Fix:* Specify initial focus, focus trap, destructive-action focus, and return-focus behavior for confirmation and save sheets.

## 5. Visual reference coverage — thin
**What was checked:** The `mockups/` directory contains `mockups/color-themes-dossel-tranquilo.html` and `mockups/pre-purchase-fit-botanical-signal.html`.

### Findings
- **high** Neither mockup is linked or referenced inline from either spine (UX mockups directory; DESIGN.md frontmatter sources; EXPERIENCE.md frontmatter sources). *Fix:* Link each mockup at the relevant Colors/Component/Flow section and state what it illustrates.
- **medium** The spines cite `.working/direction-botanical-signal.html` and `.working/color-themes-1.html` in frontmatter, but do not state spines-win-on-conflict or name the illustrated reference (DESIGN.md frontmatter; EXPERIENCE.md no `.working` link). *Fix:* Cite working references inline or remove stale frontmatter sources, and add one explicit “spines prevail on conflict” rule.

## 6. Bloat & overspecification — strong
**What was checked:** The pair was assessed for repeated PRD restatement, decorative narrative, sections without downstream value, and implementation detail versus reusable tokens.

### Findings
- No material bloat finding. DESIGN.md keeps visual rules concise and EXPERIENCE.md is primarily operational. Any duplication between PRD and spines is intentionally condensed into UX behavior and does not appear to restate personas or requirements wholesale.

## 7. Inheritance discipline — thin
**What was checked:** Frontmatter sources, glossary terms, component names, and stable requirement/UJ names were compared across the spines and PRD.

### Findings
- **high** The EXPERIENCE Key Flows do not carry the verbatim PRD UJ IDs or titles, so source inheritance is not mechanically traceable (EXPERIENCE.md, Key Flows; PRD §2.3). *Fix:* Add UJ IDs and exact source titles to flow headings or a traceability table.
- **high** The spines use component names inconsistently, and the PRD glossary is not reproduced in a consistent cross-spine glossary (PRD §3; DESIGN.md Components; EXPERIENCE.md Component Patterns). *Fix:* Use exact glossary terms and one canonical component-name list across PRD, DESIGN, and EXPERIENCE.
- **medium** DESIGN.md frontmatter lists sources that are not all represented or linked inline (DESIGN.md frontmatter; EXPERIENCE.md frontmatter). *Fix:* Verify every source path and add section-level citations where that source changes the visual or behavioral contract.

## 8. Shape fit — adequate
**What was checked:** DESIGN.md canonical section order and EXPERIENCE.md required defaults were compared with the rubric shape.

### Findings
- **medium** Experience has no explicit responsive-layout/breakpoint section or rules despite its responsive web, mobile, and desktop scope (EXPERIENCE.md, Foundation and IA). *Fix:* Add a concise Responsive Behavior section covering mobile navigation, desktop layout, reflow at zoom/dynamic text, table-to-card behavior, and breakpoint-independent reading order.
- **low** No explicit empty-state, focus-state, or loading-state table is present, while both the IA and component contracts imply these states (EXPERIENCE.md, State Patterns). *Fix:* Extend State Patterns rather than adding a redundant state section; keep all state definitions in one table.

## Mechanical notes
- Frontmatter is present in both spines and contains resolvable `.md`/`.html` source references; stale or orphaned visual references remain an inheritance issue, not a broken Markdown/HTML frontmatter parse.
- No Mermaid or other diagram syntax was found that requires syntax review.
- No `{path.to.token}` references or unresolved brace references were found.
- DESIGN.md follows the locked canonical order: Brand & Style, Colors, Typography, Layout & Spacing, Elevation & Depth, Shapes, Components, and Do's and Don'ts.
- EXPERIENCE.md includes Foundation, IA, Voice and Tone, Component Patterns, State Patterns, Interaction Primitives, Accessibility Floor, and Key Flows.
- No filenames or line-level cross-references are broken by a missing Markdown link in the spines themselves.

## Severity counts
- **Critical:** 1
- **High:** 6
- **Medium:** 8
- **Low:** 3
