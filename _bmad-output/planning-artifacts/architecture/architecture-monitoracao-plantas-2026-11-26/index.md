---
title: "Architecture Index: monitoracao-plantas"
status: final
updated: 2026-11-26
---

# Architecture Index

This folder is the build contract for the greenfield MVP. Read the spine for invariants, then load only the shard needed for the story or implementation task. The documents are technical English; user-facing domain terms, UI states, and messages remain Brazilian Portuguese.

## Read order

1. `ARCHITECTURE-SPINE.md` — paradigm, enforceable decisions, stack, and system boundaries.
2. One focused shard from the table below.
3. Only the domain source(s) routed by that feature. `04-pet-safety.md` is mandatory for every safety-sensitive change. Do not load all domain files at once.

| Shard | Use when working on |
| --- | --- |
| [tech-stack.md](./tech-stack.md) | Web, API, cache, runtime, libraries, hosting, or cost limits |
| [database-schema.md](./database-schema.md) | Species, provenance, user data, tasks, batches, media, or migrations |
| [rules-and-scheduling-engine.md](./rules-and-scheduling-engine.md) | Care rules, diagnosis, Safe Windows, agenda, climate proposals, or batches |
| [async-ingestion-and-weather.md](./async-ingestion-and-weather.md) | Provider adapters, finite enrichment queue, Cron, locks, retries, or weather |
| [auth-and-security.md](./auth-and-security.md) | Guest/account boundaries, RLS, exports, deletion, privacy, or uploads |
| [operations-and-deployment.md](./operations-and-deployment.md) | Environments, secrets, backups, observability, production gates, or budgets |

## Authority and source artifacts

The PRD owns product behavior and FRs; `EXPERIENCE.md` owns interaction and information architecture; `DESIGN.md` owns visual behavior. The local domain documents own agronomic and safety truth. This architecture fixes only the boundaries needed to keep independently built stories consistent.

- Product brief: [brief.md](../../briefs/brief-monitoracao-plantas-2026-11-22/brief.md)
- PRD: [prd.md](../../prds/prd-monitoracao-plantas-2026-11-22/prd.md) and [addendum.md](../../prds/prd-monitoracao-plantas-2026-11-22/addendum.md)
- UX behavior: [EXPERIENCE.md](../../ux-designs/ux-monitoracao-plantas-2026-11-25/EXPERIENCE.md)
- UX visual contract: [DESIGN.md](../../ux-designs/ux-monitoracao-plantas-2026-11-25/DESIGN.md)
- Domain sources: [01-species-catalog.md](../../../domain/01-species-catalog.md), [02-watering-algorithms.md](../../../domain/02-watering-algorithms.md), [03-diagnostics-matrix.md](../../../domain/03-diagnostics-matrix.md), [04-pet-safety.md](../../../domain/04-pet-safety.md), [06-bonsai-management.md](../../../domain/06-bonsai-management.md), and [07-external-apis-contract.md](../../../domain/07-external-apis-contract.md).

`05-urban-horticulture.md` is not present in the domain folder. Do not invent its rules; affected edible-crop profiles may be inventoried but remain `não suportado`, with no crop-specific care or contamination clearance, until the authoritative source is reviewed and added. Enabling this pilot capability is a domain-source release prerequisite.

## MVP scope anchors

- Pilot: Taboão da Serra, São Paulo; reviewed indoor ornamentals, urban edible crops/herbs, and bonsai.
- Guest `Encontrar planta` search and care-fit simulation work without an account. Saving to `Minhas plantas` requires authentication.
- Offline mode is read-only for already synchronized Inventory and Agenda snapshots. Show last sync; data older than 30 days is `obsoleto` and cannot represent current state.
- No native app, installable PWA, offline writes, IoT, automated photo diagnosis, ML, social/community/commerce, shared household inventory, push, SMS, or care-reminder e-mail.
- The Agenda is the sole MVP surface for reminders, critical tasks, food-crop waiting/washing follow-ups, and the isolated toxic-exposure route.
