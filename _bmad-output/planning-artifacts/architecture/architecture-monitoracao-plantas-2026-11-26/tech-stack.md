# Technology Stack and Runtime

## Web client

- Use the `create-next-app` starter with App Router, strict TypeScript, and npm workspaces.
- Build Next.js with `output: "export"` and deploy the generated static assets to Cloudflare Pages Free. The client is mobile-first and responsive; use the tokens and components in `DESIGN.md`.
- Use CSS Modules and design tokens, not a component system that conflicts with the UX contract.
- Static export has no Next.js runtime, Route Handlers, Server Actions, or dynamic server rendering. All runtime API work belongs to `api-worker`.
- Do not add a service worker, install prompt, background sync, or offline write queue. This is not an installable PWA.

## Accessibility and browser support

- Implement the core Inventory, alert, and task-completion flows to WCAG 2.2 AA, following `DESIGN.md`: keyboard operation, visible focus, semantic labels, required contrast, and no color-only status or urgency.
- Support the current and previous major versions of Chrome, Safari, Firefox, and Edge on desktop and mobile browsers.
- Automated accessibility checks complement, but do not replace, manual keyboard and screen-reader review of the core flows.

## Normative UI contracts

The UX documents are the implementation contract for layout, visual tokens, component behavior, interaction states, and copy. Do not invent replacements when a screen is not shown in a mock:

- Follow the Botanical Signal / Dossel tranquilo semantic palette, the exact approved contrast pairs, responsive typography/spacing/elevation/shapes, and the rule that every risk/fit state uses text and an accessible icon rather than color alone. `DESIGN.md` is normative.
- `EXPERIENCE.md` is normative for the four labeled destinations (`Encontrar planta`, `Agenda`, `Minhas plantas`, `Perfil`), equivalent desktop navigation, mobile-first reflow without horizontal scrolling, and converting dense task tables into labeled lists.
- Search implements loading, empty, retryable error, and cached-catalog fallback states. Forms retain entered values on validation failure, identify the field, and move focus to the first error. Permission denial offers manual/nonessential fallbacks.
- Dialogs/sheets preserve one active layer, focus within the layer, move initial focus to its purpose/first required field, provide cancel where allowed, and restore focus to the trigger on close. Confirmation states explain consequence and reversibility; interactive targets meet the UX 44px minimum.
- Critical exposure guidance remains isolated from routine Agenda flows. Before confirming a Care Batch, show all included Plants/tasks and each exclusion with its reason. Optional photos remain records, not diagnosis inputs.

## API and data access

- `api-worker` is the public TypeScript API and decision boundary. It receives user JWTs, forwards them to Supabase Data API/RPC, and relies on RLS for private rows. It contains no `service_role`, provider credential, or R2 administrative binding.
- Supabase Auth issues account sessions. The Supabase publishable key is intentionally public; it is not an authorization boundary. Protect user bearer tokens from logging and XSS.
- The API Worker runs the shared `packages/domain` rules for fit, agenda, diagnosis, and safety responses. Guest requests may submit temporary routine/location context; do not persist or log it.
- Use PostgREST/Data API and narrowly exposed RPCs over HTTPS. This avoids opening PostgreSQL sockets from browser or serverless request handlers.
- If an implementation later requires PostgreSQL wire protocol from a Worker, use Cloudflare Hyperdrive or Supavisor transaction mode. Do not open one direct database connection per request.

## Local read cache

- Persist only synchronized Inventory and Agenda snapshots in IndexedDB, partitioned by authenticated user ID.
- On page load, render the last snapshot immediately, show its `last_sync_at`, then revalidate online and replace it only after a successful response.
- When a snapshot is older than 30 days, show `obsoleto` and historical context only. Disable actions and current-state language until a fresh server response is available.
- Offline mode is read-only. Never enqueue local task, care-event, or profile writes for later synchronization.
- Clear user-scoped cache on sign-out, account switch, and confirmed deletion. Guest fit inputs remain transient.

## Worker split

| Deployment | Public reachability | Credentials and duties |
| --- | --- | --- |
| `api-worker` | Public `fetch` routes | Supabase publishable key; forwards authenticated user JWTs; public search/fit and user-scoped API; no service role or R2 binding |
| `cron-worker` | `scheduled()` plus private Service Binding only | Sole `SUPABASE_SERVICE_ROLE_KEY` and R2 binding; scheduled ingestion/weather, deletion queue, orphan sweep, and constrained media operations |

