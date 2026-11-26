# Accessibility review — Monitoracao Plantas UX designs

**Reviewed:** `DESIGN.md`, `EXPERIENCE.md` and the adjacent UX `.memlog.md`  
**Basis:** WCAG 2.2 AA, scoped to the design decisions and behavior stated in these artifacts.  
**Scope note:** This is a specification review, not a conformance claim about a rendered implementation. Findings identify design details that are absent or conditional risks that should be resolved and tested during implementation. The review considered keyboard operation, focus, dynamic text, zoom/reflow, text and icon state meaning, target size, contrast, recovery, emergency, and climate suggestions.

## Context from the UX memlog

The artifacts generally honor the memlog's intent: keep comparison available to guests; compare care with routine and cultivation locations; distinguish known, missing, and uncertain information; make climate changes advisory and user-accepted; and provide guided recovery with uncertain causes and optional photo-as-record. The memlog also calls for emergency guidance in context and a dedicated emergency route. The findings below focus on accessibility details not yet specified for those decisions.

## Findings

### 1. Color tokens need explicit contrast-use constraints

- **Location:** `DESIGN.md`, Colors, lines 22–33
- **WCAG:** 1.4.3 Contrast (Minimum); 1.4.11 Non-text Contrast
- **Evidence:** Calculated against `Surface` (`#F2F6F0`), `Adjustment` (`#C06A3B`) is approximately **3.58:1** and `Border` (`#C7D5C8`) approximately **1.39:1**. Other listed text/state colors exceed 4.5:1 against this surface. These calculations cover only the stated color pairings; the document does not define every token pairing or component state.
- **Finding:** The adjustment color is described as a possible correction/next context but has no restriction against use for normal-size text. At 3.58:1 it does not meet 4.5:1 for normal text. The low-contrast border also needs a rule against relying on it alone to identify a control boundary or meaningful state.
- **Recommendation:** Add an approved foreground/background pairing table and say that adjustment is not used for normal-size text on the primary surface unless darkened; require control boundaries/state indicators to meet 3:1 where needed to identify the component, or provide another sufficiently contrasting cue.
- **Potential consequence:** Warning/support text or control boundaries may be difficult to perceive for people with low vision, even though the state also has a label.

### 2. Keyboard behavior is stated as a goal, not specified for custom interactions

- **Location:** `EXPERIENCE.md`, Foundation and Component Patterns, lines 14–16 and 45–55; Interaction Primitives, lines 76–85
- **WCAG:** 2.1.1 Keyboard; 2.4.3 Focus Order; 4.1.2 Name, Role, Value
- **Evidence:** The foundation promises “platform-conventional, keyboard-accessible web behavior,” while the patterns introduce selectable observation conditions, task actions, climate controls, and confirmation sheets without specifying their control semantics or keyboard operation.
- **Finding:** It is not clear whether these are native controls or custom widgets, what keys operate them, or how their name, role, value, and selection state are exposed. A platform-conventional promise alone does not settle custom chips, task controls, or selection patterns.
- **Recommendation:** Specify native HTML controls where possible. For any custom widget, state its role, accessible name, value/selected/disabled state, keyboard interaction, and focus order; test every flow without a pointer.
- **Potential consequence:** Keyboard-only users may be unable to select observations, accept/retain a schedule, or complete task actions, or assistive technology may report controls without their state.

### 3. Focus visibility and obstruction need measurable design criteria

- **Location:** `EXPERIENCE.md`, Accessibility Floor, lines 89–95; `DESIGN.md`, Colors and Elevation & Depth, lines 20–33 and 53–55
- **WCAG:** 2.4.7 Focus Visible; 2.4.11 Focus Not Obscured (Minimum)
- **Evidence:** The floor says focus is visible and follows reading order, but gives no focus indicator treatment or requirement that focused controls remain visible around dialogs, sheets, sticky navigation, and viewport edges.
- **Finding:** A visible focus indicator could still be too subtle against the palette or be hidden under an overlay/sticky element; neither behavior is resolved by the current statement.
- **Recommendation:** Define a persistent focus style that contrasts with adjacent colors and is distinct from hover/selection, and require that keyboard focus is not entirely hidden by author-created content at any viewport or zoom. Include focus checks for dialogs and sheets.
- **Potential consequence:** Keyboard users can lose track of their position or unknowingly operate an obscured control.

