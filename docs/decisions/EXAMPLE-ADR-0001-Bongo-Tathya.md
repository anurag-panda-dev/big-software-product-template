> **NOTE:** This is a real ADR from a project built on this template, kept as a formatting/depth example. Project-specific content (category names, company details) is illustrative only. For a blank ADR, use TEMPLATE.md in this folder.

# ADR-0001: Ecosystem-First Monorepo Repository Structure

**Status:** Accepted
**Date:** 2026-10-30
**Amended:** 2026-10-30 — mobile apps (RN/Flutter/Android/iOS), `market-research`, and corporate-ops directories (product, marketing, finance) merged from a company-ops template
**Deciders:** Bongo Tathya founding team
**Supersedes:** —
**Related:** `13-Technical-Architecture.md`, `14-Content-Strategy.md`, `16-Monetization-Strategy.md`, `19-Team-and-Hiring-Plan.md`, `22-Final-Ecosystem.md`, `23-Shared-Layer.md`

---

## Context

Bongo Tathya will grow from today's planning repo (`docs/`, `brand/`, `Teaser/`, `temp/`) into a **29-product ecosystem**: 8 category verticals (27 products) + 2 shared-layer products (Kotha, Disha), spanning web, PWA, voice, and SMS channels.

Forces shaping this decision:

1. **Scale of surface area** — 29 products cannot live as 29 folders in a flat `apps/` directory without structure collapsing into ambiguity (shared code drifts, ownership blurs, cross-imports multiply).
2. **Data is the product** — the ecosystem's value is verified Bengal data (schemes, locations, hospitals, prices, alerts), which needs first-class governance: provenance, schemas, last-verified dates.
3. **Stack uncertainty** — the API layer (Express vs FastAPI) is deliberately undecided; the structure must not bake in a language choice.
4. **Monetization is planned but not designed** — pricing and payments must have a home now so products never couple to gateways directly.
5. **Bangla-first** — i18n is not a feature, it is the substrate: locale, numerals, dialect tolerance, transliteration must be kernel-level, not per-app.
6. **Business operations live in the repo** — content strategy, market analysis, hiring, and legal compliance inform code decisions; keeping them adjacent to code (versioned, reviewable) beats scattered cloud docs.
7. **AI-assisted development** — agents will co-maintain this repo; explicit boundaries and a machine-readable map (`AGENTS.md`) are required.

## Decision

We adopt an **ecosystem-first (vertical product lines) monorepo** built on **pnpm workspaces + Turborepo**, organized by *domain* rather than by *technical kind*:

- `categories/` — the 8 verticals; each product is **feature-sliced** (`features/<feature>/{ui,logic,data,api,__tests__}`)
- `crosscutting/` — Kotha & Disha as architectural peers of categories, not buried packages
- `kernel/` — the reusable core: design system, contracts, auth, offline, **i18n**, telemetry, **entitlements**, config
- `platform/` — backend concerns products never import: **api (stack-neutral)**, payments, notifications, voice, search, jobs, identity, database, observability
- `apps/` — thin deployable shells: **web**, **mobile (React Native ⇄ Flutter, native android/ + ios/ hosts)**, admin, marketing — routes + providers + wiring only
- `datasets/` — data as a product (raw → curated → schemas → sources → verification ledger)
- `content/` — editorial engine per doc 14 (style guide, templates, workflow, locale)
- `business/` — product (strategy/roadmaps), **market-research**, marketing, pricing, monetization, finance, metrics, team, support
- `legal/` — cross-cutting compliance (spans code + business)
- `docs/` + `rfcs/` + `labs/` + `sandbox/` + `infra/` + `security/` + `tests/` + `tooling/`

### Full structure

