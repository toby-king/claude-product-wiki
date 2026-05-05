# Operations > Environment Variables Page Template

Complete reference of every env var the product uses. Mostly code-verified.

## Properties

- **Name**: `Environment Variables`
- **Owner**: User
- **Verification**: Empty (draft)
- **Tags**: `Technical`, `Reference`

## Icon and cover

- **Icon**: 🔐
- **Cover**: security / config-themed gallery image

## Body structure

### 1. Opening paragraph

Plain prose: "Every environment variable {product} reads. Names extracted from code; descriptions captured during interview. The `.env.example` file is the canonical list of what's needed for local dev."

### 2. Source-of-truth callout

A small info callout:
```
ℹ️ Source of truth: .env.example in the repo. If a var is here but not in .env.example, that's a bug to fix.
```

(Link to the actual file using a Notion bookmark or inline link.)

### 3. Table of contents block

Insert a `/table_of_contents` block if the page is long (multiple categories of env vars).

### 4. Variables — inline database

Use a Notion **inline database** (NOT a simple table block) with these columns:

- **Name** (Title): the env var as referenced in code
- **Purpose** (Text): one-liner from user, or empty with a TODO if unanswered
- **Required** (Select): Yes / No / Conditional
- **Environment** (Multi-select): Local / Staging / Production
- **Category** (Select): Auth / Database / External API / Feature flag / Config (helps with grouping)
- **Where used** (URL or text): file path link to repo

The inline database lets the reader sort and filter — which becomes essential as env var counts grow. A simple table block does not. Always default to inline database here.

If there are 30+ env vars, no need to split into sections; the database's filter views handle it. Optionally create saved views (e.g. "Production only", "Required only") if the MCP supports it.

### 5. Secrets management

A short callout:
```
🔒 Production secrets live in {Railway / Doppler / 1Password / etc.}. Local dev uses the .env file (gitignored). Never commit secrets.
```

### 5. Adding a new env var (small section)

A short standard procedure for the team to follow when adding env vars:

1. Add to `.env.example` with a comment explaining what it's for
2. Reference it in code (typed where possible)
3. Add to this wiki table via `add-page` or manual edit
4. Add to the deployment platform's env var config
5. Notify the team if other people need it locally

## What NOT to include

- Actual env var values (ever)
- Hints about what production values look like (no "starts with sk_live_" — that's a leak vector)
- Per-developer overrides

## Source notes

- Names entirely code-verified (grep)
- Required/optional best-effort from how the var is used (e.g. with a default = optional, throws if missing = required), confirm with user
- Purpose user-stated
- Where used code-verified
- Secrets management user-stated
