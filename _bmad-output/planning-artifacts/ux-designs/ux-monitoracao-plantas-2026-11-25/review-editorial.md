# Editorial Review

This document set exists to help product and design readers translate the Monitoring Plants vision into consistent visual and interaction decisions.

**Structure model:** `DESIGN.md` is closest to a reference/database model; `EXPERIENCE.md` combines a reference model for surfaces, patterns, and states with short tutorial-style flows.

| Pass | Original Text | Revised Text | Changes |
|---|---|---|---|
| structure | `EXPERIENCE.md` — Flow 1–4 (399 words across the Key Flows section) | CONDENSE **Climax** labels and repeated failure/outcome framing | Repeated “Climax” and “Failure” blocks add ceremony and compete with the contracts in the surrounding tables. Use a direct **Outcome** or **Failure** label as needed. Preserve the flows and their recoveries. Saves a small number of words; exact savings depend on final labels. |
| structure | `EXPERIENCE.md` — Component Patterns, State Patterns, and Interaction Primitives | QUESTION or MERGE repeated rules | Rules for safe windows, destructive confirmation, and tasks recur across these sections. Keep random-access tables, but make each rule a single source of truth and refer to it elsewhere (cuts duplication without changing the contract). |
| structure | `EXPERIENCE.md` — Information Architecture Closure (46 words) | CONDENSE or CUT | This closes the overview by restating the four surfaces and MVP scope already established above. Keep only the forward-looking decision about specialist flows. Estimated reduction: about 25–35 words. |
| structure | `DESIGN.md` — Brand & Style + Do's and Don'ts | PRESERVE, then cross-reference | They have different jobs: Brand & Style establishes the visual principle; Do's and Don'ts is an implementation check. Keep both and avoid duplicating the same principle sentence-for-sentence. |
| prose | `DESIGN.md` — “Desktop content width: focused and avoid long-table density.” | “Keep desktop content width focused, and avoid dense tables.” | Fixes the incomplete/ambiguous phrase while preserving the layout constraint. |
| prose | `EXPERIENCE.md` — “Tone is friendly, complete, and practical.” | Consider: “The tone is friendly, complete, and practical.” | Adds an explicit subject and reads more naturally. |
| prose | `EXPERIENCE.md` — “No UI system is named; the app follows platform-conventional, keyboard-accessible web behavior.” | Consider: “No UI system is specified. The app follows platform-conventional, keyboard-accessible web behavior.” | Replaces a compressed semicolon phrase with clearer two-sentence guidance. |
| prose | `DESIGN.md` — “The final font choice remains non-blocking and must preserve dynamic text sizing.” | Consider: “The final font choice must preserve dynamic text sizing and does not block implementation.” | Makes the condition explicit and avoids “non-blocking” as a vague status. |

## Summary

- **Top findings:** remove/reduce the repeated UX rules and closure prose; clarify the two ambiguous layout/foundation sentences; replace the dramatic “Climax” labels with direct flow outcomes.
- **Estimated reduction:** approximately 25–50 words across both documents if the structure recommendations are accepted; neither document appears long enough to require aggressive cutting.
- **Comprehension trade-off:** preserving the tables and four flows is more valuable than optimizing for brevity. Avoid cutting examples, warnings, or the distinction between known, missing, and uncertain states.
