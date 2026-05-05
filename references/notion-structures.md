# Notion Structures

This reference covers how to assemble the wiki parent page and its sub-pages via the Notion MCP. Read this before any write-to-Notion step in `build` or `update`.

---

## Home page shape

The home page is a regular Notion page — NOT the database itself. It is the URL you share with users. The database lives inside it as a child (visible in the sidebar under the home page).

Structure (top to bottom):

1. **Cover image** — full-width banner relevant to the product
2. **Emoji icon** — topical to the product
3. **Title** — the product name
4. **Callout block** — 💡 icon, `gray_background`, product one-liner (what it does and for whom)
5. **Status paragraph** — bold, `🟢 Status: [live status description]`. 🟢 live, 🟡 beta/in-progress, 🔴 down/deprecated.
6. **Divider**
7. **Heading 2** — "Wiki sections"
8. **Paragraph** — "Jump to any section, or open the gallery to browse all pages."
9. **Bulleted list** — one item per top-level wiki page, each a real Notion page-mention link (added in step 9g once page IDs exist — use a placeholder first pass if needed)

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
    - callout block (💡, gray_background, product one-liner)
    - status paragraph (bold, 🟢/🟡/🔴 Status: ...)
    - divider
    - heading_2: "Wiki sections"
    - paragraph: "Jump to any section, or open the gallery to browse all pages."
    - bulleted_list_item × N (placeholder text for now — real mentions added in step 7)
```

**Step 2 — Create the database inside the home page**

```
notion-create-database:
  parent: { page_id: <home_page_id> }
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

View 2: notion-create-view
  name: "All Pages"
  type: table
  display_properties: [Page, Owner, Tags, Verification, Last edited time]
```

**Step 4 — Create all top-level pages inside the database**

`parent.database_id = <database_id>`. Order: Start Here → Overview → Architecture → How To's → Integrations → Reference → Changelog → Decision Log → Strategy (if applicable). Every page gets cover image, emoji icon, Verification = Empty, Owner, Tags.

**Step 5 — Create sub-pages as children of their section pages**

`parent.page_id = <section_page_id>` (not the database). Do this after section pages exist.

**Step 6 — Update section pages and Start Here with real page-mention links**

Now that sub-pages have IDs, update (or append blocks to) section landing pages and the Start Here reading order list with real page-mention blocks. Plain text is not acceptable — use the page mention block format below.

**Step 7 — Update home page navigation list with real page-mention links**

Replace the placeholder bullet items in the home page "Wiki sections" list with real page-mention links to each top-level page.

### User-provided parent

If the user provides an existing Notion page as the parent location, use that page's ID as the parent for step 1 (the home page). Do NOT create the database directly inside the user's page — always create the home page first, then nest the database inside it.

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
