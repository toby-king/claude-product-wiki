# Question Bank

Question patterns organised by page type. These are templates — adapt to what you actually found in the scan. Specific questions grounded in the code beat generic ones every time.

## General principles

- **Batch questions in groups of 3-7**, grouped by page or section.
- **Ground every question in something specific** you saw in the code.
- **Don't pad** — if the code answered everything for a section, say so and move on.
- **Unknowns are fine** — if the user says "skip" or "I don't know", mark the section as TODO and move on.
- **Numbered format** so the user can answer inline (e.g. "1. Yes, 2. Skip, 3. ...").

## Start Here page

Start Here is mostly assembled from content captured in other sections (Overview, Architecture, How To's). Draft it last so you can pull the best content forward. The only things to ask specifically for it are the quick-reference table values and the FAQ questions.

Ask in one batch, after all other sections are done:

"Almost finished — just a few things for the Start Here page:

1. What's the production app URL? (I'll add it to the quick-reference table)
2. What's the backend/API URL, if it's separate from the app?
3. Who should be listed as owner on this wiki?
4. What are the 2-3 questions someone new to this product would most likely ask? I'll already include 'How do I run it locally?' and 'How do I deploy?' — any others specific to {product}?"

Rules:
- Don't re-ask anything already captured in the Overview or Architecture interview.
- The reading order list is derived from the pages being built — don't ask the user to define it.
- If URLs were mentioned earlier in the interview, don't ask again — use what you have.

## Overview page

Goal: one paragraph describing what the product is, who uses it, and what problem it solves.

Good questions:
- "In one sentence, what is {product} and who uses it?"
- "What problem does it solve that wasn't solved before?"
- "Is this a product, internal tool, research prototype, or something else?"
- "What's the current status — production, beta, alpha, internal-only, archived?"
- "Who else works on this besides you?"

Avoid:
- "What's your mission?" (too corporate)
- "What makes this special?" (leading, generates fluff)

## Strategy page

Only generate this page if the user indicates there's substantive strategy content. If they don't, skip the page entirely.

Good questions (only if strategy page is being created):
- "Who is the target customer in one sentence?"
- "What's the wedge — the specific thing this does better than alternatives?"
- "What are you deliberately NOT doing?" (often more revealing than what you ARE doing)
- "What are the biggest open strategic questions you don't have answers to?"

## Decision Log (initial entry on creation)

When creating the page for the first time, the skill should add one starter entry to demonstrate the format. Ask:
- "Want to seed the decision log with one decision you'd want recorded? Something like the choice of {framework X} or {service Y}, or skip for now."

## Architecture > System Overview

Code tells the *what*. Ask the user the *why* and *shape*.

