# Addendum: Monitoracao Plantas

This addendum preserves technical and domain context that informs the PRD without turning capability requirements into an implementation design.

## Source and Precedence Notes

The project separates an authoritative agronomic and household-safety layer from external enrichment. The authoritative layer includes the species catalog, watering algorithms, diagnostic matrix, pet/child safety rules, urban horticulture, and bonsai management. External catalog data may fill missing fields but cannot override agronomic rules, toxicology, dosages, Waiting Periods (carências), or Safety Gates.

The external API contract identifies Perenual and Trefle for catalog enrichment, Flora e Funga do Brasil for taxonomic validation, Embrapa AgroAPI for regulated bioinputs, and INMET/Open-Meteo for climate signals. These are integration candidates and source-governance decisions, not runtime dependencies for the critical user journey.

## Domain Details to Carry Forward

- Common leafy crops and herbs generally require at least 20 cm of useful container depth; roots and tubers generally require at least 25 cm, with Species Profile exceptions.
- Substrate review may include pH, electrical conductivity, water-holding capacity, drainage, and contamination history.
- Bonsai handling includes post-transplant shade and moisture protection, species-specific watering, pruning windows, and 60/90-day fertilizer Waiting Periods where applicable.
- Natural treatments on edible crops require practical washing and waiting-period guidance.
- Unknown toxicity must remain `N/A`, with a precautionary warning rather than a positive safety claim.
- Local urban soil or groundwater requires contamination checks before food cultivation or allowing children access to the area.

## Decisions Kept Out of the Main PRD

- API ingestion should be asynchronous, batched, normalized, translated, and persisted locally to isolate quota and latency from the user's care journey.
- The canonical scientific name is the preferred deduplication key when available.
- Source fields should retain original value, normalized value, source, retrieval time, and merge outcome for auditability.
- Credential storage belongs in backend environment/secret management and must not be exposed in a client bundle.
- The identity provider, backup purge timeframe, and legally required retention periods remain architecture/privacy decisions; product behavior requires an account, cross-device sync, user export, and deletion of active data after confirmation.
- Climate suggestions are advisory and user-accepted only within a validated Safe Window; architecture and domain review will set source-specific freshness thresholds.
- The initial geography is a Taboão da Serra pilot for reviewed common indoor ornamentals, urban edible crops/herbs, and bonsai; expansion across Brazil follows pilot validation and regional rule review.

## Deferred Alternatives

- Native applications are deferred in favor of a responsive web MVP until demand and usage patterns justify platform-specific investment.
- IoT sensors are deferred in favor of manual observations because the product thesis first needs to validate whether a safe, explainable agenda changes care behavior.
- Photo-first diagnosis is deferred in favor of a deterministic guided flow because safety-critical recommendations should not depend on unvalidated image inference.

## UX Discovery: Pre-Purchase Care Fit

The user confirmed an MVP journey where a prospective buyer searches by the common or scientific name on a plant label, checks a Quick Care Summary without an account, and compares care demands to routine and home conditions when profile data is available. If the plant is a poor fit, the app may offer alternatives with similar appearance or size and care demands that better fit the user's routine. An account is required only when saving the plant to the synced Plant Inventory.

Profile setup stores general Available Care Windows and user-defined Cultivation Locations (for example, sunny yard, indirect-light window, shaded window, or no balcony); Plants can be associated with these locations. If context is incomplete, the app must label the result as a quick, non-personalized summary rather than asserting fit. Any specific watering/pruning/fertilizing cadence from the discovery example is illustrative only and must be validated against reviewed Species Profiles.

The user also confirmed guided symptom selection for recovery flows, with optional photos as records only and no automated image diagnosis in the MVP. When symptoms are ambiguous, the experience should explain what observations help distinguish possible causes and offer a safe, validated intervention. The example of white residue and possible pests or disease is not an authoritative domain rule and requires domain review before any recommendation is derived from it.

The product voice should be friendly and welcoming, never punitive when users miss care or a plant struggles; recovery suggestions should fit the user's available time.

## Landscape Research Notes

The discovery research reviewed public positioning from Planta, PictureThis, and Pl@ntNet. These products validate demand for inventory, reminders, identification, and care content, but public pages do not demonstrate this product's transparent recalculation from Brazilian location, microclimate, container, substrate, and observed plant state. This supports differentiating on adaptive, explainable care rather than on reminders or catalog breadth alone.

Research also flagged boundaries that remain important for downstream UX and safety work:

- Plant identification must not imply edibility, medicinal safety, or treatment authorization.
- Pesticide and agrochemical guidance is regulated and context-dependent; unsafe mixtures and unregistered products should not be recommended.
- Brazilian weather and urban microclimates vary materially; an indoor/sheltered/exposed context is needed before interpreting a Climate Signal.
- Humidity should not automatically produce misting advice; airflow, placement, substrate, and disease risk matter.

Sources: [Planta](https://getplanta.com/), [PictureThis](https://www.picturethisai.com/app), [Pl@ntNet](https://identify.plantnet.org/), [Ministério da Saúde](https://www.gov.br/saude/pt-br/composicao/sectics/plantas-medicinais-e-fitoterapicos), [Anvisa](https://www.gov.br/anvisa/pt-br/assuntos/agrotoxicos), and [INMET BDMEP](https://bdmep.inmet.gov.br/). The competitor findings reflect advertised capabilities, not independent accuracy or usability benchmarks.
