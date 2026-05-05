# claude-product-wiki

A [Claude Code](https://claude.ai/code) skill for building and maintaining comprehensive technical product documentation in Notion.

Given a codebase, it scans the code, interviews you for context it can't derive, proposes a structured wiki, and writes everything to Notion as draft pages — ready for you to review before publishing.

## What it does

- **`build`** — Full pipeline for a new product wiki: scan codebase → propose structure → interview → write to Notion as drafts
- **`update`** — Refresh an existing wiki against the current codebase, diffing what's stale or missing
- **`add-page`** — Add a single page to an existing wiki (new integration, new workflow, etc.)
- **`log-decision`** — Append a decision log entry in under a minute
- **`add-changelog`** — Append a changelog entry
- **`scan`** — Analyse a codebase and report what's there, without writing anything

The wiki is always a **Notion database** (not plain pages), with gallery and table views, a standard properties schema (Owner, Verification, Tags), cover images, and emoji icons on every page. New pages are created with `Verification = Empty` so you review them before promotion.

### Wiki structure

```
Product layer
├── Overview
├── Strategy (if applicable)
└── Changelog

Product Reference
└── Decision Log

Technical layer
├── Getting Started
├── Architecture/
│   ├── System Overview
│   ├── Data Model
│   └── Key Workflows/
├── Operations/
│   ├── Running Locally
│   ├── Environment Variables
│   └── Deployment
├── Integrations/ (one page per external service)
└── Reference/ (API, schema — if needed)
```

## Requirements

- [Claude Code](https://claude.ai/code) with the Notion MCP configured
- A Notion workspace where you have permission to create pages

## Installation

Clone this repo into your Claude skills directory:

```bash
# Personal install (available in all projects)
git clone https://github.com/tobyking77/claude-product-wiki ~/.claude/skills/product-wiki

# Or project-only install (available in this project only)
git clone https://github.com/tobyking77/claude-product-wiki .claude/skills/product-wiki
```

Claude Code auto-discovers skills — no restart needed.

## Usage

Open Claude Code in a project directory and invoke the skill:

```
/product-wiki build
```

Or just describe what you want — Claude will recognise the intent:

> "build a wiki for this repo"
> "log a decision"
> "add a changelog entry for the auth refactor"
> "scan this codebase"

### First run

When you run `build`, the skill will:

1. Ask which Notion page to use as the parent (or offer to create one)
2. Scan your codebase and show you what it found
3. Propose a page structure — you approve or adjust
4. Interview you for context it can't get from code (owner, deployment env, etc.)
5. Show you a full preview before writing anything
6. Write all pages to Notion as drafts

You review the drafts in Notion and set `Verification` to your confirmed state when satisfied.

## File reference

| File | Purpose |
|------|---------|
| `SKILL.md` | Main skill definition — commands, principles, workflow |
| `scan-checklist.md` | What to extract from a codebase during scan |
| `question-bank.md` | Interview question patterns by page type |
| `notion-structures.md` | How to build Notion block structures via MCP |
| `*-page.md` | Page templates (one per wiki page type) |

## Principles

- **Zero hallucination** — every claim traces to code, user input, or is explicitly marked as inferred
- **Draft-first** — nothing goes live without your review; `Verification` stays empty until you promote it
- **Short focused pages** — 200–600 words each; split rather than mega-pages
- **Pause at three points** — after scan, after structure proposal, after interview — never skips these

## Updating

```bash
cd ~/.claude/skills/product-wiki && git pull
```
