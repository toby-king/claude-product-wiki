# Changelog Page Template

Reverse-chronological log of meaningful changes to the product or its documentation. Single changelog covering both — split is more maintenance than it's worth.

## Properties

- **Name**: `Changelog`
- **Owner**: User
- **Verification**: Empty (draft) on creation; subsequent entries don't change page-level Verification
- **Tags**: `Product`

## Icon and cover

- **Icon**: 📜
- **Cover**: abstract / timeline-themed image

## Body structure

### 1. Definition callout

```
ℹ️ Reverse-chronological log of meaningful changes to {product} and its documentation. Add entries as things ship, change, or are decided.
```

### 2. Entry format

Each entry is a heading-2 with the date, followed by tagged bullet points.

```
## 2026-04-15

- **[Shipped]** Indeed sponsorship automation now fires from Zoho status change. [PR link]
- **[Docs]** Updated env var page to add INDEED_API_KEY.
- **[Decision]** Switched Smartlead from Pro to Basic tier. [Decision Log entry]
```

Tag types:
- **[Shipped]** — new functionality went live
- **[Changed]** — existing functionality modified
- **[Removed]** — functionality removed
- **[Docs]** — wiki/documentation update
- **[Decision]** — meaningful decision logged (link to decision log)
- **[Fixed]** — bug fix worth noting

### 3. Archiving threshold

When the page exceeds ~50 entries, suggest archiving older entries to a child page called "Changelog Archive {Year}" so the main page stays scannable.

## Adding entries

When called via `add-changelog`:
1. Find the most recent date heading on the page.
2. If today's date matches it, append the new bullet under the existing heading.
3. If today's date is newer, insert a new date heading at the top of the entries section with the new bullet under it.
4. Always add at the top, never the bottom — reverse chronological is the convention.

## What NOT to include

- Internal noise (small refactors, dependency bumps that don't affect anything)
- Entries that say nothing ("Various improvements")
- Marketing language
- Anything inferred — every entry needs a real source (a shipped change, a decision, a doc update)

## Source notes

- **All entries**: entirely user-stated — never infer or invent changelog entries
- The page header callout is written on creation; entries are appended over time via `add-changelog`
- Page-level Verification stays Empty on creation; adding entries does not change it — the user sets Verification when they're happy with the page
