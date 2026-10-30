# AGENTS.md — AI agent context for this repository

This repo follows an **ecosystem-first monorepo** structure (see `docs/decisions/` ADRs and root `README.md`).

## Repository map

- `apps/` — thin deployable shells (routes, providers, wiring). No domain logic.
- `categories/<category>/<product>/` — domain verticals; feature slices at `features/<feature>/{ui,logic,data,api,__tests__}`
- `crosscutting/` — shared-layer products; import `kernel/` only
- `kernel/` — design-system, contracts (Zod), auth, offline, i18n, entitlements, telemetry, config
- `platform/` — backend (api, payments, notifications, voice, search, jobs, identity, database, observability); never imported by anything above it
- `datasets/` — raw → curated → schemas → sources → pipelines; every curated file needs a schema
- `content/` — editorial engine (templates, workflow, library, locale)
- `business/`, `legal/`, `docs/` — human/agent documents; never imported by code
- `sandbox/`, `labs/` — throwaway/research; never imported by production code

## Hard rules

1. Respect the import boundaries listed in `PACKAGE.md` — do not create cross-category imports.
2. `logic/` must stay framework-free (no React, HTTP, or DB clients).
3. Never pre-create empty feature folders — scaffold features only when the feature is being built.
4. Every new folder gets a `purpose.txt` (format: `PURPOSE:` / `SHOULD CONTAIN:` / `SEE:`).
5. Architecture changes need an RFC in `rfcs/` first, then an ADR from `docs/decisions/TEMPLATE.md`.
6. Secrets go in `.env` (never committed); `.env.example` documents keys.
7. Zod schemas in `kernel/contracts` are the single source of truth for data shapes.

## Common commands (fill in once the workspace exists)

```bash
pnpm install
pnpm dev            # start dev
pnpm build          # build all
pnpm lint           # lint + boundary checks
pnpm test           # unit tests
```