```text
bongo-tathya/
│
├── README.md                        # Overview, quick start, links to docs
├── AGENTS.md                        # AI-agent context: repo map, conventions, commands
├── LICENSE
├── CONTRIBUTING.md
├── CHANGELOG.md
├── PACKAGE.md                       # Monorepo conventions: import rules, boundaries
│
├── package.json                     # Root workspace (private: true)
├── pnpm-workspace.yaml
├── turbo.json                       # Task pipeline: lint → typecheck → test → build
├── tsconfig.base.json
├── .editorconfig
├── .nvmrc
├── .gitignore
├── .env.example                     # Documented env vars (no secrets)
├── .prettierrc
├── .husky/                          # Commitlint, lint-staged
│
├── .github/
│   ├── workflows/                   # ci.yml, deploy.yml, data-validation.yml
│   ├── ISSUE_TEMPLATE/
│   ├── PULL_REQUEST_TEMPLATE.md
│   └── CODEOWNERS                   # categories/* → owning team
│
├── rfcs/                            # Pending proposals (0001: express vs fastapi)
│
├── ──----------------------------- CODE ──---------------------------------
├── apps/                            # Deployable shells (thin)
│   ├── web/                         # Flagship Next.js app (bongotathya.com)
│   │   ├── src/app/                 #   App Router + API routes
│   │   ├── src/providers/           #   Auth, i18n, query, offline providers
│   │   └── next.config.ts
│   ├── admin/                       # Moderation/CMS dashboard
│   ├── pwa/                         # (optional later) offline-first variant
│   ├── marketing/                   # Landing/SEO pages (absorbs today's Teaser)
│   └── mobile/                      # Native apps — STACK UNDECIDED (React Native ⇄ Flutter)
│       ├── DECISION.md              #   RFC link — same stack-neutral pattern as platform/api
│       ├── shared/                  #   Screens, navigation map, feature specs, shared assets
│       ├── react-native/            #   RN app — owns its android/ + ios/ host projects
│       ├── flutter/                 #   Flutter app — builds android/ + ios/ via tooling
│       ├── android/                 #   Standalone native shell (Kotlin), only if ever needed
│       └── ios/                     #   Standalone native shell (Swift), only if ever needed
│
├── categories/                      # The 8 verticals — feature-sliced products
│   ├── bhromon/                     # ভ্রমণ: jela-darshan, shonar-tori, ranna-ghor
│   │   ├── _category/               # Shared-within-category: types, fixtures, seeds
│   │   └── jela-darshan/
│   │       ├── product.md           # Spec pointer → docs/
│   │       ├── routes/              # Page definitions (wired by apps/web)
│   │       └── features/            # Feature-Sliced Design
│   │           ├── browse-districts/
│   │           │   ├── ui/          # Components for this feature
│   │           │   ├── logic/       # Pure domain logic (framework-free)
│   │           │   ├── data/        # Queries/adapters
│   │           │   ├── api/         # Endpoint contracts
│   │           │   └── __tests__/
│   │           ├── place-detail/
│   │           └── save-itinerary/
│   ├── adhikar/                     # scheme-sathi, adhikar, daftar
│   ├── shiksha/                     # bidyaloy, chakri, britti
│   ├── shastho/                     # chikitsa, toxin, rokto
│   ├── krishi/                      # krishak, bagan
│   ├── dainik-jibon/                # bari, yatri, hisheb, dorkar, dorjar, bazar, nagar, gram
│   ├── sanskriti/                   # itihas, utsab
│   └── suraksha/                    # jol, brishti, bipod
│
├── crosscutting/                    # Shared layer (products 28 & 29)
│   ├── kotha/
│   │   ├── intent/                  # NLU/intent classification
│   │   ├── router/                  # Intent → product/view routing
│   │   ├── answers/                 # Grounded answer engine + sources
│   │   ├── widget/                  # Embeddable component (all 29 products)
│   │   └── channels/                # SMS fallback, offline cache
│   └── disha/
│       ├── engine/                  # "What should I do next" logic
│       └── sdk/                     # Injectable guidance components
│
├── kernel/                          # Reusable core — "what every product IS"
│   ├── design-system/
│   │   ├── tokens/                  # Generated from brand/
│   │   └── components/
│   ├── contracts/                   # Zod schemas: content, location, scheme, review...
│   ├── auth/                        # Anonymous + registered session logic
│   ├── offline/                     # Service-worker strategies, sync queue
│   ├── i18n/                        # Bangla-first i18n kernel
│   │   ├── locales/                 #   bn (primary), en — message catalogs
│   │   ├── formatting/              #   Bengali numerals, ₹, dates (১৫ জানুয়ারি ২০২৫)
│   │   ├── transliteration/         #   Bangla↔Roman search tolerance
│   │   └── dialects/                #   Rural/urban/code-mixed hints (feeds Kotha)
│   ├── entitlements/                # Single source: "what may this user access"
│   │   └── rules/                   #   Free vs paid, quotas, category grants
│   ├── telemetry/                   # Privacy-first analytics, Sentry glue
│   ├── hooks/                       # Shared React hooks
│   ├── utils/
│   └── config/
│       ├── eslint/  typescript/  tailwind/  prettier/
│       └── flags/                   # Feature flags (env-driven)
│
├── platform/                        # Backend — products never import directly
│   ├── api/                         # REST API — STACK UNDECIDED (Express ⇄ FastAPI)
│   │   ├── DECISION.md              #   ADR link → rfcs/0001
│   │   ├── openapi/                 #   Contract-first spec (from kernel/contracts)
│   │   ├── src/                     #   Express: src/… · FastAPI: app/… (rename on decision)
│   │   ├── tests/
│   │   └── package.json             #   or pyproject.toml if Python
│   ├── payments/                    # Gateway-agnostic money layer
│   │   ├── gateways/                #   One adapter per gateway (Razorpay, UPI…)
│   │   ├── webhooks/                #   Signature verify, retries, idempotency
│   │   ├── ledger/                  #   Transactions, refunds, reconciliation
│   │   └── sandbox/                 #   Test-mode harness (no real money)
│   ├── notifications/               # Multi-channel delivery
│   │   ├── channels/                #   SMS (MSG91), WhatsApp, email, push, IVR
│   │   ├── templates/               #   Bangla-first message templates
│   │   └── preferences/             #   Per-user opt-in/out, DND compliance
│   ├── voice/                       # Kotha infra: STT (Whisper) → TTS, telephony
│   ├── search/                      # Elasticsearch/Meilisearch indexing
│   ├── jobs/                        # Workers: sync, notifications, scrapers, verification
│   ├── identity/                    # Users, sessions, permissions
│   ├── database/
│   │   ├── prisma/                  # schema.prisma + migrations
│   │   ├── supabase/                # SQL migrations, RLS policies, edge functions
│   │   └── seed/                    # districts, blocks, villages
│   └── observability/               # Dashboards, alerts, SLOs
│
├── tests/                           # Cross-app quality gates
│   ├── e2e/                         # Playwright journeys spanning categories
│   ├── contract/                    # OpenAPI consumer-driven contract tests
│   └── visual/                      # Screenshot baselines (brand fidelity)
│
├── ──-------------------- DATA & CONTENT ──-----------------------------------
├── datasets/                        # Data as a product
│   ├── raw/                         # Ingested sources (gitignored / Git LFS)
│   ├── curated/
│   │   ├── locations/               # districts → blocks → villages + geo
│   │   ├── schemes/  hospitals/  prices/  transport/  alerts/
│   │   └── content/
│   ├── schemas/                     # Validation per dataset — run in CI
│   ├── sources/                     # One folder per official source (license, rate limit)
│   ├── pipelines/                   # ETL scripts (wb-government-schemes/, imd-weather/…)
│   ├── VERIFICATION.md              # Last-verified dates, provenance, 2-source rule
│   └── README.md
│
├── content/                         # Editorial engine (per docs/14)
│   ├── style-guide.md               # "Satyo, Shohoj, Shoktiśālī" + language standards
│   ├── templates/                   # guide, directory, comparison, explainer, faq
│   ├── workflow/                    # research → write → review → publish → monitor
│   │   └── checklists/              # fact-check, legal, sensitivity, SEO, a11y
│   ├── library/
│   │   ├── guides/  directories/  comparisons/  explainers/  faqs/
│   │   └── real-time/               # alerts, prices, weather advisories (curated)
│   ├── locale/
│   │   ├── bn/                      # Primary — Bangla-first
│   │   └── en/
│   ├── seo/                         # Meta, structured data, sitemap sources
│   └── moderation/                  # UGC policy + queue configs (reviews, photos, Q&A)
│
├── ──--------------------- BUSINESS & OPS ──-------------------------
├── business/
│   ├── product/                     # Product strategy & lifecycle hub
│   │   ├── strategy-roadmaps/       #   North Star, OKRs, multi-quarter initiatives
│   │   │                           #   (2027-product-vision.md, q1-roadmap.md…)
│   │   └── launch-playbooks/        #   Release readiness steps + rollout plans
│   ├── market-research/             # Market research & insight
│   │   ├── competitive/             #   Competitor profiles + battlecards (docs/17)
│   │   ├── users/                   #   Personas, interviews, usability studies
│   │   ├── industry/                #   TAM/SAM/SOM, surveys, Bengal demographics
│   │   └── data/                    #   Product analytics reports, cohort insights
│   ├── marketing/                   # Brand & growth machinery
│   │   ├── content-strategy/        #   Editorial calendars, messaging maps, SEO guides
│   │   │                           #   (templates/workflow stay in content/)
│   │   ├── campaigns/               #   Briefs, ad copy, tracking codes, results
│   │   │   └── <launch-name>/       #   One folder per campaign
│   │   └── public-relations/        #   Media kits, press drafts, crisis response trees
│   ├── pricing/                     # Pricing & packaging (strategy + tier matrices)
│   │   ├── strategy.md              #   Free floor, tiers, willingness-to-pay
│   │   ├── tiers/                   #   Per-product / per-category tier definitions
│   │   └── experiments/             #   Price tests + results
│   ├── monetization/                # Ads, partnerships, premium revenue tracking
│   ├── finance/                     # Budget & procurement (non-sensitive only)
│   │   ├── budget-proposals/        #   Departmental financial request logs
│   │   └── vendor-management/       #   SaaS inventory, renewals, vendor security reviews
│   ├── metrics/                     # KPIs & OKRs (docs/21), review cadence
│   ├── team/                        # Docs/19: org, hiring, salary bands, onboarding
│   │   ├── org/
│   │   ├── hiring/                  # Job descriptions, interview kits
│   │   └── runbooks/                # Role-specific onboarding checklists
│   └── support/                     # Help center, ticket macros, escalation paths
│
├── legal/                           # Cross-cutting compliance (spans code + business)
│   ├── nda.md                       # Pointer → company-ops/legal/contract-templates (when created)
│   ├── compliance/                  # DPDP Act, accessibility, content liability
│   ├── licenses/                    # Dataset + font + asset licenses
│   ├── privacy/                     # Privacy policy drafts, data-retention matrix
│   └── templates/                   # Contributor agreement, partnership terms
│
├── ──-------------------- ASSETS, DOCS, MISC ──-------------------
├── brand/                           # Existing: svg/, png/, fonts/, scripts/, gallery.html
│
├── docs/
│   ├── README.md                    # Index / table of contents
│   ├── architecture/                # 13, 22, 23
│   ├── product/                     # Category deep-dives 05–12
│   ├── strategy/                    # 01–04, 15–18, 20–21
│   ├── decisions/                   # Landed ADRs (this file) — vs rfcs/ = pending
│   └── legal/                       # Or migrated → legal/
│
├── labs/                            # Research: Bangla NLP, dialect models, voice tests
│   ├── nlp/  voice/  offline/
│   └── NOTES.md
│
├── sandbox/                         # Prototypes & experiments (retires temp/)
│   ├── teaser/                      # Current Teaser/ (Astro)
│   └── playground/
│
├── infra/                           # IaC as you scale
│   ├── vercel/  cloudflare/
│   ├── docker/                      # Dockerfiles for api, voice, jobs (if needed)
│   └── monitoring/                  # Sentry config, dashboards
│
├── security/                        # Threat model, pen-test reports
│   └── THREAT-MODEL.md
│
├── tooling/
│   ├── scripts/                     # Repo-wide scripts (retires temp/*.mjs)
│   └── codegen/                     # Token generation, API client gen from openapi
│
└── .ai/                             # Agent/tooling context companion to AGENTS.md
    ├── conventions.md               # Import-boundary rules agents must follow
    └── maps/                        # Generated repo maps
```

