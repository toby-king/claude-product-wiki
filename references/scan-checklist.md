# Codebase Scan Checklist

Detailed checklist for the codebase scanning step. Work through these categories in order; skip items that don't apply.

## Language and framework detection

- List all language file extensions present with rough counts
- Identify the primary language (by LOC or file count)
- Detect the framework from dependencies + file patterns:
  - React: `react` in deps + `.jsx/.tsx` files
  - Next.js: `next` in deps + `pages/` or `app/` directory
  - Vue: `vue` in deps + `.vue` files
  - Express: `express` in deps + typically `server.ts` / `app.ts`
  - FastAPI: `fastapi` in requirements + `main.py` with `app = FastAPI()`
  - Django: `django` in requirements + `manage.py`
  - Flask: `flask` in requirements + `app = Flask(__name__)`
  - SvelteKit: `@sveltejs/kit` in deps
  - Astro: `astro` in deps
  - Remix: `@remix-run/*` in deps
- Detect build tools: Vite, Webpack, esbuild, Rollup, Turbopack, tsup
- Detect package manager: npm/yarn/pnpm/bun from lockfile
- Detect monorepo tooling: Turborepo, Nx, Lerna, pnpm workspaces

## Manifest files to read

Read fully, not just a sample:

- `package.json` (and every nested one in a monorepo)
- `requirements.txt`, `pyproject.toml`, `Pipfile`, `poetry.lock`
- `Gemfile`, `Gemfile.lock`
- `go.mod`, `go.sum`
- `Cargo.toml`, `Cargo.lock`
- `composer.json` (PHP)

Extract: dependencies (runtime vs dev), scripts, declared engine versions, workspaces.

## External service detection

Search for SDK imports and API calls. Common patterns:

| Service | Detection signal |
|---|---|
| Stripe | `stripe` package, `Stripe(` constructor |
| Supabase | `@supabase/supabase-js`, `createClient` |
| Pinecone | `@pinecone-database/pinecone` |
| Anthropic | `@anthropic-ai/sdk`, `anthropic.messages.create` |
| OpenAI | `openai` package |
| Sendgrid | `@sendgrid/mail` |
| Twilio | `twilio` package |
| ElevenLabs | `elevenlabs` package or `api.elevenlabs.io` calls |
| Auth0 | `@auth0/*` packages |
| Clerk | `@clerk/*` packages |
| AWS | `@aws-sdk/*`, `aws-sdk`, `boto3` |
| Azure | `@azure/*` packages |
| GCP | `@google-cloud/*` packages |
| Firebase | `firebase`, `firebase-admin` |
| Redis | `redis`, `ioredis` |
| Postgres | `pg`, `postgres` |
| Prisma | `@prisma/client` + `schema.prisma` file |
| Apollo | `@apollo/*` or direct API calls |
| Smartlead | direct API calls to smartlead.ai |
| Companies House | direct API calls to api.company-information.service.gov.uk |
| Zoho | direct API calls to zoho.com / zohoapis.com |
| Indeed | direct API calls or Playwright targeting indeed.com |
| Geckoboard | direct API calls to push.geckoboard.com |

For each integration found:
- File(s) referencing it
- Env vars it depends on
- Whether called from backend, frontend, or both

## Environment variable inventory

Grep patterns:
- JS/TS: `process.env.`, `import.meta.env.`, `Deno.env.get(`
- Python: `os.environ`, `os.getenv(`
- Go: `os.Getenv(`
- Ruby: `ENV[`
- Rust: `env::var(`

Also check:
- `.env.example` or `.env.sample` files
- Config files referencing env vars (`next.config.js`, `vite.config.ts`)
- Dockerfile `ENV` lines and `docker-compose.yml` environment sections
- Deployment platform config

