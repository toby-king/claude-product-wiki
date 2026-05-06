# Decision Log Page Template

Chronological record of meaningful product and technical decisions. Uses FAQ-style entry format — each entry reads as an answer to a question someone would ask in a pitch or review.

## Properties

- **Name**: `Decision Log`
- **Owner**: User
- **Verification**: Empty (draft) on creation; entries don't change page-level Verification
- **Tags**: `Product`, `Reference`

## Icon and cover

- **Icon**: 🧭
- **Cover**: abstract / decision-themed gallery image

## Body structure

### 1. Definition callout

```
ℹ️ Why we made the decisions we made. Each entry is framed as a question someone might ask, with the answer being the decision and its rationale. Aim to capture decisions at the moment they're made — adding them later loses the alternatives that were considered.
```

### 2. How to read it

Brief one-liner: "Most recent decisions at the top. Use the FAQ-style heading to scan for the decision you're looking for."

### 3. Entries

Each entry uses the format defined in `decision-log-entry.md`. Most recent first.

## Initial seed entry on creation

When first creating the page, ask the user if they want to seed it with one entry — typically the choice of a key technology or approach. If yes, use the entry format. If no, leave the page with just the header content and note "No entries yet" as a small italic line.

## What NOT to include

- Trivial decisions (which lint rule to use, what colour the button is)
- Decisions you can't reconstruct accurately — better to skip than fabricate
- Future decisions ("we'll decide X later") — those belong in open questions or roadmap

## Avoiding decay

Decision logs die when entries are added retrospectively because alternatives and reasoning fade. The skill should make `log-decision` very low-friction so capture happens at the moment of decision. The Slack bot integration (deferred) is the natural amplifier of this.

## Source notes

- **All entries**: entirely user-stated — never infer, reconstruct, or invent decisions
- The page header callout and seed entry are written on creation; all subsequent entries are appended via `log-decision`
- Page-level Verification stays Empty on creation; adding entries does not change it