### Boundary rules (enforced by lint — `eslint-plugin-boundaries` / `dependency-cruiser`)

1. `apps/*` → may import `categories/*`, `kernel/*`, `crosscutting/*`
2. `categories/*` → may import `kernel/*`, `crosscutting/*`, own `_category/` — **never another category**
3. Features within a product → may import sibling features' `logic` only via public `index.ts`; never deep-import
4. `crosscutting/*` → may import `kernel/*` only
5. `platform/*` → never imported by anything above it — communicates via `openapi/` + `kernel/contracts`
6. Products never touch payment gateways — they read `kernel/entitlements`; `platform/payments` settles money
7. `business/`, `legal/`, `docs/` → referenced by humans/agents, never imported by code
8. `logic/` never imports React, HTTP, or database clients (portable to web, voice, SMS)

### Money & access flow

`business/pricing/` (strategy) → `kernel/entitlements/rules/` (rules in code) → `platform/payments/` (settlement) → products read entitlements through `kernel/` only.

## Consequences

### Positive
- The repo "screams the domain": opening `categories/shastho/` shows the entire health vertical.
- Adding product #30 = one folder + one contract row; nothing else to wire.
- Category boundaries map to team ownership (`CODEOWNERS`) and independent future extraction (Phase 4 microservices) without restructuring.
- Data, content, business, and legal decisions are versioned and reviewable alongside code.
- API stack can still be chosen (Express *or* FastAPI) with zero consumer impact — contract-first via `kernel/contracts` → `platform/api/openapi`.
- Pricing/payments/i18n are kernel/platform concerns from day one, avoiding the usual retrofit.

