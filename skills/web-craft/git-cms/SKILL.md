---
name: git-cms
version: 1.0.0
description: |
  Give a non-technical client a visual editor for a static site backed by git —
  configuration, authentication, media handling, and keeping the CMS in step with
  the content schema. Use when setting up content editing, when the client cannot
  update the site, or when choosing a CMS for a static build.
allowed-tools:
  - Read
  - Write
  - Edit
  - Grep
  - Glob
  - Bash
---

## Git-Based CMS

A git-based CMS writes markdown to the repository through a visual editor. There
is no database, no server to patch, and no hosting cost — and every content
change is a commit with an author and a diff, which is a better audit trail than
most database CMSs provide.

For a portfolio site this is close to the ideal: **the client gets a login and a
form; the developer gets version-controlled content.**

> **Before running anything:** load `content-collections`. The CMS config is a
> *second description of the same model*, and the two drifting is the single
> largest maintenance cost of this approach.

### Choosing

| Option | Status | Notes |
|---|---|---|
| **[Sveltia CMS](https://github.com/sveltia/sveltia-cms)** (MIT) | **Actively developed; recommended** | Reads Decap/Netlify CMS config as-is. ~5× smaller bundle, first-class i18n, real mobile support. Reached production use through 2025 with v1.0 targeted for early 2026 |
| **[Decap CMS](https://decapcms.org/)** (MIT) | Maintenance has been intermittent | Its config format is the de-facto standard and Sveltia consumes it. Cite it for the schema; prefer Sveltia as the runtime |
| Payload, Directus | Fine, but need a database and a server | Out of scope for a static portfolio |
| Editing markdown in GitHub directly | Free, zero setup | Realistic only if the client is technical. They are usually not |

**Recommendation: Sveltia CMS, configured with the Decap config format.** That
keeps the migration path open in both directions — the config is portable.

### Setup

```html
<!-- public/admin/index.html -->
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Content — Studio</title>
    <meta name="robots" content="noindex">
  </head>
  <body>
    <script src="https://unpkg.com/@sveltia/cms/dist/sveltia-cms.js" type="module"></script>
  </body>
</html>
```

**Pin the version rather than tracking latest**, and prefer a self-hosted copy —
an unpinned CDN script in the admin route is a supply-chain exposure. `noindex`
so the admin page never appears in search results.

### Configuration

```yaml
# public/admin/config.yml
backend:
  name: github
  repo: owner/photography-site
  branch: main
  base_url: https://your-oauth-proxy.example.com   # or Netlify/Cloudflare auth

media_folder: "src/assets/events"
public_folder: "/src/assets/events"

collections:
  - name: events
    label: "Events"
    label_singular: "Event"
    folder: "src/content/events"
    create: true
    slug: "{{year}}-{{month}}-{{slug}}"
    summary: "{{title}} — {{eventType}}, {{date | date('MMM YYYY')}}"
    sortable_fields: [date, title]
    fields:
      - { name: title, label: "Title", widget: string, pattern: ['^.{3,60}$', "3–60 characters"] }
      - { name: eventType, label: "Event type", widget: select,
          options: [wedding, sangeet, mehendi, corporate, birthday, civic] }
      - { name: emotion, label: "Emotional register", widget: select,
          options: [reverence, exuberance, competence, delight, gravitas],
          hint: "Sets pace and colour. See the creative brief." }
      - { name: date, label: "Event date", widget: datetime, format: "YYYY-MM-DD" }
      - { name: location, label: "Location", widget: string }

      - { name: hero, label: "Hero image", widget: image, choose_url: false }
      - { name: heroAlt, label: "Hero description", widget: string,
          hint: "Describe what is happening, for screen readers. At least 10 characters.",
          pattern: ['^.{10,}$', "Write a real description"] }

      - { name: brief, label: "The brief", widget: text,
          hint: "What the client asked for, in their words. 40–70 words." }
      - { name: problem, label: "The problem", widget: text,
          hint: "One specific difficulty and how it showed up. Required — no problem, no case study." }
      - { name: decisions, label: "What we did", widget: list, min: 3, max: 5,
          field: { name: decision, widget: string },
          hint: "3–5 decisions. Verb + object. Not 'managed logistics'." }

      - name: gallery
        label: "Gallery"
        widget: list
        min: 9
        max: 14
        fields:
          - { name: src, label: "Image", widget: image }
          - { name: alt, label: "Description", widget: string, pattern: ['^.{10,}$', "Required"] }
          - { name: role, label: "Role", widget: select,
              options: [establishing, hero, detail, human, scale, closing] }

      - name: mediaConsent
        label: "Consent — required before publishing"
        widget: object
        fields:
          - { name: clientContract, label: "Contract reference", widget: string }
          - { name: grantedOn, label: "Consent date", widget: datetime, format: "YYYY-MM-DD" }
          - { name: verified, label: "I confirm consent is on file", widget: boolean, default: false }

      - { name: draft, label: "Draft", widget: boolean, default: true }
      - { name: featured, label: "Feature on home page", widget: boolean, default: false }
```

**Write `hint` text for every non-obvious field.** The hints *are* the client's
documentation — they will not read a separate manual, and a hint at the point of
entry is read every time.

### Keeping the CMS and the schema in step

This is the real maintenance burden. Two files describe one model, and nothing
enforces agreement.

Practices that help:

- **Change both in the same commit.** Always.
- **Keep field names identical** between the Zod schema and the CMS config.
- **Let the build be the backstop.** If a field is required by Zod but optional
  in the CMS, the editor saves happily and the *deploy* fails. That is a bad
  experience — mirror `required: true` and the `pattern` constraints in the CMS
  so it is caught in the editor.
- **Add a CI check** that both files list the same fields:

```bash
# Rough drift check — field names present in one file but not the other
diff \
  <(grep -oE '^\s+(name):\s*\w+' public/admin/config.yml | awk '{print $2}' | sort -u) \
  <(grep -oE '^\s+\w+:' src/content.config.ts | tr -d ' :' | sort -u)
```

### Authentication

The GitHub backend needs an OAuth proxy — the browser cannot hold a client
secret. Options: a Netlify/Cloudflare Pages auth function, or a small self-hosted
proxy.

- **Never put a client secret in `config.yml`.** It is served publicly.
- **Give the client a GitHub account with write access to that repo only.**
- **Sveltia also supports a local backend** for development:
  `local_backend: true` with `npx sveltia-cms-proxy-server`.

### Editorial workflow and deploys

`publish_mode: editorial_workflow` gives draft → review → publish via pull
requests. **Consider whether the client wants this.** For a two-person business
it is usually friction; the `draft` boolean is enough.

A content change is a commit, so a rebuild must follow:

- Configure the host to build on push to `main`.
- **Tell the client the site takes a few minutes to update.** Not knowing this,
  they will save repeatedly, thinking it failed. Say it in a hint on the
  publish screen if the CMS allows.

### Detection

```bash
grep -rn 'client_secret\|api_key' public/admin/config.yml            # must be empty
grep -n 'noindex' public/admin/index.html                            # admin not indexed
grep -c 'hint:' public/admin/config.yml                              # hints present
grep -n 'unpkg.com\|cdn.jsdelivr' public/admin/index.html | grep -v '@[0-9]'  # unpinned
grep -n 'media_folder' public/admin/config.yml                       # must be src/assets
```

**`media_folder` must point at `src/assets`, not `public`** — otherwise every
image the client uploads bypasses the optimisation pipeline. This is the most
consequential single line in the config; see `asset-workflow`.

### Caveats

- **The client will upload 8 MB photographs straight from a phone.** The CMS does
  not resize on upload. Either add an upload-size limit, or run a pre-commit
  resize, or accept slow builds. Decide before handover.
- **Media in git grows forever.** See `asset-workflow`.
- **Sveltia is pre-1.0.** It is in production use and actively developed, but pin
  the version and test upgrades on a branch.
- **Train the client on the consent field.** A tickbox they do not understand is
  worse than no tickbox — see `media-consent`.
- **Editorial workflow needs a GitHub account per editor.** For one or two
  editors it is fine; beyond that, reconsider.

### Checklist

- [ ] Sveltia CMS chosen, with Decap-format config for portability
- [ ] CMS script version pinned, ideally self-hosted
- [ ] `/admin` set to `noindex`
- [ ] No secrets in `config.yml`
- [ ] `media_folder` points at `src/assets`, not `public`
- [ ] Every field mirrors the Zod schema by name and constraint
- [ ] `hint` written for every non-obvious field
- [ ] Consent object present and required
- [ ] Drift check between config and schema in CI
- [ ] OAuth proxy configured; client has scoped repo access
- [ ] Build-on-push wired up
- [ ] Client told the site updates in minutes, not instantly
- [ ] Upload size handled — limit, pre-commit resize, or accepted
- [ ] Client walked through creating one case study end to end

## References

- **Sveltia CMS** (MIT) — configuration, Decap compatibility, i18n, local backend
  <https://github.com/sveltia/sveltia-cms>
- **Decap CMS — configuration options and widget reference**, the config schema
  Sveltia consumes <https://decapcms.org/docs/configuration-options/>
- **Decap CMS — Editorial workflow**
  <https://decapcms.org/docs/editorial-workflows/>
- **Astro docs — CMS integrations overview**
  <https://docs.astro.build/en/guides/cms/>
- **Astro docs — Images**, on why `media_folder` must resolve inside `src/`
  <https://docs.astro.build/en/guides/images/#where-to-store-images>
- **`content-collections`, `media-consent`, `asset-workflow`** (this framework)

**Not sourced — written for this framework:** the CMS comparison and the
Sveltia-with-Decap-config recommendation, the schema-drift practices and CI
check, the `media_folder` warning, the hints-as-documentation rule, and the
client-handover caveats.
