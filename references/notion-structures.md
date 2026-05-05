# Notion Structures

This reference covers how to assemble the wiki parent page and its sub-pages via the Notion MCP. Read this before any write-to-Notion step in `build` or `update`.

---

## Parent page shape

Every wiki home page looks like this (top to bottom):

1. **Cover image** — full-width banner relevant to the product
2. **Emoji icon** — topical to the product
3. **Title** — the product name
4. **Callout block** — 💡 icon, default background, contains the product one-liner description (what it does and for whom, one or two sentences)
5. **Status paragraph** — bold text, format: `🟢 Status: [present-tense description of live status]`. Use 🟢 for live/healthy, 🟡 for in-progress/degraded, 🔴 for down/deprecated. Source the status text from the user during the interview.
6. **Inline database** — the gallery of sub-pages (created as a child of this page via `notion-create-database`)

---

## Order of operations for `build`

### Starter parent (skill creates the parent)

1. `notion-create-pages` — create the parent page with:
   - `parent`: the user's chosen location (workspace or parent page ID)
   - `title`: product name
   - `icon`: topical emoji
   - `cover`: relevant Notion gallery or Unsplash image
   - `children`: callout block + status paragraph (see block shapes below)

2. `notion-create-database` — create the sub-page database:
   - `parent`: `{ type: "page_id", page_id: <parent_page_id> }`
   - `title`: `"Pages"`
   - `properties`: standard schema (see below)

3. `notion-create-view` — set the gallery view:
   - `database_id`: the newly created database ID
   - `type`: `"gallery"`
   - `name`: `"Gallery view"`
   - `format.card_preview`: `"page_cover"`

4. `notion-create-pages` (one call per sub-page, or batched) — create each sub-page:
   - `parent`: `{ type: "database_id", database_id: <database_id> }`
   - Properties, icon, cover, body blocks per sub-page spec below

### User-provided parent (user already has a parent page)

Do NOT touch the existing parent page content or properties. Start at step 2 above — create the database as a child of the user's page, set gallery view, then create sub-pages inside the database.

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
        { "name": "Overview" },
        { "name": "How To" },
        { "name": "How We Work" },
        { "name": "SOP" },
        { "name": "Technical" },
        { "name": "Product" }
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

### Standard emoji by page type

| Page              | Emoji |
|-------------------|-------|
| Start Here        | 👋    |
| Overview          | 📋    |
| Changelog         | 📝    |
| Decision Log      | 🗂️   |
| Strategy          | 🗺️   |
| Architecture      | 🏗️   |
| How To's          | 🤷    |
| Getting Started   | 🚀    |
| Running Locally   | 💻    |
| Deployment        | ☁️   |
| Integrations      | 🔌    |
| Data Model        | 🗄️   |
| System Overview   | 🗂️   |
| Key Workflows     | 🔄    |
| Reference         | 📖    |
| Env Vars          | 🔑    |

---

## Updating an existing wiki (`update` command)

When updating, the database and gallery view already exist. Do not recreate them. Use `notion-update-page` to modify sub-page content and properties. When a page is modified, reset its Verification to Empty so the user knows to re-review it.

If new sub-pages are needed (e.g. a new integration was found), create them as database entries using the same pattern — parent = database ID.
