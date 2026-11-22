---
title: 'Create botanical-domain-reference skill'
type: 'feature'
created: '2026-11-22'
status: 'done'
route: 'oneshot'
review_loop_iteration: 0
context:
  - '{project-root}/_bmad-output/planning-artifacts/briefs/brief-monitoracao-plantas-2026-11-22/brief.md'
  - '{project-root}/_bmad-output/planning-artifacts/briefs/brief-monitoracao-plantas-2026-11-22/addendum.md'
---

<frozen-after-approval reason="human-owned intent — do not modify unless human renegotiates">

## Intent

**Problem:** The project lacks a reusable botanical domain skill that gives BMad agents a safe, token-efficient route to authoritative agronomic knowledge and complementary API ingestion rules.

**Approach:** Create the `.agents/skills/botanical-domain-reference/SKILL.md` Domain Skill with progressive routing across the six local domain files, pet-safety fail-safe precedence, Trefle/Perenual ingestion-only guidance, activation triggers for relevant agents, and Portuguese output rules.

</frozen-after-approval>

## Implementation Notes

- Created `.agents/skills/botanical-domain-reference/SKILL.md` with progressive routing, activation triggers, API boundaries, fail-safe precedence, and Portuguese output rules.
- Review fixes moved pet safety to the first step of catalog ingestion, clarified Perenual's supplemental role, added conservative local conflict handling, and clarified Portuguese output coverage.

## Review Triage Log

- Finding 1 — false — The blind hunter requested acceptance criteria, but this implementation intentionally follows the one-shot spec template, which permits only the frozen Intent and Implementation Notes.
- Finding 2 — medium, patched — The ingestion sequence previously listed the species catalog before pet safety; it now reads `04-pet-safety.md` first.
- Finding 3 — medium, patched — Added explicit local conflict handling: pet safety always wins and unresolved conflicts remain conservative and auditable rather than being guessed from external data.
- Finding 4 — medium, patched — Perenual watering and thermal fields are now explicitly marked supplemental and incapable of superseding deterministic watering rules.
- Finding 5 — medium, patched — The Portuguese output contract now explicitly covers every user-facing response, including explanations and uncertainty notices.
