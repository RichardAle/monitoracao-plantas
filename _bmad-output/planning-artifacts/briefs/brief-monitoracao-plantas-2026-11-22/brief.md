---
title: "Product Brief: monitoracao-plantas"
status: draft
created: 2026-11-22
updated: 2026-11-22
---

# Product Brief: monitoracao-plantas

## Executive Summary

**monitoracao-plantas** is a residential plant-care application for Brazilian households, focused on apartments, houses, offices, urban gardens, and bonsai. It combines a localized species catalog with deterministic watering and crop-management rules, visual symptom diagnosis, and fail-safe safety protocols for pets and children.

The MVP prioritizes reliable guidance over ornamental novelty: users register plants and containers manually, receive actionable care reminders, diagnose common symptoms, and see clear safety warnings. Public botanical APIs enrich the catalog, but never override the application's authoritative Brazilian agronomic and domestic-safety rules.

## The Problem

Plant owners face conflicting advice across species databases, social media, and generic gardening applications. General recommendations often ignore the Brazilian climate, container size, substrate, urban contamination, household pets, children, and the specific risks of natural preparations. As a result, users may overwater, misuse agricultural inputs, lose plants, or expose households and food crops to preventable risk.

Care is also fragmented: the user must infer a watering schedule, identify symptoms, check plant toxicity, interpret substrate and pot constraints, and remember post-treatment waiting periods. Existing tools rarely provide an auditable, deterministic recommendation that explains why an action is appropriate.

## The Solution

The product gives each user a plant-care workspace with:

- **Localized catalog:** species, popular names, taxonomy, light, temperature, soil, and toxicology data in Brazilian Portuguese.
- **Deterministic care engine:** IF/THEN rules for watering, pots, substrate, fertilization, urban horticulture, and bonsai management.
- **Visual diagnosis:** guided symptom selection mapped to pests, diseases, nutrient deficiencies, and environmental stress.
- **Safety-first recommendations:** domestic blocks for pets and children, toxic plants, contaminated soil or water, synthetic pesticides, Euphorbiaceae latex, and unsafe indoor preparations.
- **Manual-first MVP:** users enter species, location, container, soil, and care observations without requiring sensors.

Trefle and Perenual serve as off-session catalog enrichment sources. A controlled ingestion and normalization pipeline translates and merges fields into the local database; the runtime journey must not depend on external API quotas.

## What Makes This Different

The differentiator is a **hybrid knowledge architecture**: proven Brazilian agronomic and safety rules govern every recommendation, while public APIs complement gaps in taxonomy and environmental data. The separation is explicit:

1. **Authoritative layer:** the six domain files under `_bmad-output/domain/` define deterministic management and fail-safe safety behavior.
2. **Enrichment layer:** Trefle and Perenual populate catalog gaps and supporting fields.
3. **Decision transparency:** outputs include the applied rule or safety condition, uncertainty, and practical Portuguese next steps.

This avoids the common failure mode of external data silently changing proven care or safety decisions.

## Who This Serves

**Primary user:** Brazilian plant owners with one or more plants in an apartment, house, or office who want practical, trustworthy guidance without specialist knowledge.

**Secondary users:** beginner urban gardeners, food-crop growers, bonsai enthusiasts, and household decision-makers responsible for pets or children.

Success means the user can register a plant, understand its immediate risks, follow an appropriate care schedule, and respond safely to a visible problem.

## Success Criteria

- 100% of safety-critical recommendations are sourced from or checked against the authoritative domain rules; external API fields cannot override them.
- The MVP supports reliable manual registration for indoor ornamentals and common urban crops.
- Watering, pot-volume, substrate, fertilization, and diagnosis outputs are deterministic for covered species and situations.
- Unknown toxicity remains `N/A` with a prominent fail-safe warning rather than being presented as safe.
- Users receive clear waiting periods and rinsing instructions after natural treatments used on edible plants.
- A covered plant or symptom is resolved locally without a synchronous Trefle or Perenual request.

## Scope

### MVP — in

- **[ASSUMPTION] Responsive web application**, suitable for desktop and mobile browsers.
- Manual plant, pot, environment, and care-event registration.
- Seed catalog for indoor ornamentals and relevant Brazilian urban crops.
- Deterministic watering and container/substrate recommendations.
- Symptom-guided diagnostic flow for common pests, diseases, deficiencies, and stress.
- Pet/child safety warnings and blocked unsafe recommendations.
- Async catalog ingestion and normalization for Trefle and Perenual.
- Portuguese user-facing names, symptoms, instructions, and warnings.

### MVP — out

- Continuous IoT sensor integration or hardware provisioning.
- Fully automated visual diagnosis from photographs.
- Advanced expert review, veterinary triage, or medical/agronomic certification.
- Direct external API calls in the live user journey.
- Social, marketplace, or monetized community features.
- Full coverage of every species and every regional climate zone.

## Vision

The product becomes a trusted, locally grounded plant-care companion that grows from an annotated catalog into a personalized care system. Future versions may add consented sensor integrations, richer image-based triage, and region-specific observation signals, but the Brazilian agronomic and safety knowledge remains the auditable source of truth. A long-term goal is to help residential growers reduce plant loss, water misuse, unsafe chemical exposure, and contamination risks across apartments, offices, and urban gardens.
