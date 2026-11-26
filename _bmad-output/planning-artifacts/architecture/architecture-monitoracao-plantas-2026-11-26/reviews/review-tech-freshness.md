# Technology and Version Freshness Review

**Reviewed:** `ARCHITECTURE-SPINE.md`  
**Scope:** Technology/version existence, currentness, fit with the described design, and the evidence behind the document’s freshness claims. Companion stack/deployment shards were consulted only for context.  
**Checked:** 2026-11-26 against official project documentation, Node.js release metadata, and the npm registry.

## Verdict

**Mostly verified; two freshness qualifications remain.** Every concrete software pin in the spine exists in the authoritative release/package sources checked. Node.js is LTS, the selected versions fit the stated static Next.js + Cloudflare Workers + Supabase design, and no incompatible runtime pairing was found. The npm pin is valid and exactly matches the npm bundled with the chosen Node release, but it is no longer the npm registry’s latest. Supabase PostgreSQL is intentionally not pinned yet, so its exact major and project-level support cannot be verified until provisioning.

## Findings

### 1. npm pin is valid but behind the current registry release

**Location:** `ARCHITECTURE-SPINE.md`, Stack table, npm workspaces row (lines 107–109).

`npm@11.20.0` exists and is the npm bundled by `Node.js v26.13.1`; its engine range accepts Node 26. The npm registry’s current `latest` tag is already `12.2.3`, whose engine range also accepts Node 26.13.1. Thus the current pin is compatible and may be an intentional bundled-toolchain choice, but it is not the latest npm release. The general statement that versions were checked against registries does not clarify whether latest versions or the Node-bundled npm were intended.

**Recommendation:** Either explicitly call out that npm 11.20.0 is pinned to match the Node distribution, or move to the tested npm 12 release and record the selected pairing. No architecture incompatibility is implied by keeping 11.20.0.

**Evidence:** [Node.js release index](https://nodejs.org/dist/index.json) (v26.13.1 lists npm 11.20.0); [npm 11.20.0 metadata](https://registry.npmjs.org/npm/11.20.0); [current npm metadata](https://registry.npmjs.org/npm/latest) (latest 12.2.3; Node engine includes `>=26.0.0`).

### 2. PostgreSQL major is a provisioning-time decision, not a currently verifiable pin

**Location:** `ARCHITECTURE-SPINE.md`, Stack table and final implementation gates (lines 116, 171).

“Stable supported major selected and pinned at project provisioning” deliberately postpones the exact PostgreSQL major. This is consistent with the final gate requiring it to be pinned before the first migration, but no concrete major can presently be checked for support or feature compatibility. Supabase’s platform docs describe project-specific database versions and a separate major-upgrade process, so this should remain an explicit implementation gate rather than be read as a version verified by this review.

**Recommendation:** Before provisioning/committing migrations, record the actual Supabase PostgreSQL major and verify the SQL features/extensions used by the schema against that project’s supported version.

**Evidence:** [Supabase PostgreSQL upgrade guidance](https://supabase.com/docs/guides/platform/upgrading).

## Pin-by-pin verification

| Spine commitment | Registry/release check | Fit with described architecture |
| --- | --- | --- |
| Node.js `26.13.1 LTS` | Exists; Node’s release index dates it 2026-11-10, marks it LTS (“Lithium”), and lists npm `11.20.0` bundled. | Fits build/tooling use. Next requires Node `>=20.9.0`; Wrangler `4.168.0` requires `>=22.12.0`; both accept Node 26. |
| npm workspaces `11.20.0` | Exists; it is bundled with Node `26.13.1`. Registry latest is `12.2.3` (finding 1). | npm workspaces functionality is compatible; the chosen version’s Node engine range accepts Node 26. |
| Next.js `16.4.6` | Exists and was the npm `latest` tag at review time. | Package accepts React/React DOM `^19.0.0`. Next documents `output: "export"` and static asset deployment; Cloudflare documents static Next.js export on Pages. The proposed static web + separate Workers boundary matches those constraints. |
| React / React DOM `19.3.1` | Both packages exist; both were their registry `latest` versions at review time. | Compatible with Next.js `16.4.6`’s React peer range. |
| TypeScript `7.0.2` | Exists and was the registry `latest` version at review time. | Next.js documentation explicitly describes using TypeScript 7 with its local `tsc` CLI. No incompatibility found. |
| `@supabase/supabase-js` `2.119.2` | Exists and was the registry `latest` version at review time; package Node engine is `>=22.0.0`. | Fits Node 26 and the Supabase Auth/Data API use described. Supabase documents its REST Data API as an HTTPS/PostgREST layer supporting PostgreSQL RLS and RPCs. |
| Supabase CLI `3.4.0` | Exists and was the `supabase` package registry `latest` version at review time. | Official CLI docs describe local Supabase development and require Node 20+ for npm/npx installation; Node 26 meets that requirement. |
| Cloudflare Wrangler `4.168.0` | Exists and was the registry `latest` version at review time; package Node engine is `>=22.12.0`. | Compatible with Node 26 and Worker development/deployment. |
| `@cloudflare/workers-types` `5.20261125.1` | Exists and was the registry `latest` version at review time. | Appropriate type definitions for the Worker APIs and bindings in the design. Cloudflare’s Service Bindings docs confirm Worker-to-Worker calls without public reachability; Hyperdrive docs include Supabase among supported PostgreSQL providers. |
| Supabase-managed PostgreSQL | Platform/product exists; no exact major is specified (finding 2). | The managed PostgreSQL + Data API/RLS design is supported. Major-specific feature compatibility remains unverified until the provisioning choice is made. |

No finding was raised for the Cloudflare Pages static-hosting, Worker Service Binding, Supabase Data API/RLS, or optional Hyperdrive design: current official documentation supports those boundaries.

## Authoritative sources consulted

- [Node.js releases](https://nodejs.org/en/about/previous-releases) and [release index JSON](https://nodejs.org/dist/index.json)
- npm registry metadata: [Next.js](https://registry.npmjs.org/next/16.4.6), [React](https://registry.npmjs.org/react/19.3.1), [React DOM](https://registry.npmjs.org/react-dom/19.3.1), [TypeScript](https://registry.npmjs.org/typescript/7.0.2), [Supabase JS](https://registry.npmjs.org/@supabase/supabase-js/2.119.2), [Supabase CLI](https://registry.npmjs.org/supabase/3.4.0), [Wrangler](https://registry.npmjs.org/wrangler/4.168.0), [Workers types](https://registry.npmjs.org/@cloudflare/workers-types/5.20261125.1)
- [Next.js static exports](https://nextjs.org/docs/app/guides/static-exports), [Next.js TypeScript support](https://nextjs.org/docs/app/api-reference/config/typescript), and [Cloudflare Pages: static Next.js site](https://developers.cloudflare.com/pages/framework-guides/nextjs/deploy-a-static-nextjs-site/)
- [Cloudflare Service Bindings](https://developers.cloudflare.com/workers/runtime-apis/bindings/service-bindings/) and [Hyperdrive](https://developers.cloudflare.com/hyperdrive/)
- [Supabase Data REST API](https://supabase.com/docs/guides/api), [Supabase CLI](https://supabase.com/docs/guides/local-development/cli/getting-started), and [PostgreSQL upgrading](https://supabase.com/docs/guides/platform/upgrading)
