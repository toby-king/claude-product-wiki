# Section Landing Page — Template

This template applies to every section page that acts as a parent/container: **Architecture**, **How To's**, **Integrations**, and **Reference**. These pages are what a reader lands on when they click a gallery card. Their job is to orient the reader, explain what the section covers, and link clearly to everything inside it.

**Never create a section page as an empty container.** Even if it only has one sub-page, the section page must have body content that explains the section.

---

## Page properties

| Property     | Value |
|--------------|-------|
| Emoji icon   | Per the emoji table in `notion-structures.md` |
| Tags         | Top Level + the relevant tag (Technical, How To, etc.) |
| Verification | Empty (draft) |
| Cover        | Per the cover table in `notion-structures.md` |

---

## Body structure

### Block 1 — Grey callout (section hook)

One sentence. Plain language. Tells the reader exactly what this section is.

```
ℹ️  [One sentence describing the section with no jargon.]
```

Icon: `ℹ️`, colour: `gray_background`.

**Examples by section:**
- Architecture: *"This section documents how [Product] is built: its services, data model, and key workflows."*
- How To's: *"Practical guides for the tasks you'll do most often as a developer working on [Product]."*
- Integrations: *"This section documents every external service [Product] depends on and how they're connected."*
- Reference: *"Lookup material for configuration, environment variables, and API endpoints."*

---

### Block 2 — Purpose paragraph

2–3 sentences. Expands on the callout. Explains *why* this section exists and what type of reader will find it useful.

> *This section covers the structural decisions behind [Product]: how the services are divided, what lives in the database, and how the main user journeys flow through the system. It's the right place to start if you're trying to understand the codebase as a whole before diving into a specific area.*

---

### Block 3 — Bullet list: what you'll find here

3–5 bullets. Each one is a specific thing the reader will find in this section. Concrete, not vague.

Good:
- How the frontend, backend, and database relate to each other
- The schema for the five core database tables
- Step-by-step breakdown of the video interview flow

Bad:
- Technical documentation
- Information about the system
- Various pages

---

### Block 4 — Sub-page links

One or more groups of sub-pages, each group preceded by a `## Heading` and a brief description sentence.

If all sub-pages are in one logical group, a single `## Sub-pages` heading is fine. If sub-pages split into clear groups (e.g. Architecture has "Structure" pages and "Workflow" pages), use multiple headings.

After each heading, write 1 sentence explaining what the pages in this group are for, then list the sub-pages as embedded Notion page mentions (one per line).

**Format:**

```
## [Group heading]

[One sentence describing what these pages cover.]

@[Sub-page 1]
@[Sub-page 2]
@[Sub-page 3]
```

---

## Section-specific examples

### Architecture

```
ℹ️  This section documents how [Product] is built: its services,
    data model, and key workflows.

[Product] is split across [N] repos sharing a single Supabase project.
This section gives you the full picture — from how services communicate
to how data is structured and how the core user journeys flow
through the system.

What you'll find here:
• How the frontend, backend, and database fit together
• The key database entities and their relationships
• Step-by-step breakdowns of the main product flows

## Structure

These pages cover how the system is organised and what each part does.

@System Overview
@Data Model

## Workflows

Each page walks through one major product flow end to end.

@Key Workflows
```

---

### How To's

```
ℹ️  Practical guides for the tasks you'll do most often as a
    developer working on [Product].

These guides cover setup, local development, and deployment.
They're written to be followed sequentially the first time,
and used as reference on subsequent visits.

What you'll find here:
• How to clone the repo and get a local environment running
• How the local dev environment maps to production
• How to deploy a new version

## Guides

@Getting Started
@Running Locally
@Deployment
```

---

### Integrations

```
ℹ️  This section documents every external service [Product]
    connects to and how those connections work.

For each integration, you'll find: what the service does in the
context of [Product], how it's configured, what credentials are
needed, and any known gotchas.

What you'll find here:
• The role each external service plays in the system
• Configuration and credential requirements
• Webhooks, callbacks, and data flow between services

## Services

@[Integration 1]
@[Integration 2]
@[Integration 3]
```

---

### Reference

```
ℹ️  Lookup material for costs, configuration, environment variables,
    and API endpoints.

Use these pages when you need to check a specific value, understand
a config option, or trace an API contract. They're kept close to
the code — if something changes in the codebase, update it here.

What you'll find here:
• All environment variables with descriptions and whether they're required
• Public-facing API endpoint signatures
• Any configuration schemas the system depends on

## Configuration

@Environment Variables
@Costs

## API

@API Endpoints
```

## Source notes

- **Callout, purpose paragraph, bullet list**: written by the skill based on what sub-pages exist — no user input needed
- **Sub-page links**: real page-mention blocks added in step 9f once sub-pages have IDs

> **`@[Page]` notation**: in this template `@Page Name` is shorthand for a Notion page-mention block. When writing via the MCP, use the JSON format in `references/notion-structures.md` (Page mention in a bulleted list section) — not a plain URL or text link.
