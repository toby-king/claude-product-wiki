# Operations > Deployment Page Template

Where the product runs in production and how it gets there.

## Properties

- **Name**: `Deployment`
- **Owner**: User
- **Verification**: Empty (draft)
- **Tags**: `Technical`, `Reference`

## Icon and cover

- **Icon**: ☁️
- **Cover**: deployment / infrastructure-themed gallery image

## Body structure

### 1. Definition callout

```
ℹ️ Where {product} runs in production, how it gets there, and how to operate it.
```

### 2. Production environment

Short paragraph or bulleted list:
- **Platform**: e.g. Railway / Vercel / Fly.io / AWS
- **Region(s)**: e.g. eu-west-1
- **URL(s)**: production URL(s)
- **Resources**: rough sense of scale (number of instances, DB tier)

### 3. Other environments

If there are staging / preview / dev environments, describe each briefly:
- **Staging**: at staging.{domain}, mirrors production config but uses test data
- **Preview**: auto-deployed per-PR via {platform}

### 4. Deploy process

Numbered steps for how a deploy actually happens:

1. Push to `main` triggers GitHub Action `.github/workflows/deploy.yml`
2. Action runs tests
3. If tests pass, action triggers Railway deploy via {mechanism}
4. Railway builds the Docker image and rolls out
5. Health check on `/health` confirms successful deploy

If deploys are manual:
1. Run `pnpm deploy` from the main branch
2. Confirm at the prompt
3. Wait for the deploy to complete

### 5. Rollback

How to roll back if something goes wrong:
- Railway: use the deployments list in the dashboard to redeploy a previous version
- Or: revert the commit on main and let auto-deploy take it

Include the URL to the deploy dashboard.

### 6. Secrets and config

Brief note on where production secrets live (link to Env Vars page for the full list):
> Production env vars are managed in {Railway / Doppler / etc.}. See Environment Variables page for the full list.

### 7. Monitoring (if applicable)

- Where logs go (Railway logs / Datadog / Logtail)
- Where errors go (Sentry / etc.)
- Uptime monitoring (Better Uptime / etc.)
- Who gets paged on issues

If monitoring isn't set up, say so explicitly:
> ⚠️ No formal monitoring or alerting is currently in place. Issues are caught manually.

### 8. Access

Who has access to the production environment, briefly. Don't list individuals if it changes often — describe the access pattern:
> Production access is via the Railway dashboard. Toby has full access; other team members have read-only.

## What NOT to include

- Actual secrets or credentials
- Step-by-step "how to use Railway" content (link to vendor docs instead)
- Detailed infrastructure-as-code (link to Terraform / Pulumi files if they exist)
- Cost details (these go stale fast and aren't usually wiki-appropriate)

## Source notes

- Platform and process partly verifiable from CI config files, partly user-stated
- Production URL user-stated
- Secrets locations user-stated
- Monitoring setup user-stated
