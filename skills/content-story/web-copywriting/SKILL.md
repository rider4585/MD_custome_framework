---
name: web-copywriting
version: 1.0.0
description: |
  Write headlines, body copy, calls to action, and interface microcopy for a
  premium service site. Use when drafting or editing any user-facing text, when
  copy reads generic, or when writing form labels, error messages, and empty
  states.
allowed-tools:
  - Read
  - Write
  - Grep
  - Glob
---

## Web Copywriting

Two jobs live in this skill because they fail together: **marketing copy** that
makes a promise, and **microcopy** that keeps it at the moment someone acts. A
site with a beautiful hero and a form that says "Invalid input" has broken the
promise at the only point that mattered.

> **Before running anything:** load `brand-narrative` for the voice table and the
> five-sentence spine. Copy that contradicts the positioning is worse than no copy.

### Method

1. **Write the CTA first.** Everything above it exists to earn it.
2. **Write headlines as claims**, then delete any that a competitor could also
   make.
3. **Cut the first sentence of every paragraph.** It is almost always throat-
   clearing.
4. **Write every interface state** — including error, empty, and success.
5. **Read it aloud.** Anything you would not say to a client in their living room
   is cut.

### Headlines

| Weak | Why | Stronger |
|---|---|---|
| "Creating unforgettable experiences" | True of every competitor | "310 guests. One venue change. Eleven days' notice." |
| "Your dream wedding awaits" | About the reader's fantasy, not the company's ability | "We have run 40 weddings in <the city>. Here are twelve." |
| "Premium event solutions" | Category words | "The same four people are there on the day." |

The test: **swap in a competitor's name. If the sentence still works, it is not a
headline.**

Length: 4–9 words for a hero. Under 4 is usually a slogan; over 9 will not hold
at display size and will wrap badly at 375 px.

### Body copy

- **One idea per paragraph, 2–4 sentences.**
- **Front-load.** Readers scan the first few words of each block.
- **Concrete nouns.** *Mandap*, *baraat*, *sangeet*, generator, marquee, the
  venue's name — not "elements" and "components".
- **Numbers with a source.** Every unsourced number is a liability.
- **Second person for the reader, first person plural for the company.** "You
  will meet the same coordinator" / "We book the generator ourselves."
- **Never use "we" to hide a decision.** "It was decided" is the enemy.

### Calls to action

On a premium service site the CTA is a conversation, not a purchase.

| Context | Label | Not |
|---|---|---|
| Primary, everywhere | "Check your date" | "Submit" |
| Secondary | "See a wedding we planned" | "Learn more" |
| Instant channel | "WhatsApp us" | "Contact" |
| Post-enquiry | "We reply within one working day" | *(silence)* |

**"Check your date" outperforms "Get a quote" for this category** because it is
lower commitment, it is the question the visitor actually has, and it does not
raise price before the value has landed. This is a framework claim; test it if
volume allows. See `enquiry-conversion`.

**Give WhatsApp equal weight to the form.** In this market it is often the
preferred channel, and a form-only contact page loses enquiries silently.

### Microcopy — where trust is actually won or lost

| State | Rule | Example |
|---|---|---|
| Field label | Always visible. **Never placeholder-only** | "Event date (approximate is fine)" |
| Helper text | Say why you are asking | "So we can check availability" |
| Optional field | Mark optional, not required | "Guest count (optional)" |
| Error | Name the field, say what is wrong, say how to fix | "Enter a date in the future — we cannot book past dates" |
| Empty state | Say what will appear and how to make it appear | "No weddings tagged 'outdoor' yet." |
| Loading | Say what is happening | "Sending your enquiry…" |
| Success | Confirm, and say what happens next and when | "Got it. Sonal will reply by tomorrow evening." |

**Placeholder-only labels are an accessibility failure and a usability one** —
the label vanishes on focus, fails WCAG SC 3.3.2, and is invisible to some
screen-reader flows.

**Error messages must never blame.** "Invalid" and "You must" are both blame.
Name the fix.

### Length discipline

| Element | Budget |
|---|---|
| Hero headline | 4–9 words |
| Hero subhead | ≤ 20 words |
| Section heading | ≤ 6 words |
| Body paragraph | 2–4 sentences |
| Case-study brief | 40–70 words |
| Button label | 1–4 words |
| Meta description | 150–160 characters |
| `alt` text | ≤ 125 characters, describing what matters |

### Detection

```bash
# Category words that survive a competitor swap
grep -rniE '\b(solutions?|experiences?|bespoke|seamless|elevate|curated|unforgettable|world.class)\b' \
  src/ --include=*.astro --include=*.md

# Placeholder-only inputs
grep -rn '<input' src/ --include=*.astro | grep placeholder | grep -v 'aria-label\|<label'

# Blaming error copy
grep -rniE '"(invalid|you must|wrong|failed)' src/ --include=*.astro --include=*.js

# Vague CTAs
grep -rniE '>(submit|learn more|click here|read more)<' src/ --include=*.astro
```

### Caveats

- **The client's existing copy is theirs.** Propose edits with the reason, do not
  silently rewrite their About page.
- **Do not write in a register you cannot verify.** If the site ships <the local language>,
  a native speaker writes it — translated marketing copy reads as translated. See
  `multilingual-content`.
- **Concrete numbers require sourcing.** If nobody can confirm "40 weddings", it
  does not ship. See `project-client-brand` for what is `[to verify]`.
- **Length budgets are for the design to hold**, not laws. Break them knowingly.

### Checklist

- [ ] CTA written first; everything above earns it
- [ ] Every headline fails the competitor-swap test
- [ ] Category words removed
- [ ] Numbers sourced or cut
- [ ] Body paragraphs front-loaded, 2–4 sentences
- [ ] WhatsApp given equal weight to the form
- [ ] Every field has a visible label; no placeholder-only inputs
- [ ] Error, empty, loading, and success copy written for every state
- [ ] No error message blames the user
- [ ] Success message says what happens next and when
- [ ] Length budgets checked against the real design at 375 px
- [ ] Read aloud

## References

- **WCAG 2.2 — SC 3.3.2 Labels or Instructions, SC 3.3.1 Error Identification,
  SC 3.3.3 Error Suggestion** <https://www.w3.org/TR/WCAG22/#labels-or-instructions>
- **Nielsen Norman Group — "Placeholders in Form Fields Are Harmful"**
  <https://www.nngroup.com/articles/form-design-placeholders/>
- **Nielsen Norman Group — how users read on the web**, on front-loading and
  scanning <https://www.nngroup.com/articles/how-users-read-on-the-web/>
- **MDN — `alt` text and accessible descriptions**
  <https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/img#alt>
- **Google Search Central — snippet and meta description guidance**
  <https://developers.google.com/search/docs/appearance/snippet>

**Not sourced — written for this framework:** the competitor-swap test, the
headline comparison table, the "Check your date" CTA claim, the microcopy state
table, the length budgets, and the detection commands.
