---
title: "Monitoracao Plantas — Visual Design"
status: final
created: 2026-11-25
updated: 2026-11-25
sources:
  - "_bmad-output/planning-artifacts/prds/prd-monitoracao-plantas-2026-11-22/prd.md"
  - "_bmad-output/planning-artifacts/ux-designs/ux-monitoracao-plantas-2026-11-25/.working/direction-botanical-signal.html"
  - "_bmad-output/planning-artifacts/ux-designs/ux-monitoracao-plantas-2026-11-25/.working/color-themes-1.html"
---

# Monitoracao Plantas — Visual Design

## Brand & Style

The visual expression is Botanical Signal with the Dossel tranquilo palette: precise, warm, and natural. It helps a novice understand a plant decision without turning care into a test or punishment. Editorial hierarchy gives the user a quick answer, while context and uncertainty remain visible.

The look is original and uses no existing visual reference or brand asset. The final app name is not decided in this spec.

## Colors

- **Surface:** `#F2F6F0` — primary canvas. Primary text (`#1B2B21`) must reach at least 4.5:1; supporting text (`#4A5E50`) must reach at least 4.5:1.
- **Surface Raised:** `#FFFFFF` — cards and focused decision surfaces.
- **Text Primary:** `#1B2B21` — headings and essential body copy.
- **Text Secondary:** `#4A5E50` — supporting information and nonessential labels.
- **Primary:** `#245C3A` — primary actions and positive fit indicators.
- **Adjustment:** `#C06A3B` — possible correction or next context, never the sole meaning of a warning.
- **Compatible:** `#1E5A37` — a positive compatibility signal.
- **Attention:** `#795000` — missing or review-needed context.
- **Review:** `#9B3732` — unsafe or potentially urgent action that requires attention.
- **Border:** `#C7D5C8` — calm structural separation.

Never communicate fit, urgency, or risk through color alone. Every state also uses a text label and, where appropriate, an icon with an accessible name. Normal text must meet WCAG 2.2 AA at 4.5:1; large text (24px regular or 18.66px bold and above) must meet 3:1. Primary and supporting text pairs on the canvas and raised surface must be verified before theme approval. Status combinations also require 4.5:1 for text and 3:1 for non-text indicators, such as borders or icons.

→ Reference: `mockups/pre-purchase-fit-botanical-signal.html` illustrates the pre-purchase screen. `mockups/color-themes-dossel-tranquilo.html` illustrates the selected semantic palette. The spine pair remains the visual and behavioral contract; the mocks illustrate rather than override it.

## Typography

Use a clear responsive system sans-serif for all interface copy; reserve a subtle editorial display treatment for short page titles when it improves hierarchy. The final font choice remains non-blocking and must preserve dynamic text sizing.

- Body: 16px equivalent at standard desktop/mobile zoom.
- Supporting text: 14px equivalent.
- Page/title text: 24–32px equivalent, with one main heading per screen.
- Long instructions remain in short, complete Portuguese sentences.

## Layout & Spacing

Use a single-column mobile layout and a concise two-column desktop layout for results with context. Base spacing follows 4/8/12/16/24/32px increments.

- Mobile gutters: 16–20px.
- Desktop content width: focused and avoid long-table density.
- Keep search, result, explanation, and primary action in a single visual reading path.
- Plant imagery is illustrative and must not be presented as a diagnosis.

## Elevation & Depth

Use light tonal separation and subtle borders instead of heavy cards or decorative shadows. Elevated surfaces appear only for dialogs or a result that requires immediate focused attention.

## Shapes

Use modest 8–12px corners for cards and inputs. Buttons use 8px corners. Avoid fully pill-shaped containers except compact status tags where a text label remains present.

## Components

- **Plant Search:** title-cased Portuguese label, clear close action, and text search for common/scientific names.
- **Fit Summary:** known facts, missing facts, and result in separate regions.
- **Primary action:** one clear next step, such as complete context or save after login.
- **Alternative Card:** appearance/size similarity, care benefit, and reason; never a guaranteed result.
- **Safety callout:** text, icon, risk, and required action; never color-only.
- **Agenda task:** plant name, action, Safe Window, reason, and accept/retain/complete controls.
- **Observation Form:** predefined chips or fields, optional photo as record, and uncertainty preserved.
- **Profile Context Card:** Available Care Windows and linked Cultivation Locations, with empty and invalid states.
- **Quick Care Summary:** compact care-demand summary with an explicit “non-personalized” label when profile context is missing.
- **Confirmation Sheet:** consequence, reversibility, destructive/non-destructive meaning, cancel, and confirmation focus rules.

## Do's and Don'ts

| Do | Don't |
|---|---|
| Say what is known, missing, or uncertain | Fill missing care data implicitly |
| Use calm, complete sentences | Use guilt, streak pressure, or punitive warnings |
| Put a reason beside every fit or schedule result | Use unexplained compatibility badges |
| Keep safety severity explicit in text | Depend on red, green, or icons alone |
| Preserve light, typography, and state contrast at zoom | Build dense agronomic tables into the main flow |