### 4. Dialog and sheet focus lifecycle is underspecified

- **Location:** `EXPERIENCE.md`, Confirmation sheet, lines 53–55; Accessibility Floor, line 94
- **WCAG:** 2.1.1 Keyboard; 2.4.3 Focus Order; 2.4.11 Focus Not Obscured; 4.1.2 Name, Role, Value
- **Evidence:** The spec says “Focus opens or closes one dialog/sheet at a time” and says cancellation remains available, but does not state initial focus, containment, dismissal, or where focus goes when the layer closes.
- **Finding:** A modal confirmation can open without useful focus, allow focus to move behind it, lack a keyboard dismissal route where appropriate, or close without returning focus to its invoking control. The current sentence does not define which behavior is intended.
- **Recommendation:** For each modal sheet/dialog, specify an accessible name/description, initial focus, modal semantics and focus containment, keyboard dismissal behavior (including any safety exception), and focus restoration to the trigger or a logical successor. Keep non-modal sheets non-modal in both behavior and semantics.
- **Potential consequence:** Keyboard and screen-reader users may enter the wrong context, become trapped, or lose their place after confirming/canceling.

### 5. Zoom and narrow reflow are not defined for the two-column layout

- **Location:** `DESIGN.md`, Layout & Spacing, lines 44–50; `EXPERIENCE.md`, Accessibility Floor, line 92
- **WCAG:** 1.4.10 Reflow; 1.4.4 Resize Text
- **Evidence:** Desktop results may use two columns, while the accessibility floor only says the UI remains usable at browser zoom and dynamic text sizes. No breakpoint, reflow behavior, or exception for two-dimensional content is specified.
- **Finding:** At 400% browser zoom (equivalent to a 320 CSS-pixel viewport at the usual desktop baseline), the two-column result layout or tables could require two-dimensional scrolling or hide content/actions. The text does not require the desktop layout to collapse or verify text enlargement.
- **Recommendation:** Require content and actions to reflow without loss or two-dimensional scrolling at 320 CSS px, except for genuinely two-dimensional content, and test at 200% text resize. Specify that columns stack and labels/actions wrap rather than clip, overlap, or disappear.
- **Potential consequence:** People with low vision who enlarge content may miss care context, warnings, or actions or have to scroll in two directions.

### 6. Dynamic text and text-spacing resilience need explicit acceptance checks

- **Location:** `DESIGN.md`, Typography, lines 35–42; `EXPERIENCE.md`, Accessibility Floor, line 92
- **WCAG:** 1.4.4 Resize Text; 1.4.12 Text Spacing
- **Evidence:** The visual design calls for 16px body and 14px supporting text at standard zoom and says dynamic sizing must be preserved, but does not define scaling behavior or behavior when users override text spacing.
- **Finding:** Responsive type alone does not ensure that text can be enlarged to 200% or that user-applied line, paragraph, letter, and word spacing does not clip content or obscure controls.
- **Recommendation:** Specify browser text resizing to 200% without loss of content/functionality and support WCAG 1.4.12 text-spacing overrides without clipping, overlap, or truncation in summaries, cards, labels, buttons, and dialogs. Avoid fixed-height text containers.
- **Potential consequence:** Users with low vision or reading/cognitive needs may be unable to read instructions or operate controls after enlarging or spacing text.

### 7. The 44px target rule specifies height only

- **Location:** `EXPERIENCE.md`, Accessibility Floor, line 92; `DESIGN.md`, Shapes, lines 57–59
- **WCAG:** 2.5.8 Target Size (Minimum)
- **Evidence:** The floor sets a practical minimum of 44px CSS **height**, but gives no minimum width, spacing, or rule for adjacent targets. Compact status tags are also permitted by the design.
- **Finding:** Height alone does not ensure a sufficiently large or separated hit area; a narrow icon button or tightly packed actions may remain difficult to activate. WCAG 2.2 AA specifies a 24×24 CSS-pixel minimum subject to defined exceptions, while 44px is a stronger practical target.
- **Recommendation:** Preserve the intended 44px practical target in both dimensions where feasible, and specify how smaller/inline targets meet 2.5.8 (including spacing or another applicable exception). Verify actual hitboxes, not only visible icon size, for adjacent task actions and status controls.
- **Potential consequence:** People with limited dexterity or touch users may activate the wrong action or fail to activate a control.

