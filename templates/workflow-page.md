# Architecture > Key Workflows > [Workflow Name] Page Template

A single end-to-end workflow. Uses the SOP-style numbered structure observed in the firm's existing wiki — sequential steps with sub-numbered detail.

One page per workflow. Don't combine multiple workflows on one page.

## Properties

- **Name**: `{Workflow name}` (descriptive, e.g. "Publishing a Job from Zoho to Indeed")
- **Owner**: User
- **Verification**: Empty (draft)
- **Tags**: `Technical`, `How We Work`

## Icon and cover

- **Icon**: ⚙️ or workflow-domain-specific (e.g. 📤 for publishing, 🔄 for syncing)
- **Cover**: domain-themed gallery image

## Body structure

### 1. Definition callout

```
ℹ️ {What this workflow does in one sentence and when it runs.}
```

### 2. One-sentence purpose

Plain prose, why this workflow exists or what problem it solves.

### 3. Trigger

A short paragraph or bulleted list explaining what causes this workflow to run:
- User action
- Scheduled job
- Webhook from external service
- Status change in another system

### 4. Numbered steps

Top-level steps numbered 1, 2, 3. Sub-steps as 1.1, 1.2 (using Notion's nested numbered lists).

Each step:
- Action verb first
- Concrete details: file paths in inline code, code snippets in code blocks, URLs as links
- Callouts for branches, retries, error handling

Example:

```
1. Detect status change

   1.1 The Zoho Recruit blueprint fires a webhook to `POST /api/zoho/webhook` when a job moves to `published` status.
   
   1.2 The webhook handler in `apps/api/src/routes/zoho.ts` validates the signature using `ZOHO_WEBHOOK_SECRET`.
   
   ⚠️ If signature validation fails, the request is rejected with 401 and the failure is logged but not retried.

2. Fetch job details from Zoho

   2.1 Call `GET /recruit/v2/JobOpenings/{id}` with a fresh OAuth token.
   2.2 Refresh the token if expired (handled by the Zoho client wrapper).

3. Publish to Indeed via Playwright

   3.1 The Playwright script in `apps/sync/playwright/indeed-publish.ts` launches a browser session.
   3.2 Authenticates using credentials from env vars `INDEED_USER` and `INDEED_PASS`.
   3.3 Fills out the publish form using the Zoho job data.
   3.4 Submits and captures the resulting Indeed job ID.

4. Update Zoho with the Indeed job ID

   4.1 Call `PATCH /recruit/v2/JobOpenings/{id}` with the indeed_job_id custom field.
   4.2 Mark the job as `synced` in our local sync table.

5. Notify on failure

   If any step above fails, the failure is logged to the sync table with the error and a Slack notification fires to `#ops-alerts`.
```

### 5. Outcome

A short section stating what the user/system sees when the workflow completes successfully.

### 6. Failure modes

Bulleted list of known things that can go wrong, with the recovery action:
- **Signature validation fails**: webhook rejected, no retry — usually means the secret is misconfigured
- **Indeed login fails**: Playwright captures screenshot to `tmp/`, alert fires
- **Token refresh fails**: workflow fails fast — usually means the refresh token has been revoked

### 7. Related pages (optional)

Page mentions to:
- Integration pages for services involved (e.g. Zoho integration, Indeed integration)
- Related workflows
- Decision log entries that explain why this is structured this way

## What NOT to include

- Every line of code in the implementation (the code is the source of truth)
- Detailed env var explanations (link to Env Vars page)
- Schema details (link to Data Model page)

## Source notes

- Trigger and steps are largely user-stated, scaffolded from code paths
- File paths and function names code-verified
- Error handling code-verified where present
