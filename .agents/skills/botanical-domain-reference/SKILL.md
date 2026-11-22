---
name: botanical-domain-reference
description: Provides authoritative botanical, agronomic, diagnostic, pet-safety, urban-horticulture, bonsai, and external-api domain knowledge for plant database modeling, watering and care engines, symptom diagnosis, safety alerts, and asynchronous API ingestion. Use whenever @pm, @architect, @sm, @dev, or @qa works on these plant-domain concerns.
---

# Botanical Domain Reference

## Purpose

Use this skill to ground implementation decisions in the project's two-layer botanical knowledge architecture:

1. **Authoritative local source:** deterministic rules in `_bmad-output/domain/`.
2. **Complementary enrichment source:** Trefle and Perenual data ingested offline and stored in the project database.

Keep internal reasoning and technical instructions in English. Return Brazilian Portuguese names, symptom labels, warnings, and user-facing guidance to users.

## Activation

Activate this skill whenever `@pm`, `@architect`, `@sm`, `@dev`, or `@qa` is working on:

- Plant database modeling or plant catalog fields.
- Trefle or Perenual ingestion, normalization, or seed scripts.
- Watering, fertilization, drainage, substrate, pot, microclimate, pruning, or bonsai rules.
- Symptom diagnosis, pests, diseases, nutritional deficiencies, or treatments.
- Safety alerts involving pets, children, toxic plants, contaminated soil or water, or defensives.

## Fail-Safe Hierarchy

Before recommending a species or treatment:

1. Read `_bmad-output/domain/04-pet-safety.md` first for every safety-sensitive task.
2. If the task is about species identity or category, then route to the topic file below.
3. Treat unknown toxicity as `N/A`, never as safe, and retain a prominent safety warning.
4. Never allow Trefle or Perenual fields to override local safety or deterministic care rules.
5. Apply domestic blocks before API-derived recommendations:
   - Block synthetic pesticides and unsafe fumes in homes with pets or children.
   - Block Euphorbiaceae latex and caustic preparations from indoor use.
   - Require soil and groundwater contamination checks for urban food cultivation.
   - Require waiting periods, rinsing, and edible-plant precautions after natural treatments.

## Progressive Disclosure Routing

Do not load all domain files at once. Start with the routing category that matches the task, then load only that file. Pet and child safety always remains an additional mandatory read.

| Task theme | Load only |
|---|---|
| Species catalog, categories, taxonomic gaps, environmental parameters | `_bmad-output/domain/01-species-catalog.md` |
| Watering, substrate, drainage, pot sizing, microclimate, fertilization | `_bmad-output/domain/02-watering-algorithms.md` |
| Symptom diagnosis, pests, diseases, deficiencies, controls | `_bmad-output/domain/03-diagnostics-matrix.md` |
| Toxicity, pets, children, poisonous preparations, domestic safety blocks | `_bmad-output/domain/04-pet-safety.md` |
| Urban cultivation, pot volume, soil audit, contamination, small-space gardening | `_bmad-output/domain/05-urban-horticulture.md` (not currently present; no file should be invented or loaded) |
| Bonsai styling, pruning, wiring, miniaturization, feeding, or soil exchange | `_bmad-output/domain/06-bonsai-management.md` |
| External API/seed/geoclimate contracts, provenance, quotas, merge priorities | `_bmad-output/domain/07-external-apis-contract.md` |

## API Enrichment Boundary

Route `_bmad-output/domain/07-external-apis-contract.md` for all external API and geoclimate ingestion questions. Its hybrid contract covers Perenual, Trefle, Flora and Funga do Brasil/Reflora, Embrapa AgroAPI, INMET, and Open-Meteo.

### Trefle API

Use Trefle ingestion to complement catalog normalization:

- Standardize scientific name and taxonomy: family and genus.
- Add ecological baseline fields only, such as pH range, luminosity, and minimum temperature.
- Preserve provenance and source identifiers for every enriched field.
- Keep external values separate from authoritative local fields.

### Perenual API

Use Perenual ingestion to enrich interior ornamental catalog records:

- Light level.
- Base watering frequency.
- Tolerated thermal range.
- Images.
- Toxicity flags for dogs, cats, and children.

Perenual enrichment remains supplemental: watering frequency and thermal range must not supersede `_bmad-output/domain/02-watering-algorithms.md`, and toxicity flags cannot be more authoritative than the validated local pet-safety rules.

### Additional API Sources

- Use Flora and Funga do Brasil/Reflora only to validate accepted native taxonomy, synonyms, life form, endemism, and Brazilian occurrence.
- Use Embrapa AgroAPI only to validate registered commercial bioinputs and defensives under Brazilian regulation; never bypass the residential safety blocks in `04-pet-safety.md`.
- Use INMET or Open-Meteo only to periodically/geospatially adjust watering and thermal alerts derived from `02-watering-algorithms.md`.

### Mandatory Architectural Rules

- Consume Trefle and Perenual only in asynchronous ingestion or seed pipelines.
- Never call either API synchronously in a runtime user journey.
- Never let external quota or response availability block local plant care decisions.
- Normalize field names and units, then translate interface text to Portuguese.
- Store provenance and distinguish confirmed positive/negative values from `N/A` unknown values.
- Merge API data without replacing deterministic local outcomes.
- Seed unknown toxicity as `N/A`; only confirmed, reviewed toxicity may change that state.
- If API and local evidence conflict, preserve the local authoritative rule and record the conflict for review.
- If local domain files conflict, `04-pet-safety.md` always takes precedence. For any other conflict within the routed task, keep the more restrictive local rule and mark the conflict for human agronomic review instead of guessing or calling an external API.

## Ingestion Workflow

For catalog ingestion tasks:

1. Read `_bmad-output/domain/04-pet-safety.md` and initialize or validate domestic-safety fields.
2. Resolve and validate scientific identity.
3. Apply `_bmad-output/domain/01-species-catalog.md` to constrain category and baseline parameters.
4. Fetch Trefle and Perenual only through an asynchronous pipeline or seed script.
5. Normalize names, units, ranges, images, and provenance.
6. Translate popular names, symptoms, messages, and flags for Brazilian Portuguese presentation.
7. Persist external source fields separately from authoritative local fields.
8. Add an audit trail for conflicts, unknown values, and skipped updates.

## Deterministic Decision Workflow

For recommendations:

1. Load only the task-specific local domain file(s), after mandatory pet-safety review.
2. Map the user's species, pot, substrate, environment, symptoms, and household risks.
3. Apply local `SE/ENTÃO` rules as the decision source.
4. Use API-enriched fields only as complementary context or unresolved gaps.
5. Explain the applied rule and practical next step in Portuguese.
6. Do not present uncertainty as certainty; preserve `N/A` and warnings.

## Portuguese Output Contract

Use Portuguese for:

- Every user-facing response, including rule explanations, uncertainty notices, and next steps.
- Common and popular plant names.
- Symptom names and diagnosis labels.
- User-facing warnings and blocked actions.
- Waiting periods and washing instructions.
- Recommended next steps and uncertainty notices.

Use English for source names and internal code identifiers only when the technical identifier is not user-facing.

## Synchronization State

The latest synchronization manifest is [`domain-sync-manifest.json`](./domain-sync-manifest.json). It contains the discovered file list, SHA-256 hashes, canonical routing status, duplicate detection, and UTC synchronization timestamp. Re-run `/sync-botanical-domain` after domain changes; it is an idempotent hash comparison, not a full-domain reload.