### 8. State meaning needs programmatic as well as visible labels

- **Location:** `DESIGN.md`, Colors, lines 26–33 and Components, lines 63–69; `EXPERIENCE.md`, Component Patterns, lines 52–55 and State Patterns, lines 59–74
- **WCAG:** 1.4.1 Use of Color; 2.5.3 Label in Name; 4.1.2 Name, Role, Value
- **Evidence:** Both documents correctly reject color-only meaning and require state text/icon labels. They do not specify selected, pressed, expanded, disabled, or current-state semantics for climate accept/retain, observation choices, task actions, or the “cannot postpone/dismiss” critical-task state.
- **Finding:** A visible text label does not by itself communicate a control’s current state to assistive technology. In particular, a critical task being unavailable for postponement/dismissal needs to be represented and explained, not just omitted or visually disabled; selected chips and retained/accepted suggestions also need state feedback.
- **Recommendation:** Define each control's accessible name and role, expose applicable state/value programmatically, keep the visible label in the accessible name, and explain unavailable actions and their reason in text. Use semantic disabled/read-only behavior appropriately and ensure state is not conveyed by icon/color alone.
- **Potential consequence:** Screen-reader users may not know which observations or schedule choices are selected or why an action is unavailable.

### 9. Search, climate, and task updates need announced status behavior

- **Location:** `EXPERIENCE.md`, State Patterns and Interaction Primitives, lines 61–85; Key Flows 1, 2, and 4, lines 100–121 and 134–141
- **WCAG:** 4.1.3 Status Messages; 3.3.1 Error Identification
- **Evidence:** The design includes ambiguous/unsupported search, offline/stale data, task completion, climate suggestions, and follow-up scheduling. It says messages should be readable and recoverable, but does not define how asynchronous results or updates reach assistive technology.
- **Finding:** Search results, a stale-data warning, successful task completion, or an updated follow-up may appear without moving focus and therefore not be announced. Moving focus to every update would also disrupt the user; the intended distinction is unspecified.
- **Recommendation:** Define polite/live status announcements for non-urgent updates and focused alert/error handling for urgent or blocking states, without stealing focus for routine updates. Include the action/result and relevant freshness or uncertainty in the announced text.
- **Potential consequence:** Screen-reader users may not learn that results arrived, climate data is stale, or an action succeeded or failed.

### 10. Form errors and recovery paths need field-level guidance and focus handling

- **Location:** `EXPERIENCE.md`, Profile context and Observation form, lines 51 and 54; Accessibility Floor, line 96; Flow 3, lines 123–132
- **WCAG:** 3.3.1 Error Identification; 3.3.3 Error Suggestion; 2.4.3 Focus Order
- **Evidence:** The recovery journey asks questions, collects predefined observations, may attach a photo, and can end inconclusively. The general accessibility floor promises recoverable next steps, but no form validation, invalid-field identification, or submit-error focus behavior is described.
- **Finding:** If required context, an observation, or a photo upload is invalid or cannot be saved, the user is not told how the error is associated with its field or where to go to correct it. An inconclusive diagnosis is handled conceptually, but operational input-error recovery is not.
- **Recommendation:** Specify persistent field labels, inline error text programmatically associated with the relevant field, a summary or focus target after failed submission, correction guidance, and preservation of valid entries on retry. Do not rely on color or placeholder text for errors/labels.
- **Potential consequence:** Users with visual, cognitive, or screen-reader needs may not locate or correct a problem and may lose recovery observations.

### 11. Emergency guidance requires an accessible urgent path, not only a severity state

