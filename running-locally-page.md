# Operations > Running Locally Page Template

The fuller version of "how to run this locally". Getting Started covers the minimal happy path; this page covers everything else: alternate modes, common issues, supporting services.

## Properties

- **Name**: `Running Locally`
- **Owner**: User
- **Verification**: Empty (draft)
- **Tags**: `Technical`, `How To`

## Icon and cover

- **Icon**: 💻
- **Cover**: dev-environment-themed gallery image

## Body structure

### 1. Definition callout

```
ℹ️ Detailed local-dev guidance for {product}. For the minimal first-time setup, see Getting Started.
```

### 2. Standard local-dev flow

A condensed reference of the dev commands. Pulled from package.json scripts, Makefile targets, etc.

Example:
- `pnpm dev` — start all services in dev mode
- `pnpm dev:web` — start frontend only
- `pnpm dev:api` — start backend only
- `pnpm db:migrate` — run pending migrations
- `pnpm db:reset` — drop and recreate local DB
- `pnpm test` — run unit tests
- `pnpm test:e2e` — run E2E tests (requires running dev server)

### 3. Supporting services

If the product depends on local services (Postgres, Redis, Supabase local, etc.), how to set those up:

- **Postgres**: via Docker Compose — `docker compose up postgres` from repo root
- **Supabase local**: `supabase start` (requires Supabase CLI)
- **Redis**: optional, only needed for {feature X}; `docker compose up redis`

Include the docker-compose file location if applicable.

### 4. Seed data (if applicable)

If there's seed data: how to load it, what it contains, how to reset.

### 5. Common issues

Numbered list of common problems and fixes. This is gold for new contributors.

Example:
1. **Port 5173 already in use** → Another Vite instance is running. Kill it with `lsof -ti:5173 | xargs kill -9`.
2. **`pnpm db:migrate` fails with connection error** → Check `DATABASE_URL` in `.env` and ensure Postgres is running.
3. **Anthropic API calls fail with 401** → `ANTHROPIC_API_KEY` is missing or invalid.

### 6. Working with external services in dev

If the product hits real third-party services in dev (Anthropic, Sendgrid, etc.):
- Which ones use a sandbox / test mode
- Which ones bill against real accounts
- Which ones have a local mock available

Example:
- **Anthropic**: real API in dev (no sandbox). Costs are minimal but non-zero. Use the cheapest model in dev where possible.
- **Sendgrid**: dev mode uses Sendgrid's sandbox flag — emails are accepted but not delivered.
- **Stripe**: test mode keys, no real charges.

## What NOT to include

- The minimal first-run instructions (those live on Getting Started)
- Deployment instructions (live on Deployment page)
- Production env var values

## Source notes

- Commands largely code-verified (from scripts, Makefile)
- Common issues mostly user-stated based on actual experience
- Service modes user-stated, partly verifiable from env var conventions (e.g. `STRIPE_SECRET_KEY` starting with `sk_test_`)
