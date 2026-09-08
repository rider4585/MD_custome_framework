---
name: emotional-brief
version: 1.0.0
description: |
  Turn "make it feel premium" into decisions a designer and engineer can execute
  — one named emotion per event category, mapped to pace, crop, colour, type,
  and motion. Use at the start of any page or case study, when a design is
  technically correct but emotionally flat, or when someone says "it needs more
  wow" without saying what wow means.
allowed-tools:
  - Read
  - Write
  - Grep
  - Glob
---

## Emotional Brief

A portfolio for a photographer is not selling a service. It is selling
**evidence that this company can produce a feeling on a specific day**. The site
fails when every event looks like the same event — same grid, same fade-up, same
warm filter — because that tells the visitor the company has one move.

The job of this skill is to make the *difference between event types visible* in
the design itself, before anyone writes CSS.

> **Before running anything:** load `project-shoot-catalogue` for the event types
> this client actually sells. Do not design for categories they do not run.

### Method

1. **Name one emotion per event type.** One word, not three. If you cannot pick
   one, you do not understand the event yet — go and look at fifty photos of it.
2. **Derive the register** — pace, crop, colour temperature, type weight, motion.
   Each is a decision the emotion forces, not a free choice.
3. **Find the contradiction.** Every brief has one axis where two event types
   pull opposite. That axis is where the design system has to flex, and it is the
   only place it should.
4. **Write the one-line test.** A sentence a reviewer can hold a screen against
   and say yes or no. "Does this page feel like the ten minutes before the bride
   walks in?" is testable. "Is this premium?" is not.
5. **Record it** in the case study's frontmatter so it survives the handoff.

### The mapping

This is the framework's core model. It is **not sourced** — it is an argued
opinion, and it is the first thing you should challenge with real photos.

| Event type | Emotion | Pace | Crop | Colour | Type | Motion |
|---|---|---|---|---|---|---|
| **Wedding ceremony** | Reverence | Slow, lingering | Tight faces, hands, detail | Warm, low-saturation, deep shadow | Serif display, generous leading | Long fades, no bounce |
| **Sangeet / Mehendi** | Exuberance | Fast, cut-driven | Wide, crowded, motion blur welcome | Saturated, high-key | Heavy sans, tight tracking | Quick, overlapping, slight overshoot |
| **Corporate / conference** | Competence | Measured, even | Wide, architectural, symmetrical | Cool, restrained, near-neutral | Clean grotesque, strict scale | Minimal — reveal, do not perform |
| **Birthday / private party** | Delight | Bouncy | Candid, close, imperfect | Bright, playful accents | Rounded sans | Springy, short |
| **Public / civic function** | Gravitas | Still | Very wide, stage-centred, symmetrical | Desaturated, formal | Serif, sparse | Almost none. Stillness *is* the effect |

**The contradiction axis for a full-service organiser is reverence vs.
exuberance** — usually inside the same wedding, hours apart. A single global
"vibe" cannot hold both. Resolve it by making the *case study* carry register,
and the *chrome* (nav, footer, buttons) stay neutral. Neutral chrome is what lets
a saturated Sangeet page and a hushed ceremony page live in one site without
either looking broken.

### Deriving decisions from an emotion

Do not stop at the adjective. Push each one until it names a property:

> Reverence → the viewer should feel they are intruding slightly → **tight
> crops**, subjects unaware of camera → **no hard cuts**, transitions slower than
> feels comfortable (700–900 ms) → **generous negative space**, because crowding
> reads as urgency → **muted palette**, because saturation reads as celebration,
> not ceremony.

Every arrow is a claim you can be wrong about. Write them down so a reviewer can
disagree with the arrow rather than with the taste.

### Anti-patterns

- **The uniform grid.** Forty events in identical 4:3 tiles. Every event now has
  the same emotional weight, which means none has any.
- **Stock emotion.** Words that survive find-and-replace between two different
  companies ("elegant", "seamless", "bespoke") are not a brief.
- **Emotion applied only to the hero.** A reverent hero followed by a bouncy card
  grid tells the visitor the hero was a costume.
- **Deferring to the client's favourite photo.** The photo the client loves is
  often the one they worked hardest on, not the one that carries the feeling.
  Argue with evidence, then defer — it is their business.

### Caveats

- **This model is a hypothesis about a market you may not know.** Latur wedding
  culture is not Mumbai wedding culture. Test the mapping against the client's
  own photographs before treating any row as settled.
- **Emotion does not override accessibility.** "Reverence" is not a reason for
  2.5:1 text contrast, and "exuberance" is not a reason to ignore
  `prefers-reduced-motion`. See `accessibility` and `motion-design`.
- **One emotion per event type, not per page.** Multiplying emotions is how a
  site loses coherence.

### Checklist

- [ ] Every event type the client sells has exactly one named emotion
- [ ] Each emotion resolved into pace, crop, colour, type, and motion
- [ ] The contradiction axis identified and its resolution stated
- [ ] A one-line, answerable test written per event type
- [ ] Chrome confirmed neutral, so registers can differ without breaking
- [ ] Mapping checked against at least twenty real client photographs
- [ ] Brief recorded in case-study frontmatter, not only in a conversation

## References

- **Nielsen Norman Group — "Emotional Design"** and its treatment of visceral vs.
  reflective response, which is why a hero image does more work than a headline
  <https://www.nngroup.com/articles/theory-user-delight/>
- **WCAG 2.2 — SC 1.4.3 Contrast (Minimum), SC 2.3.3 Animation from
  Interactions** — the floor that no emotional register may cross
  <https://www.w3.org/TR/WCAG22/>
- **Butterick's *Practical Typography*** — restraint as an expressive choice, not
  an absence of one <https://practicaltypography.com/>

**Not sourced — written for this framework:** the entire event-to-emotion mapping
table, the contradiction-axis concept, the neutral-chrome resolution, the
derivation-arrow method, and the anti-patterns. These are opinions, argued above.
