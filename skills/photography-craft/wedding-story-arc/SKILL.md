---
name: wedding-story-arc
version: 1.0.0
description: |
  Structure a wedding or function into the narrative beats a case study needs —
  which moments carry the story, how many frames each earns, and where a
  multi-day Indian wedding differs from a single-day one. Use when building a
  case study, sequencing a gallery, planning coverage, or when a gallery has
  200 good photographs and no story.
allowed-tools:
  - Read
  - Write
  - Grep
  - Glob
---

## Wedding Story Arc

A wedding gallery and a wedding *story* are different objects. A gallery is
chronological and complete; a story is selective and shaped. Couples browsing a
portfolio are not auditing coverage — they are asking "would this person have
seen the parts of my day that mattered?" A case study answers that by showing a
shape, not an archive.

The core claim of this skill: **the beats are the unit of selection, not the
hours.** You do not cull "the morning"; you cull *anticipation*.

> **Before running anything:** load `project-service-catalogue` for the function
> types this photographer actually shoots, and `emotional-brief` for the register
> each carries. Do not build a Sangeet section for a photographer who does not
> shoot them.

### Method

1. **Lay the day out as beats**, using the tables below as a starting map.
2. **Assign each beat a role** in the six-role scheme from `art-direction`:
   establishing, hero, detail, human, scale, closing.
3. **Allocate frames per beat** — the budget table below. A case study is 9–14
   frames total, so most beats get zero or one.
4. **Choose the hero last**, and choose it against the register, not against
   technical quality → `emotional-brief`.
5. **Cut every beat that does not advance the story**, however well shot.
6. **Read the sequence with no captions.** If a stranger cannot tell what
   happened and roughly in what order, the sequence has failed.

### The universal arc

Every function, in any culture, resolves to five movements. Use this when the
event type is unfamiliar.

| Movement | What it carries | Typical frames |
|---|---|---|
| **1. Anticipation** | Getting ready, empty venue, hands, held breath | 2–3 |
| **2. Threshold** | The irreversible act — entrance, vows, the knot, the cutting | 2–3, includes the hero |
| **3. Release** | Celebration, dancing, noise, motion | 2–4 |
| **4. Intimacy** | The quiet frame nobody else got — the aftermath | 1–2 |
| **5. Departure** | Exit, last light, the emptied room | 1 |

**Movement 4 is where photographers are hired or not.** Movements 1–3 are covered
by everyone including the guests with phones. The aftermath frame — the mother
sitting down after, the couple alone for eight seconds — is the differentiator,
and it should be the frame the case study lingers on → `signature-style`.

### Indian multi-day weddings

A North or West Indian wedding is not one arc but **three to five arcs across
several days**, each with its own register. Treating it as one long chronology is
the most common structural mistake in this market.

| Function | Register | Story job | Frames in a combined case study |
|---|---|---|---|
| **Haldi** | Play, mess, yellow | Texture and colour; the least formal day | 1–2 |
| **Mehendi** | Stillness, detail, hands | Pure detail work; often the best macro frames | 1–2 |
| **Sangeet** | Exuberance, noise, stage light | Motion and crowd; the drag-shutter frames | 2–3 |
| **Baraat** | Chaos, movement, street | Scale and energy; hardest to shoot | 1–2 |
| **Ceremony / phera** | Reverence, fire, ritual detail | The threshold; usually the hero | 2–3 |
| **Vidaai** | Grief inside joy | The intimacy movement, unmatched | 1 |
| **Reception** | Formality, portraiture, stage | Polish; often the least interesting frames | 0–1 |

**Vidaai is the single most under-used frame in Indian wedding portfolios.** It
is the only universally recognised moment in the arc where the emotion is
ambivalent, and ambivalence is what makes a photograph hold attention. If the
photographer has one and consent allows it, it belongs in the case study.

**Structural choice — one case study or several?**

- **One combined study** for a full multi-day booking: shows scope, and scope is
  what a full-wedding client is buying. Use day dividers, not a flat scroll.
- **Separate studies per function** when the photographer is often booked for a
  single function. It lets a Haldi-only enquirer see Haldi-only work.
