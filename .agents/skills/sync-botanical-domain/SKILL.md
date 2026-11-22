---
name: sync-botanical-domain
description: Automatically discovers and synchronizes changes under `_bmad-output/domain/` into `botanical-domain-reference`. Use whenever `/sync-botanical-domain` is invoked or the domain documentation needs to be indexed again.
---

# Sync Botanical Domain

## Purpose

Synchronize the local botanical domain documentation into `botanical-domain-reference` without loading or restating the full domain corpus during routing.

## Autonomous Workflow

Execute this workflow every time the skill is invoked. Do not ask the user to list files.

1. **Discover**
   - Run `git status --short _bmad-output/domain/` and `git diff --name-status HEAD -- _bmad-output/domain/`.
   - Enumerate every `*.md` file directly under `_bmad-output/domain/` with `find _bmad-output/domain -maxdepth 1 -type f -name '*.md' -print | sort`.
   - Compute SHA-256 hashes for all discovered files.

2. **Compare**
   - Read `.agents/skills/botanical-domain-reference/domain-sync-manifest.json` if present.
   - Compare the current file list and hashes with the manifest.
   - If no manifest exists, treat every canonical domain file as new for the first synchronization.
   - Classify each file as `added`, `modified`, `removed`, or `unchanged`.
   - Ignore non-canonical duplicate filenames (for example, `04-pet-safety(1).md`) for routing; preserve them in the manifest scan and report them as duplicates rather than domain sources.

3. **Read only the delta**
   - Read only added or modified canonical `.md` files.
   - For a new file, read headings and the first domain section to extract routing themes.
   - Extract only species/categories, R- rules, safety constraints, API contracts, and routing terms.
   - For removed files, remove their routing entry from `botanical-domain-reference` unless the same topic remains covered by another canonical file.

4. **Update routing idempotently**
   - Update `.agents/skills/botanical-domain-reference/SKILL.md` only.
   - Add or replace routing entries by canonical filename, topic, and activation trigger.
   - Do not copy full domain content into the reference skill.
   - Preserve progressive disclosure: agents load only task-relevant domain files and never the whole directory.
   - Preserve the fail-safe hierarchy: `04-pet-safety.md` is mandatory and takes precedence over every species, care, and input recommendation.
   - Preserve API precedence: local deterministic rules override external data; APIs feed asynchronous seed/ingestion and geoclimate pipelines only.
   - Keep all Skill instructions in English and all user-facing domain output guidance in Portuguese.

5. **Record state**
   - If any file hash, file list, routing entry, or canonical presence changed, create or update `.agents/skills/botanical-domain-reference/domain-sync-manifest.json` with:
     - UTC `synced_at` timestamp;
     - SHA-256 hash for every discovered `.md` file;
     - canonical routing status and the previous/last synchronization revision when available;
     - any duplicate or missing canonical filenames detected.
   - On an unchanged file list and hashes with unchanged routing state, make the run a no-op: preserve the existing `synced_at` and do not rewrite the manifest.

## Canonical Routing Rules

- `01-species-catalog.md`: catalog, species, taxonomy, categories, crop and environmental parameters.
- `02-watering-algorithms.md`: watering, substrate, drainage, pots, microclimate, fertilization.
- `03-diagnostics-matrix.md`: symptoms, pests, diseases, deficiencies, treatments.
- `04-pet-safety.md`: maximum-priority safety for pets, children, toxicity, inputs and contamination.
- `05-urban-horticulture.md`: urban cultivation when present.
- `06-bonsai-management.md`: bonsai styling, pruning, wiring, soil exchange and management.
- `07-external-apis-contract.md`: Trefle, Perenual, Flora and Funga do Brasil/Reflora, Embrapa AgroAPI, INMET and Open-Meteo ingestion contracts.
- Any new `00-*.md` or `08+-*.md`: infer the topic from headings and add a precise routing entry without removing existing entries.

## Required Output

After synchronization, report:

- files discovered;
- added, modified, removed, unchanged and duplicate files;
- routing entries changed in `botanical-domain-reference`;
- manifest location and synchronization timestamp.
