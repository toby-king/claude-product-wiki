# Integrations > [Service Name] Page Template

One page per major external service the product integrates with. Created only if the product has integrations (some products may have none).

## Properties

- **Name**: `{Service Name}` (e.g. "Anthropic", "Sendgrid", "Zoho Recruit")
- **Owner**: User
- **Verification**: Empty (draft)
- **Tags**: `Technical`, `Reference`

## Icon and cover

- **Icon**: 🔌 or service-specific (e.g. 📧 for email services, 🤖 for AI services, 💳 for payments)
- **Cover**: domain-themed gallery image, or service logo if available

## Body structure

### 1. Definition callout

```
ℹ️ How {product} uses {service}, including auth setup, env vars, and operational notes.
```

### 2. What it's used for

1-3 sentences in plain language. What does {product} actually call this service to do? Source: user-stated.

Example:
> Used for parsing CVs and generating interview questions. The Anthropic API is called from the Node backend whenever an applicant uploads a CV (parsing) or when a job is published (question generation).

### 3. Service details (small at-a-glance section)

- **Provider**: {Service name}
- **Plan/Tier**: e.g. "Anthropic Pro Tier 1"
- **Account owner**: e.g. "Company account, billed to ops@"
- **Docs**: link to vendor docs

### 4. Auth method

How auth works for this integration:
- **API key in header** — env var `ANTHROPIC_API_KEY`
- **OAuth with refresh token** — env vars `ZOHO_CLIENT_ID`, `ZOHO_CLIENT_SECRET`, `ZOHO_REFRESH_TOKEN`
- **Webhook signature** — env var `STRIPE_WEBHOOK_SECRET`

For OAuth flows, link to a sub-page or the vendor docs explaining how to obtain a refresh token initially.

### 5. Env vars used

Bulleted list of env vars with their purpose. Cross-reference to the Env Vars page for full detail:
- `ANTHROPIC_API_KEY` — auth
- `ANTHROPIC_MODEL` — which model to use (defaults to claude-haiku-4-5)

### 6. Where it's called from

File paths in the codebase where this integration is used:
- `apps/api/src/services/cv-parser.ts` — CV parsing
- `apps/api/src/services/question-gen.ts` — interview question generation

Link to repo if Repo URL is set on parent.

### 7. Rate limits and costs (short section)

Brief notes on operational concerns:
- Rate limits we hit or get close to
- Cost considerations
- Known quotas

Example:
> Anthropic Tier 1 limit is 50 RPM for Sonnet. We currently average ~5 RPM in production but burst to ~30 RPM during batch CV parses.

### 8. Failure modes

What happens when this service is down or returns errors:
- Does the app fail open or fail closed?
- Are errors retried? With what backoff?
- Is there a fallback?
- Does a failure alert anyone?

Example:
> If Anthropic returns 429 or 5xx, the request is retried up to 3 times with exponential backoff. After 3 failures, the CV is marked as `parse_failed` in the DB and a Slack alert fires.

### 9. Sandbox / test mode (if applicable)

How this integration behaves in dev/staging vs production:
- "Test API keys are used in dev (start with `sk_test_` for Stripe)"
- "Production hits real Anthropic API in dev (no sandbox), use cheap models in dev"
- "Sendgrid sandbox flag is enabled in dev — emails accepted but not delivered"

## What NOT to include

- Actual API keys or credentials
- Vendor documentation copy-pasted (link instead)
- Implementation code (link to repo file paths)

## Source notes

- Service identification code-verified (SDK imports, API URLs)
- Auth method code-verified
- Env vars code-verified
- Call sites code-verified
- Purpose, account ownership, rate limit experience, failure handling user-stated