Produce a single table: env var name | file references | category (secret / config / feature flag) | required (best guess from how it's used).

## Database and data model

- Look for `prisma/schema.prisma`
- Look for `supabase/migrations/*.sql`
- Look for Django `models.py` files
- Look for SQLAlchemy / TypeORM / Drizzle model definitions
- Look for plain `.sql` migration files
- Note the database flavour from ORM or connection string

Extract: table names, key columns, relationships, enums, RLS policies, triggers.

## Routing and endpoints

For web backends, enumerate every route:
- HTTP method
- Path
- Handler file
- Auth required (look for middleware)

Frameworks:
- Express: `app.get/post/...` and `router.get/post/...`
- Next.js app router: `app/**/route.ts` files
- Next.js pages router: `pages/api/**`
- FastAPI: `@app.get`, `@app.post` decorators
- Flask: `@app.route` decorators

## Workflow candidate detection

Run this after extracting routes, scripts, and jobs. Produces the **Workflow Candidates** table that anchors the workflow interview. Never invent names or purposes — derive only from code signals.

### Step 1 — Group API routes by domain prefix

Group routes by their URL namespace. Each namespace is a candidate workflow area:

- `/api/jobs/*` (8 routes) → "Job management" candidate
- `/api/applicants/*`, `/api/pipeline/*` → "Applicant pipeline" candidate
- `/api/interviews/*` → "Interview flow" candidate
- Routes that don't fit a clear group → flag as "Unclassified routes" for user to place

Use handler file names as a secondary grouping signal where URL prefixes are ambiguous.

### Step 2 — Identify non-route entry points

Each of these is a potential workflow trigger that wouldn't appear in the route list:

| Signal | What to look for |
|---|---|
| Webhook handlers | Routes named `webhook`, `callback`, `notify`, or paths like `/zoho/webhook`, `/stripe/webhook` |
| Scheduled jobs / cron | `cron(`, `schedule(`, Bull/BullMQ queue definitions, `setInterval` on long intervals, GitHub Actions `schedule:` triggers, Railway/Render cron config, `node-cron` |
| CLI commands | All scripts in `package.json`, `Makefile`, `justfile`, `bin/`, `scripts/` — flag user-initiated vs. internal tooling based on name |
| Background job processors | Files named `*worker*`, `*processor*`, `*consumer*`, `*job*`; queue listener registrations |

### Step 3 — Produce the Workflow Candidates table

Append this to the scan report shown at Pause 1:

```
## Workflow candidates (from code)

| # | Candidate name (inferred) | Signal | Entry points |
|---|--------------------------|--------|--------------|
| 1 | Job management | Routes: GET/POST/PATCH /api/jobs/* (8 routes) | jobs.ts |
| 2 | Applicant pipeline | Routes: /api/applicants/*, /api/pipeline/* | applicants.ts, pipeline.ts |
| 3 | Video interview flow | Routes: /api/interviews/* + webhook handler | interviews.ts, zoho-webhook.ts |
| 4 | Zoho sync | Cron job, runs every 15 min | zoho-sync.ts |
| 5 | Indeed publishing | Script: scripts/publish-indeed.ts | publish-indeed.ts |

⚠️ These are inferred groupings from route prefixes, file names, and job definitions.
The code shows what exists — not what it means. Confirm, rename, and complete this
list in the interview before any workflow pages are created.
```

Rules:
- Candidate names must come from route prefixes, file names, or script names — never invented
- Show the code signal (route count, file name, job trigger) so the user can verify the inference
- A product with no routes, scripts, or jobs gets no candidates table — note this explicitly
- Ungroupable routes appear as a row: "Unclassified routes — {list}" for the user to assign

## Frontend routing (if applicable)

- Route definitions (React Router, Next.js, SvelteKit)
- Layout files
- Auth-protected routes

## Entry points and scripts

- Main server entry (`index.ts`, `server.ts`, `app.py`, `main.go`)
- CLI entry (if applicable)
- All scripts in `package.json` with one-line description
- `Makefile` targets
- `justfile` recipes
- Shell scripts in `scripts/` or `bin/`

## CI/CD and deployment

- `.github/workflows/*.yml` — list each workflow with trigger + purpose
- `Dockerfile` — base image, exposed ports, entrypoint
- `docker-compose.yml` — services and relationships
- `railway.json` / `railway.toml`
- `vercel.json`
- `netlify.toml`
- `fly.toml`
- `render.yaml`
- `Procfile`

## Testing

- Test framework from dev deps
- Test file locations
- E2E framework (Playwright, Cypress) if present
- Rough test count

## Documentation that exists

- `README.md` — read in full, will likely seed the Overview page
- `CONTRIBUTING.md`
- `ARCHITECTURE.md`
- `docs/` directory
- ADRs in `docs/adr/`
- Inline comments that look like documentation

## Project structure

Top-level tree, one level deep, with a one-line purpose per directory where confidently inferable. Mark inferences as inferred.

## Likely paid services (feeds the Costs page interview)

After detecting external services, flag which ones typically have costs so the interview can ask about them specifically. Never invent costs — this step only identifies candidates.

Common paid services and what to check:

| Service detected | Likely cost driver |
|---|---|
| Anthropic / OpenAI / other LLM API | Per-token API usage |
| Supabase | Database plan (free tier has limits) |
| Stripe | Transaction fees (not a monthly line, but worth noting) |
| Vercel | Hosting plan (Pro if team, otherwise free) |
| Railway | Compute / resource usage |
| Render | Compute plan |
| AWS / GCP / Azure | Varies widely — flag for user to confirm |
| SendGrid / Postmark / Resend | Email volume plan |
| Twilio | SMS/call usage |
| ElevenLabs | Audio generation usage |
| Langfuse | Observability plan |
| Clerk / Auth0 | Auth MAU plan |
| Pinecone | Vector DB plan |
| Planetscale / Neon | Database plan |
| Sentry / Datadog / LogRocket | Monitoring plan |
| GitHub | Team / Enterprise plan if not on free |

Also check:
- `package.json` / requirements for any commercial SDK packages
- Deployment config for multiple services (each may have its own cost)
- Any `BILLING_*`, `SUBSCRIPTION_*`, or `PLAN_*` env vars that hint at a managed billing integration

Produce a list of "likely paid services" to take into the Costs interview. Do not guess tiers or figures.

## Red flags to surface

Worth mentioning explicitly:
- Dependencies that are wildly out of date
- Known-deprecated packages
- Two libraries doing the same thing (e.g. both `axios` and `node-fetch`)
- Env vars referenced in code but not in `.env.example`
- Env vars in `.env.example` but not referenced anywhere
- Substantive TODO / FIXME comments
- Large commented-out blocks
- Empty or mostly-empty test directories

List neutrally for the user to action or dismiss.
