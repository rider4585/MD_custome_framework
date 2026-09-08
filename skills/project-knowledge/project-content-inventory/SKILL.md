---
name: project-content-inventory
version: 2.0.0
description: |
  What content, media, and data actually exist for this project, what is
  missing, and who must supply it. Use before estimating, before designing
  around content that may not exist, and to track the gap to launch.
allowed-tools:
  - Read
  - Write
  - Grep
  - Glob
  - Bash
---

## Project: Content Inventory

**The most common way a client project fails is that the build is finished and
the content is not.** Not the design, not the code — the words, images, data,
permissions, and decisions that only the client can supply.

This skill tracks that gap. **It is a live document**; update it as things
arrive.

> ## 🟡 Template until onboarding fills it
>
> Shipped unfilled. Populated during onboarding → `project-discovery`.

### Abundance is not readiness

The trap on almost every project. A client with a 90,000-file asset library, a
decade of records, or a full existing site will say the content exists — and
they are right, and it is still not usable. Between "exists" and "publishable"
sit selection, permission, correction, and writing, and **all four are client
effort, not build effort.**

Say this early and in writing. It is the single most useful expectation you can
set, and it is nearly always the real critical path.

### Status

| Asset | Status | Owner | Blocking? |
|---|---|---|---|
| — | Unassessed / Partial / Ready | — | — |

Use **Unassessed** honestly. "Probably fine" is unassessed.

### Required for launch

One row per thing that must exist before this can ship. Quantities, not
adjectives.

| Item | Quantity | Owner | Blocking? |
|---|---|---|---|
| — | — | — | — |

**Set a floor and defend it.** Below some quantity the work looks thin and
undermines its own positioning — decide that number with the client at the
start, when it is a plan, rather than at the end, when it is a disappointment.

### The interview

Most client content does not exist and cannot be written from the assets. It
comes from **one short conversation per item**, and the questions that produce
usable material are consistent across domains:

1. What did they ask you for?
2. What worried you about this one before you started?
3. **What went wrong, or was harder than expected?** ← the substance lives here
4. What did you decide to do about it?
5. What are the facts — dates, scale, participants, constraints?
6. **Which part are you proudest of, and why?**
7. Who else was involved, and what credit is owed?
8. What may we publish — names, images, figures, the problem itself?

Question 3 is what separates a case study from a description. Expect to ask it
twice; the first answer is always "it went smoothly."

**Record the interviews** (with permission). The client's own phrasing is better
copy than anything written afterwards → `web-copywriting`.

### Assessing an existing archive

Before planning around it. Adapt the commands to the asset types in play.

```bash
# Volume and usable resolution, for image archives
find "$ARCHIVE" -type f \( -iname '*.jpg' -o -iname '*.png' \) | wc -l
exiftool -ImageWidth -ImageHeight -q "$ARCHIVE"/**/*.jpg 2>/dev/null | head

# Who made it — an attribution and licensing question, not a curiosity
exiftool -Artist -Copyright -q "$ARCHIVE"/**/* 2>/dev/null | sort -u

# Location metadata that must not be published
exiftool -GPSLatitude -q "$ARCHIVE"/**/* 2>/dev/null | grep -c GPS

# For a text or data archive: how much is actually current?
find "$ARCHIVE" -type f -mtime +730 | wc -l   # untouched in two years
```

**Report the finding honestly and early.** If the archive cannot support the
plan, that is a schedule fact, not an opinion, and it is cheap as an estimate
and expensive as a slip.

### Gaps that need a human with access

Some evidence is behind a login that automated fetching cannot pass — a social
account, an analytics property, a CRM, an old CMS, a shared drive. **Name the
gap explicitly rather than working around it**, list exactly what a human must
export, and treat the affected area as unassessed until they do.

Exports from platforms are often **lower quality than the originals** —
re-encoded images, truncated records, stripped metadata. Never plan to build
from an export when the original exists.

### Tracking

Keep a per-item table and update it as things arrive:

```markdown
| Item | Assets | Interview | Text | Permissions | Ready |
|---|---|---|---|---|---|
| <name> | 12 ✓ | ✓ | draft | ✓ | **yes** |
| <name> | 6 ✗ | ✓ | — | ✗ | no |
```

**"Ready" should mean the build passes**, not that someone thinks it looks
done. Enforce what can be enforced in a schema → `content-collections`.

### Caveats

- **Content is the critical path, not development.** A five-day build waiting
  six weeks for content is the normal shape of these projects, and planning as
  if it were the reverse is how deadlines are missed.
- **Do not design around content that does not exist.** A layout or feature that
  requires material the archive cannot supply is a liability, not an aspiration.
- **The client will underestimate this effort.** Interviews, selection, chasing
  permissions, and correcting records is real work for them. Schedule it
  explicitly and send the questions in advance.
- **Permissions may be impossible retroactively.** Prefer recent material where
  the relationship is still warm → `media-consent`.
- **Launching with a strong subset beats waiting for everything.** Ship, then
  add.

### Checklist

- [ ] Archive located and access granted
- [ ] Archive assessed for volume, quality, currency, and attribution
- [ ] Login-walled gaps named, with a human owner and a specific export request
- [ ] Launch floor agreed with the client, in writing
- [ ] Interviews scheduled, conducted, and recorded with permission
- [ ] Permissions obtained per item, including for third parties
- [ ] Tracking table maintained and shared with the client
- [ ] "Ready" enforced by the build where it can be
- [ ] Content critical path communicated in writing, early

## References

- **`project-discovery`, `project-context`, `project-service-catalogue`**
  (this framework) — the onboarding and scope this inventory serves
- **`case-study-structure`, `web-copywriting`, `media-consent`,
  `content-collections`** (this framework) — the workflows this inventory feeds
- **ExifTool** — the archive assessment commands above <https://exiftool.org/>

**Not sourced — written for this framework:** the abundance-is-not-readiness
framing, the launch-floor rule, the eight interview questions, the
archive-assessment commands, the login-walled-gap procedure, and the tracking
table.
