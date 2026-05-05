# Start Here — Page Template

The Start Here page is the mandatory entry point to every wiki. It is always the first page created during a `build`. Its job is to orient someone who has never seen the product before — telling them what exists, what to read first, and where to find common answers without hunting.

## Page properties

| Property     | Value |
|--------------|-------|
| Emoji icon   | 👋 |
| Tags         | Top Level, Overview |
| Verification | Empty (draft) |
| Cover        | Compass, open door, or welcome-themed image |

## Body structure

### Block 1 — Blue callout (orientation hook)

```
👋  New to [Product Name]? This page is your starting point.
    Read the pages below in order for the fastest orientation.
```

Icon: `👋`, colour: `blue_background`.

---

### Block 2 — One-liner paragraph

A single sentence explaining what this wiki is and what it covers.

> *This wiki documents [Product Name] — [one sentence: what it does and for whom].*

Source from the Overview page content. Do not repeat the full product description — just enough to confirm the reader is in the right place.

---

### Block 3 — Heading 2: "If you're new, read in this order"

A numbered list with 3–5 entries. Each entry is a page mention followed by an em dash and a one-liner telling the reader exactly what reading that page will give them.

**Order for a typical product wiki:**
1. **[Overview]** — understand what [Product] is, who it's for, and its current status
2. **[System Overview]** (under Architecture) — see how it's built at a high level and how the services connect
3. **[Getting Started]** (under How To's) — clone the repo, install dependencies, and run it locally
4. **[Key Workflows]** (under Architecture) — walk through the main user journeys end to end

Adapt the list to the actual pages that exist. If there's no Key Workflows page, swap in the most important workflow sub-page. If Getting Started and Running Locally are the same page, only link once.

---

### Block 4 — Heading 2: "Quick reference"

A 2-column table (label | value) with no header row. Row headers in bold. Populate from the scan and interview — leave a `<!-- TODO -->` for anything unknown.

| | |
|---|---|
| **Owner** | [Name] |
| **App** | [URL] |
| **API** | [URL] (if separate) |
| **Status** | 🟢 Live / 🟡 Beta / 🔴 Deprecated |
| **Stack** | [Frontend · Backend · Database — one-liner] |
| **Repo(s)** | [Link or name] |

Omit rows that don't apply (e.g. no separate API URL for a pure frontend product).

---

### Block 5 — Heading 2: "Common questions"

3–5 toggle blocks. Each toggle has a bold question as its label and a short answer (or a page mention pointing to the full answer) as its child content.

Choose questions based on the product. Good defaults:

- **What does [Product] do?**
  → Short answer + mention the Overview page for detail.

- **How do I run it locally?**
  → One-line answer + mention the Running Locally page.

- **Where are the environment variables documented?**
  → Mention the Env Vars page (or note they live in `.env.example`).

- **How do I deploy?**
  → One-line answer + mention the Deployment page.

- **Where do I find the key architectural decisions?**
  → Mention the Decision Log page.

Keep answers short — the toggle is for quick orientation, not full documentation. If the full answer is on another page, say so and link it rather than duplicating content.

---

## Example finished page

```
👋 New to Focus? This page is your starting point.
   Read the pages below in order for the fastest orientation.

This wiki documents Focus — an AI-powered recruitment platform for
in-house hiring teams at Venture PE Labs portfolio companies.

## If you're new, read in this order

1. @Overview — understand what Focus is, who it's for, and its current status
2. @System Overview — see how the two repos and Supabase fit together
3. @Getting Started — clone the repos and run Focus locally in under 10 minutes
4. @Video Interview Flow — walk through the most complex candidate-facing journey

## Quick reference

| Owner   | Toby King                                |
| App     | https://focus-crm.vercel.app             |
| API     | https://focus-server-production.up...    |
| Status  | 🟢 Live (internal beta)                  |
| Stack   | Next.js · Express · Supabase · GPT-5     |
| Repos   | focus-app, focus-server                  |

## Common questions

▶ What does Focus do?
  Focus handles the full hiring lifecycle — role creation, CV screening,
  video interviews, AI scoring, and pipeline management. See @Overview.

▶ How do I run it locally?
  Clone both repos, copy .env.example files, run `npm install && npm run dev`
  in each. Full steps at @Running Locally.

▶ Where are the environment variables?
  All env vars with descriptions are at @Environment Variables.

▶ How does Edison work?
  Edison is the AI assistant embedded throughout Focus. See @Edison Agent
  in the Reference section for the full breakdown.

▶ Where are architectural decisions recorded?
  @Decision Log — add an entry whenever a significant choice is made.
```
