# Source-to-PRD Reconciliation

Scope: compare the 22 Nov 2026 brief and addendum, the authoritative domain files, and the drafted PRD/addendum. This reconciliation records only meaningful source requirements or qualitative ideas that are absent or contradicted by the PRD; already represented or merely implementation detail is not repeated.

## 1. Brief: `brief.md`

1. **Guided visual symptom diagnosis is not represented as a complete capability.** The brief makes symptom-guided diagnosis a core MVP capability, and its success criteria require a covered symptom to be resolved locally. The PRD mentions diagnosis in the feature description and lists a symptom observation in FR-15, but it does not define a user journey or stable FR for symptom-to-pest/disease/deficiency/physiological-stress resolution using the deterministic matrix.

2. **The catalog scope is broader than the PRD's implied minimum.** The brief calls for a seed catalog covering indoor ornamentals and relevant Brazilian urban crops. The PRD leaves the launch minimum set, municipalities, and Species Profiles as open questions, but does not state the qualitative breadth principle visible in the domain source: common edible, ornamental, PANC/traditional, and bonsai contexts can coexist in the same profile model. This is less a contradiction than a silently dropped planning priority.

3. **The brief's “clear safety warnings” and 100% safety-critical provenance criterion need stricter product behavior than broad gates.** FR-13/14 cover warnings and explanations, and SM-5 samples 100% of critical recommendations. The brief additionally requires all safety-critical recommendations to be source-checked against the authoritative rules. The PRD does not give an explicit guarantee or testable outcome for universal rather than sampled coverage.

4. **The non-ornamental household problem has a human-scale emergency and prevention layer.** The brief emphasizes accidental household exposure and avoidable risk. The PRD retains general household safety and unknown toxicity, but drops the domain's emotionally important distinction between ordinary uncertainty and high-severity exposure that requires an immediate poison-control/urgent-care pathway and an identifiable plant sample.

## 2. Brief addendum: `addendum.md`

1. **Substrate characterization is under-specified.** The addendum identifies pH, electrical conductivity, water-holding capacity, drainage, and contamination history as relevant to substrate review. The PRD mentions substrate and contamination generally, but has no requirement to capture or expose these measurements, or to distinguish unavailable measurements from confirmed low-risk substrate conditions.

2. **Natural-treatment use is missing washing and waiting guidance as a complete safety flow.** The PRD requires an edible-crop follow-up reminder for “documented waiting or washing interval,” and NFRs mention dosage. The addendum's qualitative requirement is more specific: practical washing and waiting-period instructions must accompany natural treatments. The PRD does not require those instructions to be shown, documented, or traceable to the governing rule at the time of application.

3. **The fail-safe meaning of unknown toxicity is captured, but the higher-severity source-merge idea is weaker.** The PRD says unknown remains `N/A` and restrictive conflict precedence wins. The addendum/source domain also require the product to keep unknowns precautionary until veterinary validation and to preserve ordinary negative/positive meanings without collapsing them into `N/A`. The PRD preserves the first part, but not the merge-quality requirement to distinguish all three states explicitly across the catalog UI and provenance model.

4. **Only catalog and climate integration sources are named, not the full candidate governance landscape.** The addendum identifies Perenual and Trefle for ingestion and names Flora e Funga do Brasil and Embrapa as additional source-governance candidates. The PRD includes only generic external-catalog and climate concepts; it does not preserve this taxonomic/regulatory split or the distinction that Flora/e Funga and Embrapa validate specific domains rather than become runtime care dependencies.

## 3. Domain files under `_bmad-output/domain/`

### `01-species-catalog.md`

1. **The catalog must support agronomic/seasonal crop planning, not only identity and general care.** The source contains planting seasons, propagation, crop cycle, conventional and small-space spacing, useful container depth, temperature, harvest windows, PANC handling, and cultivar-specific guidance. FR-5 says covered rules include watering/fertilization/exposure/pruning/transplant/container guidance, but does not preserve planting, spacing, propagation, or harvest capability as first-class profile and task behavior.

2. **A Species Profile is too underspecified for “exceptions.”** The PRD repeatedly refers to Species Profile exceptions, but no requirement says which species-level fields are authoritative, when a cultivar or PANC overrides a generic crop rule, or how an unresolved scientific identity can avoid inheriting a wrong cultivar/agronomic profile.

### `02-watering-algorithms.md`

1. **Microclimate context is missing as a required Plant-context dimension.** The source makes sheltered, indoor, exposed, balcony, wind, radiation, humidity, and heat materially affect watering and placement. FR-3 captures environment generally, but the PRD does not require an indoor/sheltered/exposed or balcony condition strong enough to explain or recalibrate the water rule; this is especially important for humidity versus misting advice.

