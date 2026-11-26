---
title: "Monitoracao Plantas — User Experience"
status: final
created: 2026-11-25
updated: 2026-11-25
sources:
  - "_bmad-output/planning-artifacts/prds/prd-monitoracao-plantas-2026-11-22/prd.md"
  - "_bmad-output/planning-artifacts/prds/prd-monitoracao-plantas-2026-11-22/addendum.md"
  - "_bmad-output/planning-artifacts/briefs/brief-monitoracao-plantas-2026-11-22/brief.md"
---

# Monitoracao Plantas — User Experience

## Foundation

Responsive web is the initial form factor, with phone-first use and desktop support. No UI system is named; the app follows platform-conventional, keyboard-accessible web behavior. `DESIGN.md` is the visual contract. The POC favors simple onboarding, low input effort, and plain-language explanations.

## Information Architecture

| Surface | Reached from | Purpose |
|---|---|---|
| Encontrar planta | Cold open / global navigation | Guest search and pre-purchase care-fit explanation |
| Agenda | App navigation; post-save return | Weekly/monthly care tasks, climate suggestions, and completion |
| Minhas plantas | App navigation; save confirmation | Inventory, Plant detail, care history, guided diagnosis |
| Perfil | Header/settings; onboarding and context prompts | Account, Available Care Windows, Cultivation Locations, privacy/export/deletion |
| Safety alert detail | Plant detail or Care Task | Explain risk, prevention, and required action |
| Emergency exposure path | Plant or product safety content | Immediate toxic-exposure instruction; leaves routine-care flow |

The mobile navigation uses four primary destinations: Encontrar planta, Agenda, Minhas plantas, and Perfil. No account is required for plant search or a Quick Care Summary. Account creation or login is required to save and synchronize a Plant Inventory.

## Voice and Tone

| Do | Don't |
|---|---|
| “Vamos confirmar o encaixe antes da compra.” | “Você pode prejudicar sua planta.” |
| “Seu perfil ainda não informa luz e ventilação.” | “Dados insuficientes.” |
| “Esta é uma possibilidade; observe X antes de agir.” | “A planta tem fungo.” |
| “Não deu para encaixar nesta semana. Veja seu próximo horário seguro.” | “Você perdeu esta tarefa.” |
| “Se não melhorar, procure apoio especializado.” | “Você fez a planta piorar.” |

Tone is friendly, complete, and practical. It assumes a novice may lack botanical vocabulary. Safety language is direct but never shaming. Success means survival and continuing growth/flowering/fruiting beyond the store-bought state, not streak or perfection.

## Component Patterns

| Component | Use | Behavioral rules |
|---|---|---|
| Plant search | Encontrar planta | Search common or scientific name; preserve ambiguous and unsupported states |
| Plant Search | Encontrar planta | Search common or scientific name; preserve ambiguous and unsupported states |
| Quick Care Summary | Guest lookup | Show high-level care demands and missing context; label illustrative values |
| Fit Summary | Pre-purchase and profile context | Separate known, missing, and result; explain why the result exists |
| Alternative Card | Poor-fit result | Similar appearance or size plus a care compatibility reason; no survival guarantee |
| Profile Context Card | Plant/location/profile setup | Link Available Care Windows and Cultivation Locations; preserve unknown values |
| Agenda Task | Agenda and Plant detail | Show Plant, action, due/Safe Window, reason, and accept/retain/postpone/dismiss controls |
| Safety Callout | Plant/task/treatment/diagnosis | Text + icon + risk + required precaution + block or acknowledgment state |
| Observation Form | Recovery and Plant detail | Predefined visible conditions, discriminating follow-up questions, optional photo as record only |
| Confirmation Sheet | Save, delete, postpone, dismiss | State consequence and reversibility; cancel remains available |

## Responsive & Platform Behavior

- Mobile: four primary destinations use an accessible bottom or top navigation pattern with labels; no horizontal scrolling is required.
- Desktop: navigation may become a labeled side rail or top rail, but destinations and reading order remain identical to mobile.
- Search, fit summary, alternatives, and safety callouts reflow from two columns to one below the smallest usable desktop breakpoint.
- Zoom and dynamic text reflow all cards and controls without clipping or simultaneous keyboard interaction. Tables become labelled task lists rather than forcing horizontal scrolling.
- Dialog/sheet focus remains inside the active layer, initial focus starts on the purpose or first required field, and closing restores focus to the triggering control.

