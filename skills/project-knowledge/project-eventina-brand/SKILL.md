---
name: project-eventina-brand
version: 1.0.0
description: |
  The verified facts about Eventina Organisers, and — more importantly — the list
  of what is NOT yet verified. Use before writing any copy, publishing any claim,
  or entering any business detail into structured data. Load this first, in every
  session that touches content.
allowed-tools:
  - Read
  - Write
  - Grep
  - Glob
---

## Project: Eventina Brand Facts

This is the project's **single source of truth about the client**. It exists
because the alternative — an agent inferring plausible business facts — produces
copy that is confidently wrong, and a wrong address or founding year in
structured data propagates across the web.

> ## ⛔ Phase 0 is blocking
>
> **Nothing marked `[to verify]` may be published.** Not in copy, not in
> structured data, not in a meta description. An agent that needs an unverified
> fact must either omit it or escalate — never estimate.
>
> This is the rule most likely to be argued with, and the one that protects the
> client most.

### Status

| | Count |
|---|---|
| Verified with the client | **0** |
| `[to verify]` — from public sources | 8 |
| Unknown — must be asked | 14 |

**As of 2026-09-07, no fact in this file has been confirmed by the client.**
Update this table when that changes.

### `[to verify]` — from public sources, 2026-09-07

Gathered from public directory and social listings. **Plausible, not confirmed.**

| Fact | Value | Source |
|---|---|---|
| Trading name | Eventina Organisers | Instagram, Facebook |
| Registered style | "Eventina Organisers Pvt Ltd" | Instagram page title |
| Address | Kailash Plaza, Beside Manas Hotel, Barshi Road, Ganj Golai, Latur, Maharashtra 413512 | [Justdial](https://www.justdial.com/Latur/Eventina-Organisers-Near-Manas-Hotel-Latur-HO/9999P2382-2382-191109130517-W8Y2_BZDET) |
| Established | 2016 | Justdial |
| Services listed | Weddings, birthday parties, dance parties | Justdial |
| Rating | 4.8 / 5 from 93 ratings | Justdial |
| Instagram (current) | [`@eventina.organisers`](https://www.instagram.com/eventina.organisers/) — "Eventina 2.0 ✨ New account. Same passion." | Instagram |
| Instagram (previous) | [`@eventinaorganisers`](https://www.instagram.com/eventinaorganisers/) | Instagram |
| Facebook | [`Eventina.in`](https://www.facebook.com/Eventina.in/) | Facebook |

**The address is the highest-risk item.** It came from a directory listing that
may be years stale, and it is destined for `LocalBusiness` structured data and
the Google Business Profile, where an error propagates → `structured-data`,
`local-discovery`.

### Unknown — must be asked

Nothing here can be guessed.

**Business**
1. Exact legal entity name and registration status
2. Current address — has it moved since the Justdial listing?
3. Phone number(s) that are answered, and by whom
4. Business email
5. Whether `eventina.in` or another domain is owned, and by whom
6. Team size, and who is client-facing on the day

**Positioning** → `brand-narrative`
7. What work do they turn down? (the refusal)
8. What are they visibly best at, in their own view?
9. Who are the competitors they lose to, and on what?
10. Event count to date, repeat-client rate — sourced, not estimated

**Content** → `project-content-inventory`
11. Where the photograph archive lives, and who owns the rights
12. Which photographers shot which events, and what credit is required
13. Whether client consent for marketing use exists → `media-consent`

**Language and market** → `multilingual-content`
14. Marathi, English, or both — and evidence for the choice

### The Instagram gap

**The Instagram grid could not be read.** Instagram serves a login wall to
automated fetching, so post captions, image count, current bio, and the actual
mix of event types are all unknown.

This matters more than it sounds: `@eventina.organisers` is the client's strongest
existing channel and its content *is* the portfolio's raw material.

**A human with account access must supply:**
- The current bio text and link
- Follower count and posting cadence
- A breakdown of the last 30 posts by event type
- Which posts performed best, and the client's view of why
- Whether the older `@eventinaorganisers` account should be referenced or retired

Until then, `project-content-inventory` treats the archive as unassessed.

### How to use this file

**Reading:** load it at the start of any session that writes copy, metadata, or
structured data.

**Writing:** when the client confirms a fact, move it from `[to verify]` or
Unknown into a **Verified** section with the date and how it was confirmed:

```markdown
### Verified

| Fact | Value | Confirmed | How |
|---|---|---|---|
| Phone | +91 XXXXX XXXXX | 2026-09-15 | Call with Sonal, noted in project log |
```

**Never delete a fact that turns out to be wrong** — record the correction, so
the same wrong value is not re-derived from the same source later.

### Escalation

When an agent needs an unverified fact:

1. **Omit it** and continue, if the work can proceed without it.
2. **Mark the gap** in the output — `[NEEDS: verified founding year]`.
3. **Escalate** to the orchestrator with `query`, naming the fact and why it is
   blocking.

**Do not**: estimate, use a public-source value as if verified, or write copy
around the gap so smoothly that nobody notices it is missing.

### Checklist

- [ ] Loaded before any copy, metadata, or structured data is written
- [ ] No `[to verify]` value published anywhere
- [ ] Address confirmed with the client before it enters structured data
- [ ] Instagram gap filled by a human with account access
- [ ] Every confirmation recorded with date and method
- [ ] Corrections recorded, not silently overwritten
- [ ] Status counts at the top kept current

## References

- **Justdial — Eventina Organisers listing, Ganj Golai, Latur**, source of the
  address, founding year, services, and rating recorded above as `[to verify]`
  <https://www.justdial.com/Latur/Eventina-Organisers-Near-Manas-Hotel-Latur-HO/9999P2382-2382-191109130517-W8Y2_BZDET>
- **Instagram — [`@eventina.organisers`](https://www.instagram.com/eventina.organisers/)**,
  source of the current handle and the "Eventina 2.0" relaunch note. **The grid
  itself was not readable**
- **Facebook — [`Eventina.in`](https://www.facebook.com/Eventina.in/)**
- **`SOURCES.md`** (this repository) §9 — the consolidated record of what was
  gathered, when, and from where

**Not sourced — written for this framework:** the Phase 0 blocking rule, the
verified/`[to verify]`/unknown three-state model, the escalation procedure, and
the list of questions to ask.
