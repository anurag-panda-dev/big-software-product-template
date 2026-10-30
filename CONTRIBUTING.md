# Contributing

Thanks for helping build this project.

## Ground rules

1. **Read `PACKAGE.md`** — the import boundaries are enforced by lint; PRs that violate them will fail CI.
2. **Every new folder ships with a `purpose.txt`** (`PURPOSE:` / `SHOULD CONTAIN:` / `SEE:`).
3. **No empty scaffolding** — create folders when the work arrives, not in anticipation.
4. **Architecture changes go through process:** RFC in `rfcs/` → discussion → ADR from `docs/decisions/TEMPLATE.md`.
5. **Data changes need provenance:** any `datasets/curated/` change includes its schema and source/last-verified info.

## Workflow

```bash
pnpm install
pnpm dev          # run locally
pnpm lint         # includes boundary checks
pnpm test
pnpm build
```

## Pull requests

- One concern per PR (structure changes are separate from feature changes)
- Fill in the PR template; link the ADR/RFC if the change alters structure or contracts
- Keep `logic/` framework-free; add tests alongside the feature slice

## Commit style

Conventional commits: `feat:`, `fix:`, `chore:`, `docs:`, `refactor:`, `test:`, `ci:` — with an optional scope, e.g. `feat(web): …`, `chore(datasets): …`.
