# Getting Started Page Template

The first technical page a new contributor reads. Minimal happy-path instructions to get the project running locally. Anything more advanced belongs in dedicated pages (Running Locally, Deployment, etc.).

## Properties

- **Name**: `Getting Started`
- **Owner**: User
- **Verification**: Empty (draft)
- **Tags**: `Technical`, `How To`

## Icon and cover

- **Icon**: 🚀
- **Cover**: clean / welcoming gallery image

## Body structure

### 1. Definition callout

```
ℹ️ Minimal steps to clone the repo, install dependencies, configure the environment, and run {product} locally. For deeper detail see {Running Locally} and {Environment Variables}.
```

### 2. Prerequisites (short bulleted list)

Specific tools and versions needed before starting. Pull from package.json engines, README, or ask user.

Example:
- Node.js 20+
- pnpm 9+
- A Supabase account (or local Supabase via Docker)
- An Anthropic API key

### 3. Steps (numbered list)

5-10 concrete steps from clone to running. Each step is a single action with the exact command if applicable.

Example:
1. Clone the repo: `git clone {url}`
2. Install dependencies: `pnpm install`
3. Copy the example env file: `cp .env.example .env`
4. Fill in env vars — see Environment Variables for what each does
5. Run database migrations: `pnpm db:migrate`
6. Start the dev server: `pnpm dev`
7. Open `http://localhost:5173`

If any step has a likely failure mode, include a callout immediately after:

```
⚠️ If `pnpm db:migrate` fails with a connection error, check that your DATABASE_URL is set correctly and the database is reachable.
```

### 4. What to do next (short section)

Page mentions to:
- Architecture > System Overview (to understand what's running)
- Operations > Running Locally (for deeper local-dev info)
- The Decision Log (for context on why things are built this way)

## What NOT to include

- Full env var documentation (link to Env Vars page instead)
- Deployment instructions (link to Deployment page)
- Architectural rationale (link to Architecture)
- Multiple paths or branches — keep it the happy path only

## Source notes

- Step content largely user-stated, scaffolded from package.json scripts
- Prerequisites partly verifiable (engines, lockfile), partly user-stated
- Cross-references must point to real pages that will exist in the wiki