## State Patterns

| State | Surface | Treatment |
|---|---|---|
| Guest lookup | Encontrar planta | Search works without account. Saving creates sign-in/account prompt. |
| Search empty/loading/error | Encontrar planta | Empty: explain search examples. Loading: retain query and accessible status. Error: offer retry and cached catalog when available. |
| First run/zero items | Agenda, Minhas plantas | Show the minimum setup needed and a clear first action; do not invent Plants or tasks. |
| Cold load/sync partial/error | Agenda, Minhas plantas | Show cached data, last sync time, partial-sync explanation, retry, and read-only behavior where applicable. |
| Validation error | Profile, Plant form | Identify the field, preserve user input, expose a recovery action, and move focus to the first error. |
| Unauthenticated save | Any Plant save | Explain that saving requires an account, offer sign-in/create account and cancel; do not discard entered Plant context. |
| Export/delete/account deletion failure | Perfil | Preserve state, explain what succeeded or failed, and provide retry/cancel/retention status. |
| Permission denied | Location/photo/account | Explain the denied capability, the nonessential fallback, and a settings or manual-entry alternative. |
| Profile incomplete | Fit summary | Show Quick Care Summary and missing context; never imply personalized fit. |
| Identity ambiguous | Search/result | Ask for name/species confirmation. Do not inherit species safety or care rules. |
| Unsupported profile | Search/detail | Inventory may retain the Plant, but no deterministic care claim is shown. |
| Known fit | Fit summary | State known factors and why they match. |
| Poor fit | Fit summary | Offer one or more alternatives; explain appearance/size/effort difference. |
| Critical task | Agenda/detail | Emphasize deadline and required action. Cannot be postponed/dismissed. |
| Routine task | Agenda/detail | User may complete, postpone, or dismiss with a short reason. |
| Climate suggestion | Agenda/detail | Show standard schedule, proposed safe change, reason, and accept/retain controls. |
| Offline | Inventory/agenda | Show last sync and read-only fallback for cached information. |
| Stale data | Climate/catalog | State that fallback care rules are used or data is stale. |
| Unknown toxicity | Plant detail/safety | `N/A` with precaution, never “safe.” |
| Emergency exposure | Any safety content | Remove source, preserve sample, and immediate medical/veterinary/poison-control guidance. |
| Diagnosis inconclusive | Recovery | Preserve uncertainty and offer safe observation or professional-help next step. |

## Interaction Primitives

- One primary action per decision: search, complete context, save, accept, retain, complete, postpone, or dismiss.
- Search begins without account friction; saving is the first gated account action.
- Profile setup prefers short, labelled choices over free-text botanical fields.
- A climate change is always advisory and user-accepted within a Safe Window.
- A Care Task cannot silently shift outside its Safe Window.
- Non-critical tasks require a reason for postponement or dismissal; critical tasks remain non-overridable.
- Optional photos are attachments to observations, not diagnosis inputs.
- Confirmation is explicit for destructive account or inventory actions.

## Accessibility Floor

- Target WCAG 2.2 AA for core inventory, fit, agenda, recovery, alert, and account flows.
- Support stable current and previous major versions of Chrome, Safari, Firefox, and Edge on desktop and mobile.
- Keyboard focus is visible and follows reading order.
- Interactive targets meet a practical minimum of 44px CSS height and remain usable at browser zoom and dynamic text sizes.
- Every icon has a text label or accessible name; severity never relies on color.
- Focus opens or closes one dialog/sheet at a time.
- Reduced-motion preferences remove nonessential transitions.
- Error, warning, and success states include readable text and recoverable next steps.

## Key Flows

### Flow 1 — [UJ-5] Marina checks a plant before purchase

