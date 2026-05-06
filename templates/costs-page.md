# Costs Page Template

Tracks the estimated monthly running cost of the product across all paid services. Always created — every product has running costs worth recording.

## Properties

- **Name**: `Costs`
- **Owner**: User
- **Verification**: Empty (draft)
- **Tags**: `Technical`, `Reference`

## Icon and cover

- **Icon**: 💰
- **Cover**: finance / billing-themed image (calculator, invoice, coins, budget spreadsheet)

## Body structure

### 1. Definition callout

```
ℹ️ Estimated monthly running cost for {Product}. Costs are user-stated — verify against actual invoices before treating these figures as authoritative.
```

### 2. Cost table

One row per paid service. If a cost is unknown, write `Unknown` — never leave a cell blank or invent a figure.

| Service | Plan / Tier | Est. Monthly Cost | Notes |
|---|---|---|---|
| {Service} | {Free / Starter / Pro / etc.} | {£X / Unknown} | {Usage-based, flat rate, etc.} |
| **Total** | | **£X** | |

Rules:
- Always include a bold **Total** row at the bottom.
- If any individual costs are Unknown, the Total row should read `£X + unknowns`.
- Use the currency the user provided. Default to £ for UK-based teams.
- If a service is on a free tier with known limits, note the limit in the Notes column.

### 3. Usage-based notes (if applicable)

For any service with variable costs (e.g. API usage, storage), add a short note below the table:

```
> ⚠️ {Service} costs scale with {usage metric}. Current typical monthly usage: {figure or Unknown}.
```

### 4. Services detected but cost unconfirmed

If the scan found services where cost is unknown and the user couldn't confirm, list them here so they aren't silently dropped:

```
The following services were detected in the codebase but their costs were not confirmed:
- {Service} — {why it might have a cost}
```

Leave this section out entirely if all detected services were accounted for.

## Source notes

- **Entirely user-stated** — costs cannot be derived from code. The scan identifies *which* services are likely paid; the user provides the actual figures.
- Never invent a cost, tier name, or usage figure. If the user says "I'm not sure", write `Unknown`.
- On `update`, flag this page for re-review if new paid services were detected in the codebase.
