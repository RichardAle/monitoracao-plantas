# Addendum: monitoracao-plantas

## Hybrid Knowledge Architecture

The agronomic and safety domain is authoritative. It includes species catalog scope, watering and substrate algorithms, diagnostic matrices, pet and child safety, urban horticulture, and bonsai management. Public APIs may add missing catalog fields, but the system must normalize and reconcile them without replacing deterministic domain outcomes.

## Technical Parameters to Carry Forward

- Minimum urban container depth: 20 cm for common leafy crops and herbs; 25 cm for roots and tubers.
- Substrate review must include pH, electrical conductivity, water holding capacity, and contamination history where relevant.
- Natural treatment applications require practical waiting periods and washing instructions for edible crops.
- Pets and children trigger fail-safe blocks for indoor toxic preparations and unsafe pesticides.
- Euphorbiaceae latex and unknown toxicity require prominent warnings.
- Local urban soil or groundwater requires contamination checks before food cultivation or child access.
- API ingestion must preserve provenance and distinguish unknown values (`N/A`) from confirmed negative or positive values.

## Open Assumptions from Initial Discovery

- Responsive web is the initial form factor until the first release demonstrates demand for native apps.
- The MVP uses manual entry and observations; IoT sensors are a future integration.
- MVP priorities, in order, are catalog/identity, deterministic watering and container guidance, diagnosis, and safety warnings.
- Trefle and Perenual are ingestion sources rather than runtime dependencies.
