# Notion Structures

How to translate content into Notion blocks via MCP. Load this when writing to Notion.

## Property schema

Every page in a product wiki has these properties (matching the firm's existing schema):

| Property | Type | Notes |
|---|---|---|
| Name | Title | Page title |
| Owner | Person | Single person responsible. Default to current user; ask if uncertain. |
| Verification | Status / Select | **Empty = Draft**. Confirmed states (Verified / Approved / Reviewed) — do NOT set these; that's the user's signal. |
| Tags | Multi-select | Use existing vocabulary: Overview, How To, How We Work, SOP, Technical, Product, plus departmental tags (Finance, Sales & Marketing, Product, Property) where they apply. |
| Last edited time | Auto | Notion handles |
| Created time | Auto | Notion handles |

Optional properties to use if the parent database has them:
- **Stack** (Multi-select) — tech stack tags on the Overview page
- **Integrations** (Multi-select) — integration tags on the Overview page
- **Status** (Select) — Active / Maintenance / Deprecated / Planning
- **Stage** (Select) — Production / Beta / Alpha / Internal only
- **Repo** (URL)
- **Live URL** (URL)
- **Depends on** (Relation, self) — other systems this needs
- **Used by** (Relation, self) — other systems that need this
- **Company** (Relation) — link to portfolio company

Don't fail if optional properties don't exist on the parent database. Just skip them.

## Cover images and icons

- **Cover image**: every page should have one. Use Notion's gallery defaults or Unsplash via the API. Pick something topical to the page subject. Examples:
  - Overview: a screenshot or product hero image if the user provides one, else an abstract gallery image
  - Architecture: technical/abstract imagery
  - Integrations: imagery related to the service (or a clean abstract)
  - Workflows: imagery related to the workflow's domain
- **Emoji icon**: every page gets one. Pick something topical:
  - Overview: 📖 or product-themed
  - Strategy: 🎯
  - Changelog: 📜
  - Decision Log: 🧭
  - Getting Started: 🚀
  - Architecture: 🏗️
  - Data Model: 🗂️
  - Workflow pages: ⚙️ or domain-specific
  - Running Locally: 💻
  - Environment Variables: 🔐
  - Deployment: 🚢
  - Integration pages: 🔌 or service logo where Notion supports it
  - Reference: 📚

## Block patterns

### Page header pattern (every page)

After the title and properties, the body of every page starts with:

1. **One short paragraph** (1-2 sentences) introducing what this page covers. Plain prose, not a callout.
2. **Table of contents block** (`/table_of_contents`) — add this on any page longer than ~300 words. Notion auto-generates it from the page's H1/H2/H3 structure. This is the closest Notion comes to in-page sidebar navigation; cheap to add, big readability gain. Skip only on very short pages (Overview, Decision Log header).
3. **At-a-glance callout** for standalone facts that warrant visual weight (Status, Owner, Stage, Last updated). Use a single callout block with these inside as bold-prefix lines, not separate H2 sections. See "Callouts" below for format.

Then the page-type-specific content.

**Anti-pattern to avoid**: do not create a top-level H2 section for a single-fact piece of metadata (e.g. "## Status" followed by one line "Live — production"). That fact belongs in the at-a-glance callout or in the page properties, not in a heading. Reserve H2 for sections with substantive content.

### Procedural content (workflows, SOPs, Running Locally, Getting Started)

Use Notion's numbered list with multiple levels:

```
1. Top-level step
   1.1 Sub-step (use child numbered list)
   1.2 Sub-step
2. Next top-level step
```

Notion handles the numbering visually. You author the structure as parent + child numbered list blocks.

For each step:
- **Action verb first** ("Run", "Open", "Configure")
- **Concrete details inline** (commands in code blocks, file paths in inline code, URLs as links)
- **Callouts within steps** for warnings or "if X then Y" branches

### Reference content (env vars, integrations, dependencies, routes)

There are two table options in Notion and the choice matters:

**Inline database** (`/database - inline`) — use this for ANY reference table with 5+ rows where the user might want to sort, filter, or query. This is what Notion is genuinely good at and underusing it makes the wiki feel like rendered Markdown rather than native Notion. Inline databases let readers sort env vars by environment, filter integrations by status, group routes by resource. Set proper column types (Select for required/environment, URL for "where used" file links, etc.) — don't dump everything as plain text.

**Simple table block** (`/table`) — use only for small at-a-glance tables (3-5 rows max, e.g. the Stack table on Overview).

Default to inline database for:
- **Env vars** (columns: Name [title] | Purpose [text] | Required [select: Yes/No/Conditional] | Environment [multi-select] | Where used [URL])
- **Integrations** (columns: Service [title] | Purpose [text] | Auth Method [select] | Env Vars [text] | Owner [text])
- **Dependencies** (columns: Package [title] | Version [text] | Purpose [text] | Type [select: runtime/dev])
- **Routes** (columns: Method [select] | Path [title] | Handler [URL] | Auth Required [select])
- **Runnable functions / commands** if there are 5+
- **Tabs/components/modules** if there are 5+

If a list has 30+ items and most readers don't need it, wrap the inline database in a toggle so it doesn't dominate the page.

### Code and commands

Always use Notion's code block with the appropriate language tag:
- `bash` for shell commands
- `typescript` / `javascript` / `python` / `go` etc. for code
- `json` for config
- `sql` for schema

Inline code (single backticks in markdown → Notion's inline code) for file paths, env var names, function names.

### Diagrams

For architecture diagrams:
1. **Mermaid** — Notion supports Mermaid in code blocks with language `mermaid`. Use for flowcharts, sequence diagrams, ERDs. Best default because it's editable as text.
2. **Embedded Excalidraw / Figma** — for hand-drawn or richer diagrams, embed via URL. Tell the user where the source lives.
3. **Static image** — last resort, only if the diagram already exists as an image.

Always prefer Mermaid where possible — it stays editable, lives in the page, and renders natively in Notion.

**CRITICAL Mermaid rules** (these address bugs from previous output):

1. **Create exactly ONE code block per diagram.** A Notion code block with `language: mermaid` renders the diagram natively. Do NOT additionally create a second code block, image block, or rendered embed of the same diagram. Producing both a "source" block and a "rendered" block creates visible duplication on the page.
2. **Use real newlines for line breaks inside node labels, not the literal string `\n`.** Mermaid in Notion renders `\n` as the literal characters, not a line break. Either:
   - Use actual line breaks (newline character) inside the node label string, or
   - Use `<br/>` for explicit line breaks within labels, or
   - Keep node labels short enough to fit on one line and avoid line breaks entirely (preferred — multi-line node labels often signal the diagram is too dense).
3. **Keep diagrams under 10 nodes** on overview-level pages. If you have more than 10, the diagram is too detailed for the page and either needs decomposing into multiple smaller diagrams or moving to a sub-page.
4. **Test Mermaid syntax mentally before submitting.** Common errors: unclosed brackets, mismatched quotes around labels, `flowchart LR/TD/TB/RL` direction tokens (use one of these, not made-up directions).

### Callouts

Use callouts for:
- **ℹ️ Info / scope definitions** at the top of pages
- **⚠️ Warnings / gotchas** in flow
- **💡 Tips / quick notes** for nuance
- **🔒 Security-sensitive** notes
- **At-a-glance metadata** at the top of pages (see pattern below)

**At-a-glance callout pattern**: a single info callout at the top of a page containing the page's standalone metadata facts as bold-labelled lines. Replaces multiple tiny H2 sections that each hold one fact.

Format inside the callout:
```
**Status:** Live (production)
**Owner:** Toby King
**Stage:** v1 — S&D only
**Last updated:** 2026-05-12
```

Use this on Overview pages especially, but also on any page where there are 2-4 standalone facts that don't deserve their own sections.

Don't use callouts for general body content — they lose their signal value if overused. The test: if a callout has more than ~3 sentences in it, it should probably be regular paragraphs.

### Cross-references and page mentions

When referring to another page in the wiki, use a **page mention block** (`@page-name` in editor, `mention` block type via API) — NOT plain text with a link, NOT just the page title in bold.

Page mentions show the icon and title inline and are visually consistent with how Notion handles cross-references everywhere else. They also stay valid if the target page is renamed or moved.

Examples:
- **Wrong**: "See the [Environment Variables](url) page for the full list."
- **Wrong**: "See the **Environment Variables** page for the full list."
- **Right**: "See @Environment Variables for the full list." (rendered as inline page mention with icon)

For "what to do next" sections at the end of pages, use a vertical list of page mentions rather than a paragraph of links.

### Dividers

Use Notion's divider block (`/divider`) to visually separate major sections on a page when headings alone aren't enough — particularly between dense content blocks. Don't overuse; one or two per page max.

### Toggles

Use sparingly. Only for:
- Genuinely optional reference content (full env var dump when it's huge)
- Advanced topics most readers can skip
- Old / archived content

Never wrap workflow steps or critical content in toggles.

## Page composition workflow

When composing a page, build the block structure in this order:

1. Determine the page properties (title, owner, verification=empty, tags, icon, cover)
2. Build the body as an ordered list of block specifications
3. For long pages, batch block creation in groups of ~50 to stay within Notion API limits
4. Set relations after page creation (relations require the page to exist first)
5. Verify the page renders as expected

## Parent (index) page composition

**Important context**: the parent page is the hardest thing to get right via MCP and is also a one-time visual design exercise per product. The user is expected to design the parent page themselves once they have an initial version to react to. The skill's job for the parent page is to produce a serviceable starter that the user can then redesign manually — not to nail the final layout.

The skill should also support a mode where the user has **already created the parent page manually** and just wants the skill to create sub-pages underneath it. See "Parent page input mode" below.

### Default starter parent page (when the skill creates one)

When the skill is creating the parent page from scratch, produce a minimal serviceable version:

1. **Cover image** and **emoji icon**
2. **Properties**: Name, Owner, Verification, Tags including "Overview" and "Technical"
3. **Body**:
   - One short paragraph (2-3 sentences) describing what the product is, current status, and owner. No more.
   - **Section heading: 📦 Product**
   - Sub-page cards (link_to_page blocks) for: Overview, Changelog, Decision Log, and Strategy if it exists
   - **Section heading: 🛠 Technical**
   - Sub-page cards for: Getting Started
   - **Sub-section heading: Architecture** (just the word, no trailing slash)
   - Sub-page cards for the architecture pages (System Overview, Data Model, Key Workflows)
   - **Sub-section heading: Operations**
   - Sub-page cards for Running Locally, Environment Variables, Deployment
   - **Sub-section heading: Integrations** (only if there are integrations)
   - One sub-page card per integration
   - **Sub-section heading: Reference** (only if there are reference pages)
   - Sub-page cards for any reference pages

**Critical rules**:
- The parent page must NOT contain a prose outline of the wiki structure followed by a separate list of links. That creates redundancy and the prose isn't clickable. The cards ARE the outline.
- Category headings must NEVER use a trailing slash (e.g. "Architecture", not "Architecture/"). The slash is a filesystem convention that has no meaning in Notion.
- Do NOT include the descriptive one-liners as separate prose alongside the cards. If `link_to_page` blocks support subtitles in the current MCP version, use them; otherwise omit subtitles rather than reintroducing the prose.
- After creating the starter parent page, tell the user explicitly: "I've created a starter parent page — feel free to redesign it manually in Notion. The sub-pages will continue to work regardless of how you restructure the parent."

### Parent page input mode (when the user provides one)

The `build` command supports an optional input: an existing Notion page URL or ID to use as the parent. When provided:

1. **Skip parent page creation entirely.** Don't touch the user's parent page — they own its layout.
2. **Validate the parent page exists** and that the skill has write access to create children under it.
3. **Create all sub-pages as children** of the user-provided parent.
4. **Optionally append sub-page mentions** to the bottom of the parent page in a "Pages" section, only if the user explicitly asks. Default is to leave the parent untouched and let the user add navigation themselves.
5. **Confirm to the user**: "Sub-pages created under your parent page. The parent page itself was not modified."

This mode is the recommended workflow once the user has built a landing page they like. It separates the visual design problem (their job) from the content generation problem (the skill's job).

## Relations to set up

After all pages are created:

1. **Link sub-pages to parent** — if the parent database has a `Used by` or `Part of` relation
2. **Link integration pages to integration services** — if there's a database of services
3. **Cross-link related pages** — workflow pages reference the systems they use, integration pages reference the workflows that use them

If the schema doesn't support relations between specific pages, use page mentions in the body content instead.

## Error handling

If Notion MCP calls fail:
- **Authentication errors** — surface clearly to user, suggest re-connecting via suggest_connectors
- **Permission errors** — tell user which page lacks permission
- **Rate limits** — back off and retry, batch operations
- **Schema mismatches** (property doesn't exist on database) — skip that property, log it, continue
- **Validation errors** (e.g. invalid emoji, invalid URL) — fix and retry, or skip with a note

Never write a partial wiki and walk away. Either complete the write or roll back what was created so the user has a clean state to retry from.
