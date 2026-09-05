---
name: project-design-system
version: 1.0.0
description: |
  Inventory the visual and component conventions of an existing frontend: design
  tokens, colour, typography, spacing, component library, and the inconsistencies
  between them. Produces docs/project-knowledge/design-system.md. Use when asked
  "document the design system", "what components exist", "what are our tokens",
  "audit UI consistency", or when starting Phase 0 discovery on a project with a
  frontend.
allowed-tools:
  - Read
  - Grep
  - Glob
  - Bash
---

## Project Design System Discovery

Most projects have a design system whether or not anyone decided to build one.
It is expressed in a Tailwind config, a handful of shared components, and a
long tail of one-off values. Document the intended system and measure the drift.

### Confidence marking (mandatory)

`[verified]` (read in config or component source, cited) · `[inferred]`
(a convention you observed repeatedly) · `[assumed]` (Open Questions).

### Method

1. **Find the token source.** Read `tailwind.config.*`, any `theme.ts`, CSS
   custom properties in the global stylesheet, or a tokens package. This is the
   declared system.
   ```bash
   fd -e ts -e js -e css 'tailwind.config|theme|tokens|globals' --max-depth 3
   grep -rn "^\s*--[a-z-]*:" src/ --include=*.css | head -40
   ```

2. **Extract the scales.** Colour, typography, spacing, radius, shadow,
   breakpoints, z-index. For each, record the declared scale and its semantic
   names. A scale with names like `gray-1..9` is a primitive scale; one with
   `surface`, `border`, `danger` is semantic. Note which you have — it determines
   how theming works.

3. **Measure the drift.** Count hard-coded values that bypass the tokens. This is
   the single most useful number in the document:
   ```bash
   grep -rnE "#[0-9a-fA-F]{3,8}\b" src/ --include=*.tsx | wc -l
   grep -rnE "\b(p|m|gap|w|h)-\[[0-9]" src/ --include=*.tsx | wc -l
   grep -rnE "style=\{\{" src/ --include=*.tsx | wc -l
   ```
   Report counts with the worst offending files.

4. **Inventory the components.** List shared/primitive components, their props,
   and their variants. Then find duplicates — two Button implementations, three
   Modals, a Card that exists in four slightly different forms. Duplicates are the
   main finding of this exercise.

5. **Record the interaction states.** For each primitive: default, hover, focus,
   active, disabled, loading, error. Missing focus states are an accessibility
   defect, not a cosmetic gap — note them explicitly.

6. **Check dark mode and theming.** Whether it exists, how it is switched, and
   whether tokens are defined for both. A colour defined only inside a dark-mode
   block is a bug waiting to happen.

7. **Determine the responsive strategy.** Breakpoints, mobile-first or not, and
   whether layouts actually adapt. For a point-of-sale or admin system, record
   the real target devices — a till screen and a phone are different constraints.

8. **Check accessibility basics.** Colour contrast on the declared palette
   against WCAG 2.2 AA (4.5:1 body text, 3:1 large text and UI boundaries),
   focus visibility, and whether interactive elements are real buttons and links.

### Output

Write `docs/project-knowledge/design-system.md`:

```markdown
# Design System

## 1. Token Source    — where tokens live, primitive vs semantic
## 2. Scales          — colour, type, spacing, radius, shadow, breakpoints
## 3. Drift Metrics   — hard-coded values, worst files    ⚠️ the headline number
## 4. Components      — inventory with variants and props
## 5. Duplicates      — components implemented more than once
## 6. States          — coverage table; missing focus states flagged
## 7. Theming         — dark mode, switching mechanism, token coverage
## 8. Responsive      — breakpoints and real target devices
## 9. Accessibility   — contrast failures, focus, semantic elements
## Open Questions
```

### Finishing

- Append durable facts to `$AGENT_DIR/memory.md`.
- `propose` Open Questions to `god`.
- Report duplicates and contrast failures as findings, but do not fix them here —
  consolidation is a separate, reviewed work item.

## References

- **W3C Design Tokens Community Group format specification** — primitive vs
  semantic token distinction in step 2
  <https://tr.designtokens.org/format/>
- **WCAG 2.2, Success Criteria 1.4.3 / 1.4.11 / 2.4.7** — the contrast ratios and
  focus visibility requirements in steps 5 and 8
  <https://www.w3.org/TR/WCAG22/>
- **Tailwind CSS documentation — Theme Configuration** — token source locations
  in step 1 <https://tailwindcss.com/docs/theme>
- **Brad Frost, _Atomic Design_** — the primitive/component inventory approach in
  step 4
- **Munder Difflin `PROTOCOL.md`** — memory write-back and `propose` handoff

**Not sourced — added deliberately:** the confidence marking and the drift-metric
counting in step 3, which is a measurement convention rather than a standard.
