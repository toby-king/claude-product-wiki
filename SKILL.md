---
name: product-wiki
description: Build and maintain comprehensive technical product documentation in Notion for software products. Use this skill whenever the user wants to document a software product, build dev docs, create or update a product wiki, log a product decision, or add a changelog entry. Produces structured Notion pages following the firm's existing visual conventions (cover images, emoji icons, properties schema) and the standard developer-documentation pattern (Overview, Getting Started, Architecture, Operations, Integrations, Reference). Always writes to Notion as drafts (Verification = Empty) for user review before promotion to live. Zero tolerance for hallucination — every claim must be traceable to code, the user's input, or be explicitly marked as inferred.
---

# Product Wiki

A skill for building and maintaining technical product documentation in Notion. Produces dev-docs-style structured pages organised across three layers (Product, Product Reference, Technical) with the firm's existing visual and metadata conventions.

## Core principles

These are the principles every command follows. They are not optional.

### Three sources of truth

Every line that goes into a wiki must come from one of three places:

1. **Code-verified** — extracted directly from the codebase. Dependencies in a manifest, env vars referenced in code, routes registered in the router, schema in migrations. Write these confidently.
2. **User-stated** — captured from the user during the interview. Project intent, deployment context, business rationale. Quote or paraphrase faithfully without embellishment.
3. **Explicitly inferred** — patterns suggested by code but not confirmed. These must either be verified by asking the user, or written with a clear hedge ("appears to handle X based on imports") never as a confident claim.

If a section cannot be filled from these three sources, leave it as a `<!-- TODO: -->` marker. A shorter truthful wiki always beats a longer partly-fictional one.

### Notion-direct with Draft status

All output goes to Notion via the Notion MCP, never to local markdown as the final destination. To make this safe:

- New pages are created with the Verification property left **Empty** (= draft state).
- The user reviews drafts in Notion itself, then sets Verification to a confirmed state when satisfied.
- Never set Verification to a confirmed state directly — that's the user's signal of approval.
- Always present the full structural plan to the user *before* writing anything. Once they approve, write all pages in a single batch operation rather than streaming pages into existence.

### Match the firm's existing conventions

Pages must feel native to the existing Notion workspace, not stylistically distinct. This means:

