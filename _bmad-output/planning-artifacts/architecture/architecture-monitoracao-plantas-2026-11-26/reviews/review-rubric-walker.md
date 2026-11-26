# Reviewer Gate — Rubric Walker

**Target:** `ARCHITECTURE-SPINE.md`  
**Review date:** 2026-11-26  
**Intent:** Validate; no architecture deliverables were modified.  
**Verdict:** **Conditional pass — two medium findings need an explicit architecture/verification response before implementation handoff.** The spine is coherent, lint-clean, and its companions fill in most implementation detail. No critical or high-severity blocker was found.

## Findings

### Medium — Performance requirements have no architecture acceptance path

**Location:** Spine `Design Paradigm`, `AD-4`, `Structural Seed`, and `operations-and-deployment.md` release path.

The PRD §5 sets concrete targets: usable Inventory and Agenda content within 2 seconds at p75, and a recalculation for up to 200 Plants within 3 seconds at p95. The spine fixes the runtime split and deterministic engine, and the operations shard names checks run in CI, but neither establishes where the schedule calculation is measured, representative workload/conditions, or a release gate for these targets. The free-tier CPU limits make this particularly relevant: the architecture has a deployment-cost response for CPU overruns, but no criterion for whether the user-facing latency objective is met.

**Consequence:** Separately built UI/API/domain work can meet the functional ADs while delivering materially different response times, and release checks may pass without detecting a missed product target.

**Disposition:** Discuss / resolve in the architecture or a clearly referenced performance contract. Bind the PRD latency targets to a concrete measurement point, representative 200-Plant workload, and CI or release verification; specify the fallback decision if the Workers budget cannot meet them.

### Medium — Accessibility and supported-browser requirements are not bound to a verification gate

**Location:** Spine `Capability → Architecture Map` and `operations-and-deployment.md` release path.

The PRD §5 requires WCAG 2.2 AA for core Inventory, alert, and task-completion flows, including keyboard, focus, semantic labels, contrast, and non-color-only urgency; it also names current and previous major Chrome, Safari, Firefox, and Edge on desktop and mobile. The cited UX documents contain relevant design/interaction constraints, but the architecture map and release checklist do not carry these acceptance requirements into implementation verification. The current smoke-check list covers product journeys and RLS/media/privacy behavior, not accessibility or browser coverage.

**Consequence:** The shared design contract can exist on paper while independently implemented screens or browser-specific behavior fail the required accessibility/platform baseline without blocking release.

**Disposition:** Defer only if the spine explicitly inherits the UX accessibility/platform contract and the release process names its automated/manual verification gate. Otherwise, add that boundary to the architecture map and release checks.

## Rubric walk

| Checklist dimension | Assessment |
|---|---|
| Divergence points for the level below | **Mostly covered.** The spine establishes ownership, authority/provenance, deterministic rules, safe scheduling, privilege separation, async job behavior, offline snapshot semantics, and account lifecycle. The structural seed and companion shards make the monorepo and worker roles legible. No obvious pair of teams can produce conflicting owners or rule authorities while obeying the stated ADs. |
| Enforceability of each AD | **Pass.** AD-1 has explicit precedence and unknown-to-precaution behavior; AD-2 excludes providers from decision paths; AD-3 specifies row ownership and RLS; AD-4 fixes a versioned TypeScript engine and persistence markers; AD-5 constrains movements and critical-task actions; AD-6 separates credentials and access; AD-7 names bounded/idempotent controls; AD-8 gives a 30-day stale threshold and no offline writes; AD-9 defines a retryable deletion saga and backup disclosure. These are falsifiable implementation constraints, not just aspirations. The shards supply additional transaction and failure details. |
| Deferred ambiguity | **Pass with explicit gates.** Deferred provider caps are set from actual plans before enabling adapters; PostgreSQL major is selected before migrations; privacy wording/retention exceptions are reviewed before production; SMTP and photo-limit changes are gated before launch/pilot. These leave bounded decisions for later rather than silently permitting incompatible implementations. |
| Verified technology | **Pass.** Spine pins claim verification against official docs and package registries on the review date. Independent checks of Node’s distribution metadata, npm registry latest metadata for React, Next.js, TypeScript, Supabase JS, Wrangler, Workers types, and the Supabase CLI GitHub latest release agreed with the listed versions (Node 26.13.1 includes npm 11.20.0; Supabase CLI latest was 3.4.0). The companion stack provides the relevant official documentation links. PostgreSQL major remains intentionally unselected until provisioning and is explicitly deferred to that point. |
| Source-spec capability coverage | **Broad coverage; the two findings above are the gaps.** All six journeys and FR-11–FR-20 are explicitly bound in spine metadata; the capability map and ADs also cover the FR-1–FR-10 inventory, profile, schedule, and safety capabilities, though those ten FR IDs are not listed in `binds`. Catalog, weather, diagnosis, emergency safety, guest fit, offline, account export/deletion, and media are carried through the spine/companions. The PRD’s performance and accessibility/platform NFRs are not yet bound to architecture/release verification. |
| Operational/environmental envelope | **Pass.** The operations shard decides local, staging, and production environments; Supabase/Cloudflare/R2 choices and plan limits; region, deployment/release sequencing, credentials, cost gates, backups/restore, observability, and recovery behavior. Production gates and explicit deferred policy/provider decisions are present. |
| Brownfield/inherited constraints | **Not applicable.** The spine explicitly scopes a greenfield MVP; no existing implementation or parent spine was identified as a ratification constraint. |

## Mechanical check

Ran:

```text
uv run .agents/skills/bmad-architecture/scripts/lint_spine.py --workspace _bmad-output/planning-artifacts/architecture/architecture-monitoracao-plantas-2026-11-26
```

Result: **0 findings** (no placeholder, duplicate AD ID, missing Binds/Prevents/Rule, or unpinned stack-version issues).