The two Workers share contracts and domain types from the monorepo. The API Worker can request narrowly typed media operations over Cloudflare Service Binding; the Cron Worker validates each operation against a pending owner-scoped database record before touching R2.

## Performance budgets

- Inventory and Agenda must render usable content within 2 seconds at p75 under a documented normal-network profile.
- Schedule recalculation for an inventory of up to 200 Plants must complete within 3 seconds at p95.
- Fetch owner data in bounded batch queries and avoid per-Plant network round trips. Benchmark with representative 200-Plant data in CI and the release environment; missed budgets block release until optimized or an approved, measured runtime change is made.

## External cache and media

- Cloudflare Cache API may cache only public catalog search candidates (normalized query, published catalog revision, names/IDs). Use a short bounded TTL and versioned invalidation.
- Never edge-cache care-fit results, private plants, safety context, auth responses, or user-specific Agenda data.
- Store species images and private user photos in R2. Private user images are served through authorized Worker operations; do not expose a public bucket URL.
- Resize and encode user photos in the browser to WebP at no more than 1200 px before upload. This is a bandwidth optimization, not a trust boundary.
- User photo limits: WebP, max 1200 px, 2 MiB per file, 5 per plant, 50 per account, and 10 upload attempts per account per hour. Delete or replace is available at the cap.

## Development and deployment

- Use Supabase CLI local services for schema/Auth integration tests and `wrangler dev` for Worker handlers. Keep migrations and Worker configuration in version control; secrets stay in local ignored files or provider secret stores.
- Deploy static web, API Worker, and Cron Worker independently from the same repository. The Cron Worker has no public route.
- Supabase Free is for development/staging only. Before public production, use Supabase Pro in `sa-east-1` (São Paulo), configure custom SMTP, and verify backups/restore. Do not treat a daily Cron request as an availability guarantee.

## Verified versions and cost limits

| Component | Pin or current limit |
| --- | --- |
| Node.js / npm | 26.13.1 LTS / 11.20.0 (npm bundled with this Node release) |
| Next.js / React / TypeScript | 16.4.6 / 19.3.1 / 7.0.2 |
| Supabase JS / CLI | 2.119.2 / 3.4.0 |
| Wrangler / Workers types | 4.168.0 / 5.20261125.1 |
| Supabase Free | 500 MB database, 5 GB egress; projects may be paused after 7 days of low activity; no downloadable backups |
| Supabase Pro | $25/month starting price for one project on Micro compute, as checked 2026-11-26; daily backups retained 7 days; overages/add-ons can cost more |
| Cloudflare Workers Free | 100,000 requests/day, 10 ms CPU per HTTP or Cron invocation, 128 MB memory; break CPU-heavy work into small batches |
| Cloudflare Workers Paid | $5/month minimum starting price; consider only when measured CPU, request, or runtime limits require it |
| Cloudflare R2 Standard free allowance | 10 GB-month, 1M Class A and 10M Class B operations/month; no egress fee; storage and operations beyond the allowance are billed |

The free tiers are bounded, not a production availability promise. Catalog enrichment stops at its provider budget; user care remains local and available if an integration is paused.

## Current references

- [Next.js installation and starter](https://nextjs.org/docs/app/getting-started/installation)
- [Next.js static exports and unsupported server features](https://nextjs.org/docs/app/guides/static-exports)
- [Next.js TypeScript support](https://nextjs.org/docs/app/api-reference/config/typescript)
- [Supabase Next.js quickstart](https://supabase.com/docs/guides/getting-started/quickstarts/nextjs)
- [Supabase database connection selection](https://supabase.com/docs/guides/database/connecting-to-postgres)
- [Supabase regions](https://supabase.com/docs/guides/platform/regions)
- [Supabase pricing](https://supabase.com/pricing)
- [Supabase production checklist](https://supabase.com/docs/guides/deployment/going-into-prod)
- [Cloudflare Workers limits](https://developers.cloudflare.com/workers/platform/limits/)
- [Cloudflare Workers pricing](https://developers.cloudflare.com/workers/platform/pricing/)
- [Cloudflare Service Bindings](https://developers.cloudflare.com/workers/runtime-apis/bindings/service-bindings/)
- [Cloudflare R2 pricing](https://developers.cloudflare.com/r2/pricing/)

Versions and quotas were checked on 2026-11-26. Recheck before provisioning; provider pricing and plan limits can change.
