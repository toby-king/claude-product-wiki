# Overview — Page Template

The Overview page is the product-layer entry point. It answers "what is this, who is it for, and what state is it in?" for both technical and non-technical readers. It does NOT go deep into implementation — that lives in the Architecture section.

## Page properties

| Property     | Value |
|--------------|-------|
| Emoji icon   | 📋 |
| Tags         | Top Level, Overview, Product |
| Verification | Empty (draft) |
| Cover        | Clean abstract, data visualisation, or dashboard-themed image |

---

## Body structure

### Block 1 — What it is (paragraph or short description)

1–3 sentences. Plain English. What this product does and for whom. No jargon, no implementation detail.

> *[Product] is [what it does] for [who]. It [key differentiator — what makes it notable or different]. [Current deployment context — where it runs, who uses it today.]*

---

### Block 2 — Status callout

A callout showing current live status. Use the traffic-light convention:
- 🟢 Live / 🟡 Beta / Internal testing / 🔴 Deprecated / Paused

```
⚠️  [Status description — one sentence on current state, including any blockers or caveats]
```

Icon: `⚠️` (for caution) or `💡` (for neutral info), colour: `yellow_background` for beta/in-progress, `gray_background` for stable live.

---

### Block 3 — Who it's for

1–3 sentences. Who the product is built for. Be specific: team type, company stage, use case.

---

### Block 4 — Key facts table

2-column table (label | value). Populate from code scan and interview.

| | |
|---|---|
| **Owner** | [Name] |
| **App** | [URL] |
| **API** | [URL] |
| **Frontend** | [Framework, version, key libs] |
| **Backend** | [Framework, runtime, version] |
| **Database** | [Provider and what it handles] |
| **AI / LLM** | [Provider and model(s)] — omit if not applicable |
| **Email** | [Provider] — omit if not applicable |
| **Other key services** | [ATS, job board, etc.] — omit if not applicable |

Only include rows that apply. A pure frontend product doesn't need an API row.

---

### Block 5 — What it does

Bulleted list. 4–8 bullets. Each one is a named feature or capability in bold, followed by a short description. Source from the codebase and user interview.

> - **[Feature name]** — [what it does, one sentence]
> - **[Feature name]** — [what it does, one sentence]

---

### Block 6 — Known gaps (optional)

A bulleted list of significant things the product does NOT yet do. Include only if the user has confirmed these gaps exist. This section is valuable for setting expectations with new readers.

---

### Block 7 — Where to go next

`## Where to go next`

A short numbered list of 3–4 links — the most important pages for a reader who wants to go deeper after reading the Overview. Each link is a page mention followed by an em dash and a one-liner.

> 1. @System Overview — see how the services are structured and how they communicate
> 2. @Getting Started — get a local dev environment running
> 3. @[Key Workflow] — understand [the most important user journey]
> 4. @Decision Log — see the key choices made during development

Choose the 3–4 pages that give the most value in sequence. Do not just list every page — be selective.