1. Marina opens the app at a store and searches by the common or scientific name on the label.
2. The app shows a Quick Care Summary: care demand, basic light/ventilation needs, and what is known or missing.
3. If her profile is ready, the app compares routine and linked Cultivation Locations and states fit or mismatch with the reason.
4. If fit is poor, it offers similar-looking or sized alternatives with care demands that better match her routine.
5. The app does not create an account merely to provide this comparison.
6. If Marina buys the plant and chooses to save it, the app prompts account creation/login.
7. **Climax:** Marina understands whether the plant is likely to fit her life and sees a clear next step, not a purchase guarantee or guilt.

Failure: identity ambiguous or profile unsupported → explain uncertainty and do not invent Care Fit.

### Flow 2 — [UJ-2] Marina fits her plant into her care week

1. Marina creates an account and configures general Available Care Windows plus home Cultivation Locations.
2. She adds a plant and confirms its linked location and basic context.
3. The Agenda proposes watering, fertilization, exposure, pruning, or treatment tasks inside safe windows.
4. Marina accepts a safe proposal or retains the standard schedule.
5. She completes or records a task and sees the next Safe Window.
6. **Climax:** The app turns a plant need into a small, explainable action that fits her routine.

Failure: no safe window exists before a critical task → escalate without silently postponing.

### Flow 3 — [UJ-6] Marina records a struggling plant

1. Marina opens the Plant detail and selects “Ajudar a recuperar.”
2. She chooses predefined symptoms and, optionally, attaches a photo as a record.
3. The app asks distinguishing questions and presents possible causes with uncertainty.
4. It offers a validated intervention or safe observation tied to the next free time.
5. It schedules a follow-up and records the selected action.
6. **Climax:** Marina has a practical recovery step and a welcoming explanation of what to observe next.

Failure: signals remain inconclusive → preserve uncertainty and route to safe observation or professional help.

### Flow 4 — [UJ-3] Marina sees a climate suggestion

1. Marina opens a task with a climate suggestion.
2. The app shows the standard schedule, local signal, its freshness, the proposed safe change, and the reason.
3. Marina accepts or retains the standard schedule.
4. **Climax:** She feels control and understands that climate did not silently override agronomic/safety rules.

Failure: stale/missing/conflicting signal → use and label the deterministic base schedule.

### Flow 5 — [UJ-1] Marina turns a mixed collection into a trustworthy inventory

1. Marina signs in and opens an empty or partial Minhas plantas view.
2. She searches or adds a plant name and records its location, Cultivation Location, container, substrate, and household context.
3. If identity is ambiguous, the app asks for species confirmation and avoids applying species-specific rules while unresolved.
4. For incomplete safety or environmental data, the app shows `N/A` or an uncertainty label and an actionable next check.
5. After each Plant is saved, the app shows its next task, unresolved data, and active Safety Gates.
6. **Outcome:** Marina has an understandable collection and can open the Agenda or a Plant detail.

Failure: no species match or no reviewed profile exists → retain the inventory entry in an unsupported state and provide no deterministic care claim.

### Flow 6 — [UJ-4] Marina batches compatible pruning and fertilization work

1. Marina opens the monthly Agenda and filters pruning and fertilization tasks.
2. The app groups compatible tasks by location and care type and lists every Plant before confirmation.
3. It excludes incompatible species, Waiting Period, toxicity, transplant, or treatment conflicts with a visible reason.
4. Marina reviews and confirms the batch, recording completed and skipped tasks separately.
5. The app recalculates only affected tasks and shows the next Safe Window.
6. **Outcome:** Marina completes compatible work in one visit without bypassing conflicts or hidden restrictions.

Failure: no compatible batch can be created → show the individual tasks and preserve all safety exclusions.

## Information Architecture Closure

The four primary surfaces deliver every current MVP need: compare before purchase, plan and complete care, maintain and diagnose the inventory, and configure routine/home context/account privacy. Specialist flows remain within detail/alert surfaces and do not create permanent top-level destinations unless later usage shows they need one.

Canonical names for reusable components are defined in Component Patterns and must match the names in DESIGN.md; no synonym is introduced in downstream stories. The selected mock is an implementation reference for the pre-purchase flow: `mockups/pre-purchase-fit-botanical-signal.html`. If a mock and this spine disagree, the spine pair is authoritative; the selected palette reference is `mockups/color-themes-dossel-tranquilo.html`.
