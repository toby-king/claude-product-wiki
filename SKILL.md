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
- Use the existing tag vocabulary where it applies (Overview, How To, How We Work, SOP) plus a new "Technical" tag for the dev-docs pages and "Product" for product-layer pages.
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

## The three-layer wiki structure

Every product wiki follows this shape, with categories pruned to what's relevant:

**Product layer** (small, narrative, for both technical and non-technical readers)
- Overview — what it is, who it's for, current status, owner
- Strategy — positioning, target customer, why this exists (only if there's substantive content; skip if not)
- Changelog — reverse-chronological log of meaningful changes

**Product Reference layer** (artefacts, mostly stable)
- Decision Log — chronological record of meaningful product/technical decisions, formatted as FAQ-style entries

(Diagrams, worksheets, roadmap deliberately deferred until there's content to fill them.)

**Technical layer** (the dev-docs spine)
- Getting Started — clone, install, run locally happy path
- Architecture/
  - System Overview — high-level diagram + components
  - Data Model — key entities and relationships
  - Key Workflows — one page per major workflow, written in numbered SOP style
- Operations/
  - Running Locally — concrete copy-pasteable steps
  - Environment Variables — full table
  - Deployment — where it lives, how it gets there
- Integrations/ — one page per major external service, only if there are integrations
- Reference/ — API endpoints, schema, configuration, only if needed

## The commands

The skill exposes six commands. Identify which one applies from the user's request. If unclear, ask.

### `build` — Full pipeline for a new product wiki

This is the main entry point. Use when the user wants to document a product that has no existing wiki.

**The wiki is always a Notion database, never plain nested pages.** This is a hard requirement — databases support gallery and table views, filtering by tags, and proper property schemas. Create the wiki using `notion-create-database` under the specified parent page, then create all wiki pages as database entries inside it.

**Parent page modes**: at the orient step, ask whether the user has an existing Notion page they want to use as the parent, or whether the skill should create a starter parent page. The recommended workflow is for users to design the parent page themselves once they have a baseline to react to, so on the first run the skill creates a starter parent; on subsequent runs (or once the user has a manual parent page) the skill should accept the existing parent and only create sub-pages underneath. See `notion-structures.md` for both modes.

Workflow:
1. **Orient** — confirm:
   - The project (repo path)
   - The product name
   - **Parent page mode**: ask "Do you have an existing Notion page you want to use as the wiki parent, or should I create a starter parent page that you can redesign later?" If existing, get the page URL or ID. If new, get the parent location (which company sub-tree, etc.).
2. **Scan** — analyse the codebase per `scan-checklist.md`. Produce a scan report summarising tech stack, dependencies, integrations, env vars, routes, schema, scripts, deployment hints. Show this to the user.
3. **Pause 1** — "here's what I found, anything missing?" Wait for input.
4. **Propose structure** — based on scan, propose the page tree. Include only categories that make sense for this product. Show the user.
5. **Pause 2** — "here's the structure, any changes?" Wait for input.
6. **Draft verifiable sections** — write the content for pages that can be sourced from code (Tech Stack, Env Vars, Integration pages, Reference pages). Mark sections as `[code-verified]` internally.
7. **Interview** — for each page that needs user input, ask targeted questions per `question-bank.md`. Batch questions (3-7 at a time). Save progress between batches in case the user steps away.
8. **Pause 3** — show full structure with all content filled in. "Ready to create these in Notion as drafts?"
9. **Write to Notion** — follow the sequence below. Do not proceed to the next step until the current one succeeds.
   1. **Create the database** under the parent using `notion-create-database`. Schema must include: Name (title), Owner (person), Verification (status), Tags (multi-select). Give the database an emoji icon and cover image.
   2. **Create all first-level section pages** as database entries (using `data_source_id` from the fetch result). The standard set is: Overview, Getting Started, Architecture, Operations, Integrations (if applicable), Reference (if applicable), Changelog, Decision Log. Tag each with `"Top Level"` in addition to their content tags — this is what the gallery view filter uses.
   3. **Create sub-pages** under each section page using `page_id` parent (not data_source_id — sub-pages are plain pages, not database entries). Key sub-pages:
      - Under Architecture: System Overview, Data Model, Key Workflows
      - Under Key Workflows: one page per major workflow (e.g. Candidate Hiring Flow, Role Creation, etc.) — do NOT leave Key Workflows as a leaf page with no children
      - Under Operations: Running Locally, Environment Variables, Deployment
      - Under each Integrations section page: one sub-page per external service
   4. **Create views** on the database after all pages exist:
      - `notion-create-view` type `gallery`, name `"Home"`, configure `FILTER "Tags" = "Top Level"` — this is the landing view showing only section pages
      - `notion-create-view` type `table`, name `"All Pages"` — shows everything
   5. Set properties on all pages: Verification = Empty, Owner = user, Tags = appropriate values, cover image, emoji icon.
10. **Confirm** — tell the user where the drafts live, how many pages were created, what's in any open-questions sections, and (if a starter parent was created) explicitly invite them to redesign it manually. Note: `notion-delete-page` is not available via MCP — if there's an old plain-page hierarchy to remove, tell the user to archive it manually in Notion.

### `update` — Refresh an existing wiki against the current codebase

Use when an existing wiki may have drifted from the code.

Workflow:
1. **Locate** — find the existing wiki in Notion (ask user for parent page if not given).
2. **Read existing wiki** — fetch all pages and their content.
3. **Scan codebase** — same as build (per `scan-checklist.md`).
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
1. **Run the codebase scan** per `scan-checklist.md`.
2. **Produce scan report**. Show the user.
3. **Stop.** No structure proposal, no interview, no Notion writes.

## Working with Notion via MCP

The skill uses the Notion MCP to read and write pages. Key operations:

- **Find parent page** — search for the product/company parent page where the wiki should live
- **Create page** — with title, icon (emoji), cover, properties, and body blocks
- **Update page** — modify properties or body blocks
- **Append blocks** — add to an existing page (used heavily for changelog and decision log)
- **Create relations** — link pages to each other and to the company database

When creating pages, always:
- Set Verification to Empty (drafts)
- Set Owner to the user (or ask if uncertain)
- Apply Tags appropriately ("Technical" for dev-docs pages, "Product" for product layer, "Overview" for overview pages, etc.)
- Add cover image and emoji icon
- Link to the parent page via relation if a relations property exists

For complex pages with many block types (callouts, tables, headings, code blocks), build the block structure programmatically and submit in batches to avoid Notion API limits.

## Page composition

For each page type, follow the structure in the corresponding template file at the skill root:

- `overview-page.md` — Product layer overview page
- `strategy-page.md` — Strategy page (use sparingly)
- `changelog-page.md` — Changelog page structure
- `decision-log-page.md` — Decision log page structure
- `decision-log-entry.md` — Format for individual decision entries
- `getting-started-page.md` — Getting Started page
- `architecture-overview-page.md` — Architecture system overview
- `data-model-page.md` — Data model page
- `workflow-page.md` — Key workflow page (uses SOP-style numbered structure)
- `running-locally-page.md` — Running Locally page
- `env-vars-page.md` — Environment Variables page
- `deployment-page.md` — Deployment page
- `integration-page.md` — Per-integration page
- `reference-page.md` — Generic reference page (API, schema)

Each template specifies the section structure, the appropriate Notion blocks, and where content comes from (code-verified vs user-stated).

## Anti-hallucination checklist (run before writing to Notion)

Before submitting any pages to Notion, verify:

- [ ] Every dependency mentioned exists in a real manifest file
- [ ] Every env var mentioned is actually referenced in code
- [ ] Every integration mentioned has a real SDK import or API call
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

- `scan-checklist.md` — Detailed codebase scanning checklist (load when running `scan`, `build`, or `update`)
- `question-bank.md` — Question patterns organised by page type (load before interview phase)
- `notion-structures.md` — How to translate content into Notion block structures, including how to build properties, tables, callouts, toggles, and relations via MCP (load when writing to Notion)
- Page templates (all at skill root): `overview-page.md`, `getting-started-page.md`, `architecture-overview-page.md`, `data-model-page.md`, `workflow-page.md`, `running-locally-page.md`, `env-vars-page.md`, `deployment-page.md`, `integration-page.md`, `reference-page.md`, `changelog-page.md`, `decision-log-page.md`, `decision-log-entry.md`, `strategy-page.md`