- Use the existing properties schema: **Owner** (person), **Verification** (status), **Tags** (multi-select), **Last edited time**, **Created time**.
- Use cover images on every page. The skill should suggest reasonable defaults from Notion's gallery (or Unsplash via Notion's picker) based on the page topic, but always allow the user to override.
- Use emoji icons on every page. Pick something topical.
- Use the tag vocabulary below. Tags serve two purposes: filtering the Gallery Home view (Top Level) and grouping in the default gallery. Apply them accurately so navigation works.
  - **Top Level** — every page that should appear on the Gallery Home view (Start Here, Overview, Architecture, How To's, Integrations, Reference, Changelog, Decision Log, Strategy). Sub-pages under section pages do NOT get this tag.
  - **Overview** — the Overview page and Start Here page.
  - **Product** — Overview, Changelog, Strategy.
  - **Technical** — Architecture and its sub-pages, Integrations and its sub-pages, Reference and its sub-pages, How To's and its sub-pages.
  - **How To** — How To's section page and all Getting Started, Running Locally, Deployment sub-pages.
  - **SOP** — step-by-step workflow pages.
  - **Reference** — Decision Log, Env Vars, API Endpoints, config pages.
- Use callouts for definitions, warnings, and gotchas, not for general body content.
- Use numbered lists for sequential steps (no toggles burying steps).
- Use tables for structured reference data (env vars, integrations, routes).
- Toggles only for genuinely optional reference content (full env var lists when there are 30+, exhaustive route enumerations).

### Page granularity

Short focused pages over mega-pages. A reader should be able to land on the page they need, get the answer, and leave. Aim for 200-600 words per page in most cases. If a page is approaching 1000+ words, it probably wants to be split.

Adaptive structure: small products skip categories that don't apply, complex products add what they need. The skill proposes a structure based on what the codebase actually contains, not a fixed template.

### Always pause for user input at three points

These pauses are the user-control mechanism that makes Notion-direct safe. Never skip them.

1. **After scan, before structure proposal** — "here's what I found in the codebase, anything missing or wrong?"
2. **After structure proposal, before interview** — "here's the page layout I'm proposing, any changes?"
3. **After interview, before writing to Notion** — "here's everything I have, ready to create the drafts?"

## The wiki structure

Every product wiki follows this shape. The critical principle — learned from the best wikis in the wild — is that **section pages must be rich landing pages, not empty containers**. A reader who clicks "Architecture" should immediately understand what the section covers and see links to everything inside it. They should never land on a blank page and have to guess.

### Top-level pages (tagged "Top Level" — appear on the Gallery Home view)

| Page | Emoji | Type | Purpose |
|------|-------|------|---------|
| **Start Here** | 👋 | Rich landing | Mandatory entry point. Reading order, key facts, FAQ toggles. Always created. |
| **Overview** | 📋 | Rich landing | What it is, who it's for, status, quick-reference table, "where to go next" links |
| **Architecture** | 🏗️ | Section landing | Describes the system shape → links to System Overview, Data Model, Key Workflows |
| **How To's** | 🤷 | Section landing | Guides for common tasks → links to Getting Started, Running Locally, Deployment |
| **Integrations** | 🔌 | Section landing | External services → links to one page per integration (skip if none) |
| **Reference** | 📖 | Section landing | Lookup material → links to Env Vars, API Endpoints, config pages (skip if none) |
| **Changelog** | 📝 | Leaf page | Reverse-chronological log of meaningful shipped changes |
| **Decision Log** | 🗂️ | Leaf page | Chronological record of product and technical decisions |
| **Strategy** | 🗺️ | Leaf page | Positioning, roadmap, why this exists (only create if there's real content) |

### Sub-pages (nested under section landing pages, NOT tagged "Top Level")

Sub-pages live *inside* their section page. They appear as embedded page links in the section landing page and as sidebar children in Notion. They don't appear on the Gallery Home view. Prune any sub-pages that don't apply to the specific product.

| Under Architecture | Under How To's | Under Integrations | Under Reference |
|--------------------|----------------|--------------------|-----------------|
| System Overview | Getting Started | One page per service | Environment Variables |
| Data Model | Running Locally | | **Costs** (always created) |
| Key Workflows/ | Deployment | | API Endpoints |
| └─ [one per major workflow] | | | (config, schema, etc.) |

## Section landing page pattern

This pattern must be followed for every section page (Architecture, How To's, Integrations, Reference) and for the Overview and Start Here pages. It is what makes the wiki navigable to someone who has never seen the product before.

**Section pages (Architecture, How To's, Integrations, Reference):**
1. **Grey callout** (`ℹ️` icon, `gray_background`) — a single sentence explaining what this section is. No jargon.
   - Example: *"This section documents how the system is built: its services, data model, and key workflows."*
2. **Purpose paragraph** — 2–3 sentences on what the section provides clarity over and why it matters.
3. **Bullet list** — 3–5 bullets, each one a specific thing a reader will find here.
4. **Sub-page links** — embedded Notion page links (one per sub-page), grouped under a `## Heading` if there are multiple logical groups. Each group gets a brief description before the links.

Never leave a section page empty. Even if there's only one sub-page inside it, the section page should explain the section and link to that sub-page.

**Start Here page** — see `templates/start-here-page.md`. It follows a different but equally specific pattern.

**Overview page** — see `templates/overview-page.md`. It ends with a "Where to go next" section that links to the 3–4 most important deeper pages with one-liners.

## The commands

The skill exposes six commands. Identify which one applies from the user's request. If unclear, ask.

### `build` — Full pipeline for a new product wiki

This is the main entry point. Use when the user wants to document a product that has no existing wiki.

**Parent page modes**: at the orient step, ask whether the user has an existing Notion page they want to use as the parent, or whether the skill should create a starter parent page. The recommended workflow is for users to design the parent page themselves once they have a baseline to react to, so on the first run the skill creates a starter parent; on subsequent runs (or once the user has a manual parent page) the skill should accept the existing parent and only create sub-pages underneath. See `references/notion-structures.md` for both modes.

Workflow:
1. **Orient** — confirm:
   - The project (repo path)
   - The product name
   - **Parent page mode**: ask "Do you have an existing Notion page you want to use as the wiki parent, or should I create a starter parent page that you can redesign later?" If existing, get the page URL or ID. If new, get the parent location (which company sub-tree, etc.).
2. **Scan** — analyse the codebase per `references/scan-checklist.md`. Produce a scan report summarising tech stack, dependencies, integrations, env vars, routes, schema, scripts, deployment hints. Show this to the user.
3. **Pause 1** — "here's what I found, anything missing?" Wait for input.
4. **Propose structure** — based on scan, propose the page tree. Include only categories that make sense for this product. Show the user.
5. **Pause 2** — "here's the structure, any changes?" Wait for input.
6. **Draft verifiable sections** — write the content for pages that can be sourced from code (Tech Stack, Env Vars, Integration pages, Reference pages). Mark sections as `[code-verified]` internally.
7. **Interview** — for each page that needs user input, ask targeted questions per `references/question-bank.md`. Batch questions (3-7 at a time). Save progress between batches in case the user steps away.
8. **Pause 3** — show full structure with all content filled in. "Ready to create these in Notion as drafts?"
9. **Write to Notion** — follow `references/notion-structures.md` exactly. The creation order below is mandatory — each step depends on the previous one having IDs to reference.

   **9a — Create the home page (always, no exceptions)**
   Create a regular Notion page as the wiki's landing page. This is what users navigate to — it is NOT the database. Body content (top to bottom): status paragraph + callout (💡, gray_background) + divider + two-column layout (left column: "Wiki sections" heading + description + placeholder bullet list; right column: empty for now — the inline gallery block is placed here manually after step 9c). Parent: the user's chosen location.

   **9b — Create the database at the same level as the home page**
   `parent.page_id = <user_chosen_parent_id>` — the same parent the home page lives in, NOT inside the home page. If you nest the database inside the home page, it appears as a closed database block in the page body AND the inline gallery also appears, giving a confusing duplicate. Keep them as siblings.

   **9c — Create views in this exact order (order determines default)**
   1. **Home** — gallery, filtered to Tags contains "Top Level", card preview = page_cover, grouped by Tags (hide empty groups, manual sort). First view created = default.
   2. **All Pages** — table, showing Page, Owner, Tags, Verification, Last edited time.

   Then embed the gallery inline on the home page:
   - Call `notion-create-view` with `parent_page_id = <home_page_id>` and `data_source_id = <database_id>`
   - type = gallery, filter = Tags contains "Top Level", card_preview = page_cover, group_by = Tags
   - Notion places this block at the bottom of the page. After the build, drag it into the right column in the Notion UI and adjust the column divider to ~25/75 width.

   **9d — Create all top-level pages inside the database**
   Parent = database ID. Order: Start Here → Overview → section landing pages → leaf pages. Every page gets a cover image, emoji icon, Verification = Empty, Owner, and Tags.

   **9e — Create all sub-pages as children of their section pages**
   Parent = section page ID (not the database). Do this after section pages exist and have IDs.

   **9f — Insert real page-mention links into section pages, Start Here, and Overview**
   Now that sub-pages have IDs, update:
   - Section landing pages — replace placeholder sub-page bullets with real page-mention links
   - Start Here reading order list — replace any placeholder items with real page-mention links
   - Overview "Where to go next" — replace placeholder text with real markdown links (e.g. `[System Overview](https://notion.so/...)`) pointing to the actual sub-pages and top-level pages
   A reader must be able to click every link. See page mention block format in `references/notion-structures.md`.

   **9g — Insert real page-mention links into the home page**
   Update the home page's "Wiki sections" bullet list with page-mention links to every top-level page now that they have IDs.

10. **Confirm** — share the **home page URL** (not the database URL) with the user. State how many pages were created and flag any sections needing manual input. Always end with this exact note:

   > **Two quick manual steps to finish the home page layout:**
   > 1. Open the home page in Notion. The gallery view will be sitting at the bottom of the page — drag it up into the right column.
   > 2. Drag the column divider left until the left column is roughly 25% wide and the right column is 75%. This gives the nav list / gallery split its intended look.
   >
   > Everything else is done.

### `update` — Refresh an existing wiki against the current codebase

Use when an existing wiki may have drifted from the code.

Workflow:
1. **Locate** — find the existing wiki in Notion (ask user for parent page if not given).
2. **Read existing wiki** — fetch all pages and their content.
3. **Scan codebase** — same as build.
4. **Diff** — identify:
   - **Stale**: claims in the wiki the code contradicts
   - **Missing**: things in the code not yet in the wiki (new integrations, new env vars, new routes)
   - **Confirmed**: still-accurate content
5. **Report diff** — show user before changing anything.
6. **Approve changes** — let user pick which updates to apply.
7. **Interview only on deltas** — don't re-ask things already answered.
8. **Write updates** — modify pages in place. Set Verification back to Empty for any modified page. Add a changelog entry summarising what changed.
9. **Confirm** — show user what was updated.

### `add-page` — Add a single page to an existing wiki

Use when something new needs documenting (a new integration, a new workflow page) without a full wiki rebuild.

Workflow:
1. **Locate parent wiki and target category.**
2. **Determine page type** — is this an integration page, a workflow page, a reference page, or something else?
3. **Scan and interview** — narrow scope, just enough for this one page.
4. **Show draft.**
5. **Write to Notion as draft.**
6. **Add changelog entry.**

### `log-decision` — Append a single entry to the decision log

Use when the user wants to record a decision. This must be fast — the whole point is low friction so it actually gets used.

Workflow:
1. **Capture the decision.** Ask only what's needed:
   - The decision (what was chosen)
   - The rationale (why)
   - The alternatives considered (what was rejected and briefly why)
   - The date (default to today)
   - Which product this applies to (if not obvious from context)
2. **Format as FAQ-style entry** per `templates/decision-log-entry.md`.
3. **Append to the decision log page** in the relevant product wiki.
4. **Confirm.** Show the entry as it now appears.

This command should be runnable in under a minute. Don't over-interview. If the user provides everything in their first message, just write it.

### `add-changelog` — Append a changelog entry

Often called automatically by `update` and `add-page`. Can also be invoked directly.

Workflow:
1. **Capture entry**: date, type (Shipped / Changed / Removed / Docs / Decision), one-line summary, optional link.
2. **Append to changelog page** at the top (reverse chronological).
3. **Confirm.**

If the changelog has 50+ entries, suggest archiving older ones to a child page.

### `scan` — Just analyse the codebase and report

Use when the user wants to understand what's in a codebase before deciding what to do, or wants to see what the skill would extract without committing to a build.

Workflow:
1. **Run the codebase scan** per `references/scan-checklist.md`.
2. **Produce scan report**. Show the user.
3. **Stop.** No structure proposal, no interview, no Notion writes.

## Working with Notion via MCP

The skill uses the Notion MCP to read and write pages. Key operations:

> **Callout format warning**: Never use GitHub-style admonitions (`[!NOTE]`, `[!WARNING]`, `[!TIP]`) in Notion content. Notion does not render these as callouts — they appear as literal blockquote text with `[!WARNING]` visible to readers. Use only proper Notion callout block syntax (see `references/notion-structures.md`) or a plain blockquote `> emoji content`.


- **Find parent page** — search for the product/company parent page where the wiki should live
- **Create database** — `notion-create-database` creates an inline database on a parent page with the standard properties schema (Name/title, Owner/people, Verification/status, Tags/multi-select, Created time, Last edited time). Sub-pages live inside this database.
- **Set gallery view** — `notion-create-view` on the database with type = gallery, card_preview = page_cover, and group_by = Tags. This is how the gallery card layout and tag grouping is established.
- **Create page** — with title, icon (emoji), cover, properties, and body blocks. When creating sub-pages, parent must be the database ID (not the parent page ID), so they appear as gallery entries.
- **Update page** — modify properties or body blocks
- **Append blocks** — add to an existing page (used heavily for changelog and decision log)

When creating sub-pages, always:
- Set parent to the database ID (not the wiki parent page)
- Set Verification to Empty (drafts)
- Set Owner to the user (or ask if uncertain)
- Apply Tags appropriately ("Technical" for dev-docs pages, "Product" for product layer, "Overview" for overview pages, etc.)
- Add cover image and emoji icon — these render as gallery card thumbnails

For complex pages with many block types (callouts, tables, headings, code blocks), build the block structure programmatically and submit in batches to avoid Notion API limits.

## Page composition

For each page type, follow the structure in `templates/`:

**Navigation / orientation pages (always load these):**
- `templates/start-here-page.md` — The mandatory entry point. Reading order, quick-reference table, FAQ toggles.
- `templates/section-page.md` — The section landing page pattern for Architecture, How To's, Integrations, Reference.
- `templates/overview-page.md` — Product overview with "Where to go next" links at the bottom.

**Technical pages:**
- `templates/getting-started-page.md` — Getting Started page
- `templates/architecture-overview-page.md` — System Overview (sub-page under Architecture)
- `templates/data-model-page.md` — Data model page (sub-page under Architecture)
- `templates/workflow-page.md` — Key workflow page, SOP-style numbered structure (sub-page under Architecture/Key Workflows)
- `templates/running-locally-page.md` — Running Locally (sub-page under How To's)
- `templates/env-vars-page.md` — Environment Variables page (sub-page under Reference)
- `templates/deployment-page.md` — Deployment page (sub-page under How To's)
- `templates/integration-page.md` — Per-integration page (sub-page under Integrations)
- `templates/reference-page.md` — Generic reference page (API, schema) (sub-page under Reference)
- `templates/costs-page.md` — Costs page: monthly running costs across all paid services (sub-page under Reference, always created)

**Product / governance pages:**
- `templates/strategy-page.md` — Strategy page (use sparingly, only if real content exists)
- `templates/changelog-page.md` — Changelog page structure
- `templates/decision-log-page.md` — Decision log page structure
- `templates/decision-log-entry.md` — Format for individual decision entries

Each template specifies the section structure, the appropriate Notion blocks, and where content comes from (code-verified vs user-stated).

## Anti-hallucination checklist (run before writing to Notion)

Before submitting any pages to Notion, verify:

- [ ] Every dependency mentioned exists in a real manifest file
- [ ] Every env var mentioned is actually referenced in code
- [ ] Every integration mentioned has a real SDK import or API call
- [ ] No costs, pricing tiers, or usage figures on the Costs page were invented — every figure came from the user
- [ ] Every route mentioned is really registered
- [ ] Every "we decided X" or "the team does Y" came from the user
- [ ] No invented version numbers, dates, team sizes, or metrics
- [ ] No sections filled with generic best-practice filler
- [ ] All TODOs are explicit rather than papered over
- [ ] Every page has Verification = Empty (draft)
- [ ] Every page has Owner set
- [ ] Every page has appropriate Tags
- [ ] Every page has a cover image and emoji icon

## Handling partial attention

The user may run this skill in background time while doing other work. Adapt:

- Keep each message self-contained so context isn't lost on re-read.
- Batch interview questions in groups of 3-7, not one at a time.
- Save progress to `.product-wiki-progress.json` after each batch so the session is fully resumable.
- If the user is silent for a while and there's verifiable work to do, do it and report progress rather than waiting idle.
- When resuming, summarise the current state in one line before continuing.

## Reference files

For details on specific aspects of the workflow, load the relevant reference:

- `references/scan-checklist.md` — Detailed codebase scanning checklist (load when running `scan`, `build`, or `update`)
- `references/question-bank.md` — Question patterns organised by page type (load before interview phase)
- `references/notion-structures.md` — How to translate content into Notion block structures, including how to build properties, tables, callouts, toggles, and relations via MCP (load when writing to Notion)
- `templates/` — Page-specific templates (load the relevant one when composing a page)