2. **Concrete substrate and fertigation constraints are absent.** The source specifies drainage, shallow-bonsai substrates, substrate mixing, hydroretentive material limits, soil compactness, cure/odor checks for compost, pH, electrical conditions, and species-specific fertilizer behavior. The PRD has generic substrate and fertilizer blocks, but not enough normative structure to carry these domain rules into UX or architecture.

3. **Climate and humidity must not trigger misting or overwatering by assumption.** The PRD conservatively says climate can modulate urgency and not override safety. It does not explicitly preserve the source concern that humidity alone is insufficient: airflow, placement, substrate, and disease risk may outweigh a humidity reading.

### `03-diagnostics-matrix.md`

1. **Pest/disease/deficiency diagnosis is not a complete FR.** The source supplies a symptom-led matrix that distinguishes pests, diseases, nutrient deficiencies, physiological stress, and contamination, with practical non-identifying observations and follow-up actions. FR-15 records observations but does not require mapping observations to supported conclusions or excluding unsupported diagnoses.

2. **The matrix separates environmental stress from treatment authority.** The PRD protects against over-care but does not require common physiological causes such as severe restriction, light deficit, pot stress, contamination, or root/soil conditions to remain distinct from pesticide or fungicide recommendations. The source requires non-diagnostic/low-risk first checks before more dangerous interventions.

### `04-pet-safety.md` and `04-pet-safety(1).md`

1. **Emergency poisoning response is missing.** Both safety files require removal from exposure, preservation of a plant sample, and immediate veterinary or medical/CIATox/SINITOX guidance after suspected ingestion. The PRD has Safety Gates and unknown-toxicity warnings, but no FR or journey for this mandatory emergency behavior.

2. **Household-specific handling bans need product-level detail.** The sources block synthetic pesticides/metaldeído and toxic indoor preparations around pets or children, prohibit Euphorbiaceae latex use indoors, ban phosphine fumigation indoors, and require protective handling for latex. FR-13 has generic blocks, but the PRD does not list or preserve these explicit indoor/high-toxicity categories and their required substitutions.

3. **Edible-crop and urban-contamination safeguards are incomplete.** The PRD says edible-crop waiting periods and contamination checks exist. The domain adds strict rules for soil and groundwater laboratory checks, washing/peeling, post-treatment waiting periods, washing hands and changing clothes after urban-soil work, and blocking raw consumption of PANCs with toxic parts. These become testable flows that are currently only broad statements.

4. **The duplicate safety source remains unresolved.** The repository contains `04-pet-safety.md` and `04-pet-safety(1).md` with overlapping but not identical lists and details. The PRD does not mention reconciliation/precedence between these two source files. It relies on a single authoritative safety concept, so conflicting or extra species and handling entries could silently disappear.

### `06-bonsai-management.md`

1. **Bonsai-specific post-transplant and fertilization constraints are not sufficient.** FR-6 mentions the 60/90-day bonsai constraints generally, and FR-5 mentions transplant recovery, so the core is captured. The missing depth is the 15-day post-transplant quarantine with shade/fresh-moisture protection, species-specific watering/pruning windows, substrate renewal intervals, and separation of liquid versus granulated fertilizer windows.

2. **Bonsai handling includes high-risk procedures absent from the MVP behavior model.** The source includes rust/arthropod care, arame removal timing, sealed-container and phosphine restrictions, and external/ventilated preparation of toxic liquid-for-jin. The PRD only carries broad pesticide and treatment blocks; it does not require species-specific manual details or warnings for these bonsai operations.

### `07-external-apis-contract.md`

1. **Taxonomic and regulatory source roles are collapsed into generic catalog ingestion.** The contract distinguishes Perenual enrichment, Trefle taxonomy, Flora e Funga do Brasil taxonomic validation, and Embrapa regulatory validation. FR-16/17 does not preserve which source can validate name/taxonomy versus permissible bioinputs or defensive products, and the PRD should avoid implying Perenual/Trefle can authorize treatment.

2. **API ingestion safety and governance are more specific than FR-16/17.** The source requires asynchronous batched seed loads, Portuguese translation, canonical scientific-name deduplication, preserved original/normalized values, cache/rate-limit handling, and backend-only credentials. The PRD captures most provenance and secret requirements, but not explicit batch/rate-limit/cache behavior or the failover implication for cached, stale catalog fields.

## Contradictions found

- **No direct contradiction was found between the source inputs and the drafted PRD.** The PRD is generally conservative and aligned with the source precedence and MVP deferrals. The differences are primarily dropped specificity, missing testable flows, and a few source roles that are only implicit.

- **Possible scope ambiguity:** the source documents call for deterministic diagnosis and crop/urban-horticulture behavior, while the PRD makes those broad and does not allocate explicit FRs for the diagnosis matrix or the full catalog profile. This is a silent scope gap, not a source contradiction.