Good questions (use what's relevant from the scan):
- "I see {framework X} on the frontend and {framework Y} on the backend — was that deliberate, and are they in the same repo or separate?"
- "There's a `services/` directory and a `lib/` directory — what's the intended distinction?"
- "Is there a queueing or async job system? I didn't find one but want to confirm."
- "Are there any architectural conventions a new contributor should know about?"

## Architecture > Data Model

Verifiable structure + user context on meaning.

Good questions:
- "The `{table_name}` table has `{column_name}` — what does this represent?"
- "Is `{enum_value}` still used or legacy?"
- "I see a relationship between `{table_A}` and `{table_B}` through `{join_table}`. Many-to-many as it looks, or something more?"
- "The `{column}` is nullable — under what conditions is it null?"
- "Are any of these tables deliberately cross-tenant? I see {tenant_id} on some but not others."

## Architecture > Key Workflows

Usually the most user-input-heavy section. Each workflow becomes its own page using SOP-style numbered structure.

Good questions per workflow:
- "What are the 3-5 main things this product actually does end-to-end? I'll make a page for each."
- For each named workflow:
  - "Walk me through {workflow} step by step. Start from the trigger, end with the outcome."
  - "Anything non-obvious — retries, side effects, edge cases?"
  - "What happens when it fails?"
  - "Are there variations or branches in the flow?"

The "walk me through the last time you actually did this" technique is essential — it produces concrete narrative rather than idealised description.

## Operations > Running Locally

Mostly user-stated, but you can scan for clues (npm scripts, README setup section, Docker compose).

Good questions:
- "What's the actual sequence of commands to get this running from a fresh clone?"
- "Any prerequisites — specific Node version, Python version, Docker, services that need to be running?"
- "Anything that always trips people up the first time?"
- "Is there seed data, and how do you load it?"

## Operations > Environment Variables

Names come from grep. Ask for context.

Batch all env vars in one question:

"Here are the env vars I found. Quick context on each — what it's for, required vs optional, which environment (local / staging / prod):

- ANTHROPIC_API_KEY
- SUPABASE_URL
- SUPABASE_SERVICE_KEY
- {...}

Also — anything I missed? I didn't find references to a payment provider key, for instance."

## Operations > Deployment

Partly verifiable from config, partly user-stated.

Good questions:
- "Where does this run in production? I see {hints} but want to confirm."
- "Push to main auto-deploys, or is there a manual step?"
- "Are there multiple environments (dev / staging / prod), and how do they differ in config?"
- "Where do production secrets live (Doppler / 1Password / Railway vars / Vercel env)?"
- "What's the rollback story?"

## Integrations > [Per-service]

The integration exists (verified from code). Ask about purpose, ownership, and operational concerns.

Good questions per integration:
- "I can see {service} is integrated. What's it being used for in this product?"
- "Is it in production use or still being tested?"
- "Who owns the {service} account — personal, company, shared?"
- "Any rate limits, quotas, or cost considerations worth flagging?"
- "Is there a fallback if {service} is down, or does the app just error?"

## Costs page

Always ask — this page is always created. The scan will have produced a list of likely paid services; use that list as the basis for the questions.

Open with the list from the scan, then ask in one batch:

"I found the following services that likely have running costs. For each, what plan or tier are you on and roughly what does it cost per month? 'Not sure' or 'free tier' is a fine answer — I'll mark it as Unknown rather than guess.

- {Service A} ({why it likely costs — e.g. 'API usage'})
- {Service B} ({e.g. 'hosting plan'})
- {Service C} (...)

Also — are there any other services, subscriptions, or infrastructure costs not visible in the code? Things like domain registration, DNS, error monitoring, analytics, or team tooling that should be counted here."

Follow-up if totals seem incomplete:
- "Is there anything you pay for on a yearly basis that should be reflected as a monthly figure?"
- "Any services on free tiers you're likely to move to paid soon?"

Rules:
- Never ask about costs for services not detected in the code or mentioned by the user.
- If the user is unsure of a figure, write `Unknown` — don't prompt them to estimate.
- The final total row should reflect only confirmed figures; use `£X + unknowns` if some are unconfirmed.

## Reference pages (API, schema, configuration)

Mostly auto-generated from code. Ask only when something is genuinely ambiguous:
- "I see endpoint `{path}` but the handler is sparse — what's it actually used for?"
- "Is the `{config_option}` setting still in use? It's not referenced anywhere I can see."

## Meta-questions to ask early

Before section-specific batches, a few orienting questions help:

- "Who's the primary audience for this wiki — future you, future contributors, the wider firm, an acquirer?" (shapes tone and depth)
- "Any sections in the proposed structure you don't want? Any I should add?"
- "Is there any part of this product you're planning to rewrite soon? If so I'll flag rather than document heavily."
- "Any existing docs (README, scattered Notion pages, Google Docs) I should pull from before asking you things?"

## Questions to NOT ask

- Anything the code clearly answers
- Leading questions
- Multi-part questions stuffed into one
- Questions whose answer doesn't go into the wiki
- "Do you think the architecture is good?" (not the skill's job)