### Negative & mitigations
| Cost | Mitigation |
|---|---|
| Fewer off-the-shelf tooling examples for this shape | Turborepo/pnpm use globs, not assumptions; document commands in `AGENTS.md` |
| Devs habituated to `src/components` need adjustment | `PACKAGE.md` + `.ai/conventions.md` + boundary lint errors that teach |
| Two planes (`apps/` and `categories/`) can confuse contributors | Rule of thumb in README: *deployable → `apps/`, domain → `categories/`, reusable → `kernel/`* |
| Polyglot risk if FastAPI chosen | `uv` + `ruff` + `mypy` tasks registered in `turbo.json`; Node and Python coexist |

### Neutral
- `Teaser/` → `sandbox/teaser/` (or `apps/marketing/` if it graduates); `temp/` → `tooling/scripts/` or gitignored; PDF → `docs/`.

## Alternatives considered

1. **Standard `apps/` + `packages/` horizontal monorepo** (most common OSS layout) — rejected: at 29 products, flat `apps/` becomes unowned soup; shared code drifts into grab-bag packages. Retained as fallback if team composition changes.
2. **Single Next.js app, folder-per-route** — rejected: no boundary enforcement, no independent extraction path, voice/SMS channels can't reuse `logic/`.
3. **Multi-repo per product** — rejected: 29 repos = 29 CI setups, fragmented brand/data contracts, impossible shared kernel.
4. **Layer-first (hexagonal: domain/application/infrastructure at root)** — rejected: correct discipline but hides the domain; the feature-sliced `categories/*/features/` pattern delivers the same isolation with clearer naming.