- **Location:** `EXPERIENCE.md`, Information Architecture and State Patterns, lines 26–27 and 72–74; `DESIGN.md`, Components, line 67
- **WCAG:** 2.1.1 Keyboard; 2.4.3 Focus Order; 4.1.3 Status Messages (when presented dynamically)
- **Evidence:** The memlog calls for critical safety warnings in context and a dedicated emergency route. The UX design says emergency exposure has immediate medical/veterinary/poison-control guidance and leaves the routine-care flow, but specifies no keyboard/screen-reader presentation or transition behavior.
- **Finding:** The emergency instruction is high consequence and must remain immediately perceivable and operable without color, pointer, or visual scanning alone. The route's urgency, focus entry/return, and accessible actions are not defined. It is also unclear how a dynamically surfaced exposure alert is announced without delaying or hiding the immediate instruction.
- **Recommendation:** Specify a direct, keyboard-reachable emergency action from relevant safety content; give the screen an urgent, descriptive heading and plain-language first instruction before secondary detail; expose any actionable contact methods with meaningful names; announce dynamic urgent alerts and move focus only when needed to prevent missing an immediate instruction. Ensure the route does not depend on dismissing a dialog first.
- **Potential consequence:** A user relying on keyboard or assistive technology may not reach or understand the time-critical guidance promptly.

### 12. Climate proposals need accessible distinction between proposed and active schedules

- **Location:** `EXPERIENCE.md`, Climate suggestion State Pattern and Interaction Primitive, lines 69 and 81–82; Flow 4, lines 134–141; `.memlog.md`, decision on climate suggestions
- **WCAG:** 4.1.2 Name, Role, Value; 4.1.3 Status Messages; 1.4.1 Use of Color
- **Evidence:** The memlog and UX flow correctly make climate changes advisory: users accept or retain the standard schedule, and stale/missing/conflicting signals use a labeled base schedule. No accessible selected/proposed/active state or announcement after accepting/retaining is defined.
- **Finding:** Users may encounter two schedules and need to distinguish which one is currently active from a recommendation, including when a climate signal is stale or conflicting. Visual layout or color alone could leave a screen-reader user uncertain; updates should not silently change the active schedule.
- **Recommendation:** Present the standard schedule and proposed change as separately named values, identify the active schedule in text and programmatic state, and announce the result of accept/retain. Preserve the active schedule and focus unless the user confirms a proposal; communicate stale/conflicting-data fallback in the same accessible reading order.
- **Potential consequence:** Users may follow a recommendation they did not accept or misunderstand which care schedule remains in force.

### 13. Recovery observations and optional photos need accessible input patterns

- **Location:** `DESIGN.md`, Recovery observation, line 69; `EXPERIENCE.md`, Observation form and Flow 3, lines 54 and 123–132; `.memlog.md`, recovery-journey decisions
- **WCAG:** 1.1.1 Non-text Content; 2.1.1 Keyboard; 3.3.2 Labels or Instructions
- **Evidence:** The memlog and flow call for visible-condition selections, discriminating questions, and an optional photo that is only a record. The design specifies predefined chips/fields but no accessible grouping, labels, keyboard behavior, image alternative, or status for the uploaded record.
- **Finding:** A visual symptom option may not be understandable or operable as an input to a screen-reader/keyboard user; a photo can be optional, but the control and resulting attachment state still need accessible names and feedback. If any plant image conveys a meaningful alternative or observation, “illustrative” alone is not an alternative.
- **Recommendation:** Group related observations with a visible and programmatic prompt; label every option/input; expose selection and attachment/removal status; make upload and removal keyboard operable; provide alternative text for informative imagery and empty alt text for decorative imagery. Keep all diagnosis-relevant information in text, consistent with photo-as-record-only.
- **Potential consequence:** Users may be unable to report the same symptoms or confirm the record they attached, leading to incomplete or misunderstood recovery guidance.

## Verification checklist for implementation

- Test each flow with keyboard only and at least one screen reader on mobile and desktop; include nested/closing sheets, form errors, async search, climate accept/retain, and emergency entry.
- Test text resize to 200%, 400% browser zoom / 320 CSS px reflow, and WCAG text-spacing overrides; verify no clipped, hidden, or two-dimensionally scrollable core content.
- Verify all text and meaningful non-text/control boundaries against their actual backgrounds and states; include focus indicators and disabled/selected states.
- Verify touch target hitboxes, not just their visible glyphs; assert the 44px practical target in both dimensions where intended.
- Confirm assistive technology receives names, roles, values, selections, errors, urgency, freshness, and success states without unnecessary focus movement.