- **Never both for the same wedding** — duplicate URLs of the same images split
  ranking and confuse the visitor → `project-site-architecture`, `seo-foundations`.

### Non-wedding functions

| Function | Arc compression | The frame that must exist |
|---|---|---|
| **Engagement / roka** | Movements 2 and 4 only | The reaction, not the ring |
| **Pre-wedding shoot** | No arc — it is a portrait set | One frame with real, unposed contact |
| **Birthday** | 1, 3, 4 | The child's face at the moment of the candles, not the cake |
| **Naming / cradle ceremony** | 1, 2, 4 | Grandparents' hands |
| **Corporate function** | 1, 2, 3 compressed | The speaker mid-gesture with the room visible behind |
| **Anniversary** | 4, then 1 | The couple looking at each other, not the camera |

A pre-wedding shoot deliberately has no arc, and sequencing it like a wedding
makes it look padded. Sequence portrait sets by **light and distance**, not by
time → `gallery-patterns`.

### Frame budget

For a 9–14 frame case study — the range `art-direction` sets:

```
Anticipation   ██        2
Threshold      ███       3   ← hero lives here
Release        ███       3
Intimacy       ██        2
Departure      █         1
                        ---
                        11
```

**Every frame added past 14 reduces the weight of all the others.** If the
photographer insists on more, that is what a client gallery is for →
`client-gallery-delivery`. The case study is an argument; the gallery is the
evidence. Do not confuse them.

### Captions and names

- **Caption the beat, not the photograph.** "Just before the baraat reached the
  gate" earns its place; "Beautiful moment" does not → `web-copywriting`.
- **Real names only with consent, in writing**, and never for minors →
  `media-consent`.
- **Alt text describes the frame for someone who cannot see it**, and is not the
  caption repeated → `accessibility`.
- **Credit the second shooter and the film team by name** if they shot frames in
  the set → `image-rights-and-credit`, `vendor-network`.

### Caveats

- **These beat tables are a starting map, not ethnography.** Wedding structure
  varies enormously by region, community, and family — South Indian, Bengali,
  Muslim, Christian, and Maharashtrian weddings each have beats absent from the
  table above. Ask the photographer which functions they actually shoot and
  rewrite the rows before using them.
- **Consent constrains the arc.** Vidaai, getting-ready, and any frame involving
  a child may be unusable regardless of how good it is. Check consent *before*
  designing the sequence, not after → `media-consent`.
- **Do not stage the arc.** This is a selection model for work already shot. A
  photographer chasing the five movements during a live wedding will miss the
  wedding.
- **The frame budget is this framework's opinion**, tuned for a scrolling case
  study on a phone. A print portfolio would answer differently.

### Checklist

- [ ] Day mapped to beats before any frame is chosen
- [ ] Each function's register taken from `emotional-brief`, not assumed
- [ ] Every selected frame assigned a movement and an `art-direction` role
- [ ] Hero chosen last, from the threshold movement
- [ ] An intimacy/aftermath frame present — this is the differentiator
- [ ] Total 9–14 frames; overflow sent to the client gallery instead
- [ ] Combined-vs-separate structure decided, and not both
- [ ] Sequence readable with captions hidden
- [ ] Consent confirmed per frame before the sequence is locked
- [ ] Captions name the beat; alt text written separately
- [ ] Second shooters and film team credited

## References

- **This framework's `art-direction`** — the six image roles and the 9–14 frame
  range this skill allocates against
- **This framework's `emotional-brief`** — the function-to-register mapping the
  beat tables inherit
- **This framework's `media-consent`** — the consent gate that constrains which
  beats are publishable, built on India's DPDP Rules 2025
- **Freytag's five-part dramatic structure**, the classical source of the
  five-movement shape adapted here
  <https://www.britannica.com/art/Freytags-pyramid>

**Not sourced — written for this framework:** the five-movement arc as applied to
functions, the Indian multi-day function table and its register assignments, the
claim that the intimacy/aftermath frame is the hiring differentiator, the
vidaai argument, the non-wedding compression table, and the frame budget.
