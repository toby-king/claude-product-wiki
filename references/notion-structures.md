# Notion Structures

This reference covers how to assemble the wiki parent page and its sub-pages via the Notion MCP. Read this before any write-to-Notion step in `build` or `update`.

---

## Home page shape

The home page is a regular Notion page — NOT the database itself. It is the URL you share with users. The database is a sibling page (same parent, visible in the sidebar alongside the home page).

Structure (top to bottom):

1. **Cover image** — full-width banner relevant to the product
2. **Emoji icon** — topical to the product
3. **Title** — the product name
4. **Status paragraph** — bold, `🟢 Status: [live status description]`. 🟢 live, 🟡 beta/in-progress, 🔴 down/deprecated.
5. **Callout block** — 💡 icon, `gray_background`, product one-liner (what it does and for whom)
6. **Divider**
7. **Two-column layout** (column_list block):
   - **Left column** (~25% width): Heading 2 "Wiki sections" + short description paragraph + bulleted list of page-mention links to every top-level page (added in step 9g once page IDs exist — use placeholder text on first pass)
   - **Right column** (~75% width): Inline gallery view of the database, grouped by Tags, cover images, filtered to `Top Level` pages

> **Column width ratio**: Notion's public API always creates equal-width columns. After the build completes, drag the column divider in the Notion UI to set the ~25/75 split.

---

## Order of operations for `build`

**Step 1 — Create the home page**

Always do this first, regardless of whether the user provided a parent location or not.

```
notion-create-pages:
  parent: <user's chosen location — workspace or page ID>
  title: <product name>
  icon: <topical emoji>
  cover: <relevant image>
  children:
    - status paragraph (bold, 🟢/🟡/🔴 Status: ...)
    - callout block (💡, gray_background, product one-liner)
    - divider
    - column_list:
        - column (left):
            - heading_2: "Wiki sections"
            - paragraph: "Jump directly to a section, or browse everything in the gallery below."
            - bulleted_list_item × N (placeholder text for now — real mentions added in step 7)
        - column (right):
            - [leave empty — the inline gallery view is placed here in step 3b]
```

**Step 2 — Create the database at the same level as the home page**

The database must be created under the **same parent as the home page** — NOT inside the home page. If you nest the database inside the home page, it appears as a closed database block in the page body AND the inline gallery also appears, causing a confusing duplicate. Keep them as siblings.

```
notion-create-database:
  parent: { page_id: <user_chosen_parent_id> }   ← same parent as the home page
  title: <product name>
  properties: <standard schema below>
```

**Step 3 — Create views (order matters — first view = default)**

Create the Home gallery view FIRST so it becomes the default:

```
View 1 (default): notion-create-view
  name: "Home"
  type: gallery
  filter: Tags contains "Top Level"
  card_preview: page_cover
  display_properties: [Page]
  group_by: Tags (multi_select, hide_empty_groups: true, sort: manual)

View 2: notion-create-view
  name: "All Pages"
  type: table
  display_properties: [Page, Owner, Tags, Verification, Last edited time]
```

**Step 3b — Embed the gallery inline in the right column of the home page**

After creating the views, embed a gallery inline on the home page. This goes into the right column of the column_list created in step 1.

```
notion-create-view:
  parent_page_id: <home_page_id>
  data_source_id: <database_id>
  name: "Home"
  type: gallery
  filter: Tags contains "Top Level"
  card_preview: page_cover
  group_by: Tags (multi_select, hide_empty_groups: true, sort: manual)
  display_properties: [Page]
```

This creates an inline gallery block on the home page. Notion will place it at the bottom of the page — after the build completes, drag it into the right column in the Notion UI. Because the database is a sibling (not a child), there is no duplicate database block in the body.

**Step 4 — Create all top-level pages inside the database**

`parent.database_id = <database_id>`. Order: Start Here → Overview → Architecture → How To's → Integrations → Reference → Changelog → Decision Log → Strategy (if applicable). Every page gets cover image, emoji icon, Verification = Empty, Owner, Tags.

**Step 5 — Create sub-pages as children of their section pages**

