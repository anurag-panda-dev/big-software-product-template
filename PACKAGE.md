# PACKAGE.md — Monorepo conventions

## Layout

Domain-first monorepo: `apps/` (deployables) · `categories/` (products) · `crosscutting/` · `kernel/` · `platform/` · `datasets/` · `content/` · `business/` · `legal/` · `docs/` · `rfcs/` · `labs/` · `sandbox/` · `infra/` · `tests/` · `tooling/`.

Root `README.md` has the rules of thumb; every folder's `purpose.txt` defines what it may contain.

## Import boundaries (normative)

| From | May import | Never |
|---|---|---|
| `apps/*` | `categories/*`, `kernel/*`, `crosscutting/*` | `platform/*` directly |
| `categories/<cat>/<product>` | `kernel/*`, `crosscutting/*`, `categories/<cat>/_category/` | any other category; deep imports of other features |
| feature slices | own `logic/data/ui`, sibling features via public `index.ts` | `apps/*`, `platform/*` |
| `crosscutting/*` | `kernel/*` | `categories/*`, `apps/*` |
| `platform/*` | `kernel/*` | everything above it |
| `logic/` (inside any feature) | framework-free libs | React, HTTP, DB clients |

**Communication across the platform boundary:** `platform/api/openapi` (contract-first, generated from `kernel/contracts`). No product imports a payment gateway — products read `kernel/entitlements`.

## Enforcement

- `kernel/config/eslint` holds the boundary rules (`eslint-plugin-boundaries` or `dependency-cruiser`).
- `pnpm lint` fails the build on a boundary violation — this is intentional.
- `tests/contract` pins API consumers to the OpenAPI spec.

## Directory conventions

- Feature slice: `features/<feature>/{ui,logic,data,api,__tests__}` — created only when the feature is built.
- Product folder: `product.md` (spec pointer), `routes/` (route table), `features/`.
- Data: every `datasets/curated/<x>/` file validates against `datasets/schemas/<x>.schema.ts` in CI.
- Decisions: RFC (`rfcs/`) → ADR (`docs/decisions/` from `TEMPLATE.md`); ADRs are append-only for amendments.

## Tooling

- Package manager: pnpm (workspaces) · Task runner: Turborepo · TS config extended from `tsconfig.base.json`
- Formatting: Prettier config in `kernel/config/prettier`; git hooks via `.husky/`