## Companion repository (adopted from the company-ops template)

Sensitive corporate operations are **excluded from this product monorepo** and belong in a separate **private `company-ops` repository** with stricter `CODEOWNERS` (Legal approves `/legal`, Founders approve `/finance-and-procurement`):

```text
company-ops/                         # PRIVATE — not this repo
├── .github/workflows/
│   ├── compliance-check.yml         # Expired contracts / unsigned NDAs scan
│   └── employee-onboarding.yml      # Identity provisioning trigger
├── legal/
│   ├── corporate-formation/         # Incorporation, bylaws, board resolutions
│   ├── intellectual-property/       # Trademarks, patents, brand protection filings
│   ├── compliance-privacy/          # DPDP/GDPR policy masters, cookie disclosures
│   └── contract-templates/          # NDA, contractor agreement, vendor DPA (read-only)
├── finance-and-procurement/
│   ├── budget-proposals/
│   ├── equity-cap-table/            # NEVER in the product repo
│   └── vendor-management/
├── hr-and-people/
│   ├── handbook/                    # Culture, compensation, time-off policies
│   ├── templates/                   # JDs, review frameworks, offer letters
│   └── workflows/                   # Onboarding / offboarding checklists
└── README.md
```

**Rule of thumb:** *code-adjacent* compliance (dataset licenses, privacy drafts, DPDP implementation) stays in this repo's `legal/`; *corporate* documents (equity, HR, signed contracts, IP filings) never enter it.

## Compliance

- New structure directories may only be created as products/data/business actually arrive — **empty scaffolding is forbidden**; this ADR defines *where* things go, not *when*.
- Deviations require a new ADR in `docs/decisions/` (for landed changes) or an RFC in `rfcs/` (for pending proposals).
- Review cadence: revisit at Phase 2 (admin + database adoption) and Phase 4 (microservices extraction).
