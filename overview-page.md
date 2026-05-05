# Overview Page Template

The product layer entry point. Read by both technical and non-technical readers. Keep it short and oriented toward "what is this and how do I find what I need."

## Properties

- **Name**: `{Product Name}` (matches parent page name or close variant — e.g. "Focus Overview")
- **Owner**: User
- **Verification**: Empty (draft)
- **Tags**: `Overview`, `Product`
- **Optional properties** (if parent database supports them): Stack (multi-select from scan), Integrations (from scan), Status, Stage, Repo URL, Live URL

## Icon and cover

- **Icon**: 📖 or product-themed emoji
- **Cover**: hero image if user supplies, else abstract gallery image

## Body structure

### 1. Opening paragraph

Plain prose, 1-2 sentences: what this product is and the primary problem it solves. Not a callout — a regular paragraph.

### 2. Table of contents block

Insert a `/table_of_contents` block. Auto-generates from the H1/H2/H3s on the page. Skip if the page has fewer than ~4 sections.

### 3. At-a-glance callout

A single info callout at the top with the standalone facts as bold-labelled lines. Replaces multiple tiny H2 sections that each hold one fact (like "## Status" → "Live").

Format inside the callout:
```
**Status:** {Production, Beta, Internal-only, etc.}
**Owner:** {Name}
**Stage:** {e.g. v1 — S&D only, Phase 2 in scope}
**Stack:** {one-line summary}
**Live at:** {URL if applicable}
```

These facts should NOT also be repeated as separate H2 sections elsewhere on the page.

### 4. What it does (one paragraph)

2-4 sentences in prose. What the product does, who uses it, what it replaces or improves on. Plain language. Source: user-stated.

If there's a primary trigger/cadence pattern (like the Geckoboards two-trigger setup), include a small Notion table immediately after this paragraph showing it. Use a simple `/table` block, not an inline database — this is at-a-glance content, not reference data.

### 5. Scope (if relevant)

For products with explicit scope boundaries (in-scope / out-of-scope), use a small two-column Notion table. This is one of the most useful things on a product Overview when it applies.

### 6. Stack (if not already covered in the at-a-glance callout)

A small `/table` block with two columns: Component | Technology. Keep to 4-7 rows. For longer stack details, link to the Architecture > System Overview page instead.

### 7. What's next

A short vertical list of page mentions (NOT prose with links) pointing readers to the main parts of the wiki:
- @Getting Started — to run the project locally
- @System Overview — for the architecture
- @Operations — for operational tasks
- @Integrations — for external services

Use real Notion page mention blocks, not text + link.

### 8. Open questions callout (if any unanswered)

Only include if there are unresolved questions during creation:

```
💡 Open questions
- {question 1}
- {question 2}
```

## What NOT to include

- Marketing pitch or mission statement language
- Detailed roadmap (deferred unless explicitly added)
- Full feature list (belongs in Architecture or in feature-specific pages)
- Personnel beyond the Owner property
- Financial information
