# Decision Log Entry Format

The format for individual entries on the Decision Log page. Each entry is FAQ-style: a question header followed by structured answer.

## Format

### Heading

The entry heading is phrased as the question someone would ask in a review, pitch, or onboarding conversation. Examples:

- ## Why did we choose Apollo over Hunter for lead enrichment?
- ## Why is Focus built on Supabase rather than a self-hosted Postgres?
- ## Why did we decide to use Playwright instead of the Indeed Job Sync API directly?

The question phrasing matters. It makes the log scannable — a reader can scroll the headings and immediately know which decisions are documented.

### Body

Below the heading, four labelled lines:

```
**Date:** {YYYY-MM-DD}
**Decision:** {one sentence stating what was chosen}
**Rationale:** {2-4 sentences on why}
**Alternatives considered:** {bulleted list of options not chosen, each with a brief reason}
```

Optionally, two more lines if relevant:

```
**Trade-offs accepted:** {what we gave up by choosing this}
**Revisit if:** {condition that would prompt reopening this decision}
```

### Example entry

```
## Why did we choose Apollo over Hunter for GrowthOS lead enrichment?

**Date:** 2026-03-22
**Decision:** Use Apollo Professional ($149/mo) for lead enrichment in the GrowthOS pipeline.
**Rationale:** Credit cost analysis on the calls we were actually making (revealPerson + enrichCompany) showed Apollo at ~60% lower cost per qualified lead than Hunter for our UK SME segment. Data quality on filterable fields (SIC code, employee count) was materially better. Apollo's Professional tier includes the volume we need without the Enterprise jump.
**Alternatives considered:**
- Hunter — rejected on credit cost and weaker UK company data
- Direct Companies House + scraping — rejected as primary because enrichment quality is too low without a paid layer; we still use it as a waterfall fallback
- Clearbit — rejected on price ($500+/mo)

**Trade-offs accepted:** Locked into one vendor for primary enrichment. Migration cost if we change later.
**Revisit if:** Apollo pricing changes materially, our segment shifts, or we hit credit limits.
```

## Capture flow (used by `log-decision` command)

When the user invokes `log-decision`, ask only what's needed:

1. **The decision** — what was chosen
2. **The rationale** — why
3. **Alternatives** — what was rejected and briefly why
4. **Date** — default to today, accept override
5. **Product** — which wiki this applies to (if not obvious from context)

If the user provides everything in their first message ("log a decision: we chose X over Y because Z"), don't pad with extra questions. Format and submit.

If they're terse ("log decision: chose Apollo"), prompt for the missing pieces but keep it tight — three follow-up questions max.

The whole flow should complete in under a minute.

## Heading generation

If the user gives the decision but not a question heading, derive one. Pattern:

- For a chosen technology / vendor: "Why did we choose {X} {for Y / over Z}?"
- For an architectural choice: "Why is {component} {built / structured} this way?"
- For a deferral: "Why did we decide not to {build / use} {X}?"

Show the generated heading to the user before submitting so they can adjust if it doesn't match how they'd phrase the question.