`parent.page_id = <section_page_id>` (not the database). Do this after section pages exist.

**Step 6 — Update section pages and Start Here with real page-mention links**

Now that sub-pages have IDs, update (or append blocks to) section landing pages and the Start Here reading order list with real page-mention blocks. Plain text is not acceptable — use the page mention block format below.

**Step 7 — Update home page navigation list with real page-mention links**

Replace the placeholder bullet items in the home page "Wiki sections" list with real page-mention links to each top-level page.

### User-provided parent

If the user provides an existing Notion page as the parent location, use that page's ID as the parent for step 1 (the home page). Do NOT skip the home page and create the database directly as a child of the user's page — always create the home page first (as a child of the user's page), then create the database as a sibling to the home page (also a child of the user's page, NOT nested inside the home page).

---

## Section landing page body

Section pages (Architecture, How To's, Integrations, Reference) must be created with this structure as their body. Never create them empty.

```
[1] grey callout — one sentence describing the section
[2] paragraph — purpose paragraph (2–3 sentences)
[3] bulleted_list_item × 3–5 — what a reader will find here
[4] heading_2 — "Sub-pages" (or a specific group name if splitting into groups)
[5] paragraph — brief intro for the sub-page group
[6] child_page links — one per sub-page (created as actual child pages, linked via page mention)
```

**Callout for section pages** — use `gray_background` and `ℹ️` icon:

```json
{
  "type": "callout",
  "callout": {
    "icon": { "type": "emoji", "emoji": "ℹ️" },
    "color": "gray_background",
    "rich_text": [
      { "type": "text", "text": { "content": "<one sentence describing the section>" } }
    ]
  }
}
```

**Bulleted list item:**

```json
{
  "type": "bulleted_list_item",
  "bulleted_list_item": {
    "rich_text": [
      { "type": "text", "text": { "content": "<what the reader finds here>" } }
    ]
  }
}
```

---

## Start Here page body

The Start Here page must be created with this exact structure. It is the entry point for anyone new to the product.

```
[1] grey callout — orientation sentence ("New to [Product]? Start here.")
[2] paragraph — what this wiki covers and who it's for (2 sentences)
[3] heading_2 — "If you're new, read in this order"
[4] numbered_list_item × 3–5 — each item is a page link + one-liner
[5] heading_2 — "Quick reference"
[6] table — owner, app URL, API URL, status, stack (key facts at a glance)
[7] heading_2 — "Common questions"
[8] toggle × 3–5 — FAQ items (question as toggle label, answer as child paragraph)
```

**Orientation callout for Start Here** — use `blue_background` and 👋:

```json
{
  "type": "callout",
  "callout": {
    "icon": { "type": "emoji", "emoji": "👋" },
    "color": "blue_background",
    "rich_text": [
      { "type": "text", "text": { "content": "New to [Product]? This page is your starting point. Read the pages below in order for the fastest orientation." } }
    ]
  }
}
```

**Numbered list item with page mention:**

```json
{
  "type": "numbered_list_item",
  "numbered_list_item": {
    "rich_text": [
      {
        "type": "mention",
        "mention": { "type": "page", "page": { "id": "<page-id>" } }
      },
      {
        "type": "text",
        "text": { "content": " — <one-liner describing what reading this page gives you>" }
      }
    ]
  }
}
```

**Toggle block (for FAQ):**

```json
{
  "type": "toggle",
  "toggle": {
    "rich_text": [
      {
        "type": "text",
        "text": { "content": "<question>" },
        "annotations": { "bold": true }
      }
    ],
    "children": [
      {
        "type": "paragraph",
        "paragraph": {
          "rich_text": [
            { "type": "text", "text": { "content": "<answer — or a page mention if the full answer lives elsewhere>" } }
          ]
        }
      }
    ]
  }
}
```

**Quick reference table** (2 columns: Label | Value):

```json
{
  "type": "table",
  "table": {
    "table_width": 2,
    "has_column_header": false,
    "has_row_header": true,
    "children": [
      {
        "type": "table_row",
        "table_row": {
          "cells": [
            [{ "type": "text", "text": { "content": "Owner" }, "annotations": { "bold": true } }],
            [{ "type": "text", "text": { "content": "<owner name>" } }]
          ]
        }
      }
    ]
  }
}
```

---

## Block shapes

> **Callout format warning**: Never use GitHub-style admonitions (`[!NOTE]`, `[!WARNING]`, `[!TIP]`) in Notion content. Notion does not render these as callouts — they appear as literal blockquote text with the `[!WARNING]` string visible to readers. Use only the JSON callout block format below, or a plain blockquote `> emoji content` for simple cases.

### Callout block

```json
{
  "type": "callout",
  "callout": {
    "icon": { "type": "emoji", "emoji": "💡" },
    "color": "default",
    "rich_text": [
      { "type": "text", "text": { "content": "<product one-liner>" } }
    ]
  }
}
```

### Mermaid diagram block

Used on System Overview and Data Model pages. Produces a rendered diagram natively in Notion.

```json
{
  "type": "code",
  "code": {
    "language": "mermaid",
    "rich_text": [
      {
        "type": "text",
        "text": {
          "content": "flowchart LR\n  User[User Browser] --> Frontend[Next.js]\n  Frontend --> API[Express API]\n  API --> DB[(Supabase)]"
        }
      }
    ]
  }
}
```

> **One block only**: produce exactly one mermaid code block per page. Do not also include a plain-text version, image, or second code block of the same diagram — it renders once and duplicates are visible.

> **No `\n` literals in node labels**: use real newlines inside `content` (JSON `\n` escape is fine for line breaks between statements). Keep node labels short enough to avoid wrapping.

### Table block

Used for reference tables (key facts, component lists, env var summaries). `has_row_header: true` makes the first column bold.

```json
{
  "type": "table",
  "table": {
    "table_width": 2,
    "has_column_header": false,
    "has_row_header": true,
    "children": [
      {
        "type": "table_row",
        "table_row": {
          "cells": [
            [{ "type": "text", "text": { "content": "Owner" }, "annotations": { "bold": true } }],
            [{ "type": "text", "text": { "content": "Toby King" } }]
          ]
        }
      },
      {
        "type": "table_row",
        "table_row": {
          "cells": [
            [{ "type": "text", "text": { "content": "Status" }, "annotations": { "bold": true } }],
            [{ "type": "text", "text": { "content": "🟢 Live" } }]
          ]
        }
      }
    ]
  }
}
```

Adjust `table_width` to match the number of columns. Add as many `table_row` children as needed.

### Status paragraph

```json
{
  "type": "paragraph",
  "paragraph": {
    "rich_text": [
      {
        "type": "text",
        "text": { "content": "🟢 Status: <status description>" },
        "annotations": { "bold": true }
      }
    ]
  }
}
```

### Page mention in a bulleted list

In templates, `@[Page Name]` is shorthand notation meaning "insert a Notion page-mention block here". It is not valid Notion API syntax on its own — when writing via MCP, use this block format:

```json
{
  "type": "bulleted_list_item",
  "bulleted_list_item": {
    "rich_text": [
      {
        "type": "mention",
        "mention": { "type": "page", "page": { "id": "<page-id>" } }
      },
      {
        "type": "text",
        "text": { "content": " — <one-liner describing the page>" }
      }
    ]
  }
}
```

Page mentions auto-update their display text if the page is renamed, and navigate on click. Plain text links do not. Always use page mention blocks, never plain URLs, when linking to sibling wiki pages.

### Column layout (two-column block)

Used for the home page body. Left column holds the wiki nav list; right column holds the inline gallery view.

```json
{
  "type": "column_list",
  "column_list": {
    "children": [
      {
        "type": "column",
        "column": {
          "children": [
            {
              "type": "heading_2",
              "heading_2": {
                "rich_text": [{ "type": "text", "text": { "content": "Wiki sections" } }]
              }
            },
            {
              "type": "paragraph",
              "paragraph": {
                "rich_text": [{ "type": "text", "text": { "content": "Jump directly to a section, or browse everything in the gallery below." } }]
              }
            }
          ]
        }
      },
      {
        "type": "column",
        "column": {
          "children": []
        }
      }
    ]
  }
}
```

> **Column width**: Notion's public API creates equal-width columns (50/50). To achieve the ~25/75 split, drag the column divider in Notion after the build completes.

---

## Database properties schema

Create the database with these properties so sub-pages carry the standard metadata:

| Property name   | Type            | Notes                                                                 |
|-----------------|-----------------|-----------------------------------------------------------------------|
| Name            | title           | Required by Notion; holds the page title                             |
| Owner           | people          | Who owns / is responsible for this page                              |
| Verification    | status          | Workflow state; always set to Empty (draft) on creation              |
| Tags            | multi_select    | Pre-populate options: Overview, How To, How We Work, SOP, Technical, Product |
| Created time    | created_time    | Auto-populated                                                        |
| Last edited time| last_edited_time| Auto-populated                                                        |

```json
{
  "Name": { "title": {} },
  "Owner": { "people": {} },
  "Verification": { "status": {} },
  "Tags": {
    "multi_select": {
      "options": [
        { "name": "Top Level" },
        { "name": "Overview" },
        { "name": "How To" },
        { "name": "How We Work" },
        { "name": "SOP" },
        { "name": "Technical" },
        { "name": "Product" },
        { "name": "Reference" }
      ]
    }
  },
  "Created time": { "created_time": {} },
  "Last edited time": { "last_edited_time": {} }
}
```

---

## Sub-page creation

Each sub-page is a database entry. Set `parent.database_id` to the database created above.

### Required fields on every sub-page

- **Title** (the Name property)
- **Icon** — emoji relevant to the page topic
- **Cover** — Notion gallery image relevant to the page topic (see suggestions below)
- **Verification** — leave Empty (draft)
- **Owner** — set to the user
- **Tags** — apply the appropriate tag(s) from the schema

### Cover image themes by page type

The cover is what shows as the gallery card thumbnail — choose something visually distinct per page so the gallery is easy to navigate at a glance. Pick from Notion's built-in gallery or Unsplash via Notion's picker.

| Page              | Theme                                         |
|-------------------|-----------------------------------------------|
| Start Here        | Compass, map, open door, welcome mat          |
| Overview          | Clean abstract, data visualization, dashboard |
| Changelog         | Calendar, planner, timeline                   |
| Decision Log      | Sticky notes, meeting room, planning wall     |
| Strategy          | Horizon, chess board, roadmap                 |
| Architecture      | Server room, data centre, circuit board       |
| How To's          | Toolbox, workshop, step ladder                |
| Getting Started   | Rocket launch, road, open door                |
| Running Locally   | Laptop, terminal, desk setup                  |
| Deployment        | Satellite, launch pad, cloud                  |
| Integrations      | Network cables, connectors, puzzle pieces     |
| Data Model        | Blueprint, schema diagram, grid               |
| Reference         | Library shelves, index cards, filing cabinet  |
| Env Vars          | Key, lock, configuration panel                |
| Costs             | Calculator, invoice, coins, budget spreadsheet |

### Standard emoji by page type

| Page              | Emoji |
|-------------------|-------|
| Start Here        | 👋    |
| Overview          | 📋    |
| Changelog         | 📝    |
| Decision Log      | 🧭    |
| Strategy          | 🗺️   |
| Architecture      | 🏗️   |
| How To's          | 🤷    |
| Getting Started   | 🚀    |
| Running Locally   | 💻    |
| Deployment        | ☁️   |
| Integrations      | 🔌    |
| Data Model        | 🗄️   |
| System Overview   | 🌐    |
| Key Workflows     | 🔄    |
| Reference         | 📖    |
| Env Vars          | 🔑    |
| Costs             | 💰    |

---

## Updating an existing wiki (`update` command)

When updating, the database and gallery view already exist. Do not recreate them. Use `notion-update-page` to modify sub-page content and properties. When a page is modified, reset its Verification to Empty so the user knows to re-review it.

If new sub-pages are needed (e.g. a new integration was found), create them as database entries using the same pattern — parent = database ID.
