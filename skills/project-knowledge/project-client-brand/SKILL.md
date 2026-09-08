---
name: project-client-brand
version: 2.0.0
description: |
  The verified facts about the client or business this project is for, and —
  more importantly — the list of what is NOT yet verified. Use before writing
  any copy, publishing any claim or price, or entering any business detail into
  structured data, a contract, or a config. Load this first, in every session
  that touches client-facing content.
allowed-tools:
  - Read
  - Write
  - Grep
  - Glob
---

## Project: Client and Brand Facts

This is the project's **single source of truth about the client**. It exists
because the alternative — an agent inferring plausible business facts — produces
work that is confidently wrong, and a wrong address, price, or credential
propagates into structured data, contracts, and the client's reputation.

> ## 🟡 This file is a TEMPLATE until onboarding fills it
>
> Shipped unfilled. The orchestrator populates it during onboarding →
> `project-discovery`. **An unfilled file means every fact is unknown**, which
> is a stricter state than an empty one, not a laxer one.

> ## ⛔ Phase 0 is blocking
>
> **Nothing marked `[to verify]` may be published.** Not in copy, not in
> structured data, not in a meta description, not in a page title, and above all
> not in a price or a legal claim. An agent that needs an unverified fact must
> either omit it or escalate — never estimate.
>
> This is the rule most likely to be argued with, and the one that protects the
> client most.

### The three-state model

Every fact about the client is in exactly one state. There is no fourth state,
and "probably right" is not one of them.

| State | Means | May be published? |
|---|---|---|
| **Verified** | Confirmed by the client, with a date and a method | ✅ Yes |
| **`[to verify]`** | From a public source, a document, or an inference | ❌ **No** |
| **Unknown** | Nobody has asked | ❌ No — and it must be asked |

The distinction that matters is between *verified* and *plausible*. A directory
listing, an old brochure, a competitor's description, a LinkedIn page, and an
inference from a domain name are all `[to verify]`. So is anything the previous
agency wrote.

### Status

Update these counts whenever the file changes. A stale count is worse than none.

| | Count |
|---|---|
| Verified with the client | **0** |
| `[to verify]` | 0 |
| Unknown — must be asked | — |

**As of <date>, no fact in this file has been confirmed by the client.**

### Verified

| Fact | Value | Confirmed | How |
|---|---|---|---|
| — | — | — | — |

Add a row only with a date and a method. "The client mentioned it once" is a
method; write that down rather than leaving the column blank.

### `[to verify]`

| Fact | Value | Source | Risk if wrong |
|---|---|---|---|
| — | — | — | — |

**Record the risk column honestly.** It is what decides which gaps get chased
first. A trading name that reaches every page title and every embedded copyright
notice is a higher-risk gap than a founding year in an about paragraph.

### Unknown — must be asked

The starting question set. Delete what does not apply to this project; add what
does. Nothing here can be guessed.

**Identity**
1. Exact trading name — spelling, capitalisation, punctuation
2. Legal entity name and registration status
3. Who the client-facing people are, and what they are called publicly
4. Address, and whether it should be published at all
5. Phone number(s) that are answered, and by whom
6. Business email
7. Domain ownership — who holds the registration
8. Year founded, or year the current form of the business began

**Positioning** → `brand-narrative`
9. What work do they turn down? (the refusal)
10. What are they visibly best at, in their own view?
11. Who do they lose to, and on what?
12. Proof points — counts, years, named clients — **sourced, not estimated**

**Commercial**
13. What is sold, and at what prices
14. Whether prices may be published
15. Terms that appear publicly — deposits, turnaround, travel, cancellation

**Content and rights**
16. Where the source material lives, and who owns the rights to it
17. Whether third parties contributed, and what credit is owed
18. Whether consent exists for any identifiable person shown

**Market and language**
19. Which languages, with evidence for the choice
20. Which regions or segments are actually served

### How to use this file

**Reading:** load it at the start of any session that writes copy, metadata,
prices, structured data, or anything a customer will read.

**Writing:** when the client confirms a fact, move it into **Verified** with the
date and how it was confirmed:

```markdown
| Phone | +XX XXXXX XXXXX | 2026-09-15 | Call with <name>, noted in project log |
```

**Never delete a fact that turns out to be wrong** — record the correction, so
the same wrong value is not re-derived from the same source later.

### Escalation

When an agent needs an unverified fact:

1. **Omit it** and continue, if the work can proceed without it.
2. **Mark the gap** in the output — `[NEEDS: verified trading name]`.
3. **Escalate** to the orchestrator with `query`, naming the fact and why it is
   blocking.

**Do not**: estimate, use a public-source value as if verified, invent a price,
or write around the gap so smoothly that nobody notices it is missing.

### When the client is not reachable

Common, and not a reason to relax the rule. Ship with the gap visible rather
than filled: omit the section, use a placeholder the build rejects, or publish
the page without the claim. **A missing fact is a smaller problem than a wrong
one**, because a wrong one is discovered by a customer.

### Caveats

- **This file is only as good as its last update.** Assign an owner; an
  unmaintained facts file is trusted long after it stops being true.
- **Verified facts expire.** Addresses, prices, and team members change. Re-check
  anything load-bearing before a relaunch.
- **A client confirming a fact does not make it lawful to publish** — consent,
  licensing, and third-party rights are separate gates → `media-consent`.
- **Do not let this file become the whole brief.** It holds facts, not
  positioning; positioning lives in `brand-narrative`.

### Checklist

- [ ] Loaded before any copy, metadata, price, or structured data is written
- [ ] Every fact in exactly one of the three states
- [ ] No `[to verify]` value published anywhere
- [ ] Risk column filled for every `[to verify]` fact
- [ ] Highest-risk gaps chased first
- [ ] Every confirmation recorded with date and method
- [ ] Corrections recorded, not silently overwritten
- [ ] Status counts current
- [ ] An owner named for keeping this file true

## References

- **`project-discovery`, `project-context`** (this framework) — the onboarding
  that populates this file
- **`brand-narrative`, `web-copywriting`, `structured-data`** (this framework) —
  the consumers of these facts, and where a wrong one does the most damage
- **`media-consent`** (this framework) — the separate permission that ownership
  of a fact or an asset does not supply

**Not sourced — written for this framework:** the Phase 0 blocking rule, the
three-state model, the risk column, the escalation procedure, the
client-unreachable guidance, and the question set.
