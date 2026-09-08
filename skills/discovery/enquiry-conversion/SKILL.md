---
name: enquiry-conversion
version: 1.0.0
description: |
  Turn a visitor into a conversation — CTA placement, form design, WhatsApp and
  phone as first-class channels, and what to ask. Use when designing the contact
  path, when the site gets traffic but no enquiries, or when the form is long.
allowed-tools:
  - Read
  - Write
  - Grep
  - Glob
  - Bash
---

## Enquiry Conversion

Everything else in this framework exists to produce this moment. A site that is
beautiful, fast, accessible, and does not generate enquiries has failed at the
only thing the client is paying for.

The governing insight for this market: **the enquiry is a conversation, not a
transaction.** Nobody books a wedding planner from a web form. The form's job is
to start a phone call.

> **Before running anything:** ask the client how enquiries reach them today, and
> which channel they answer fastest. Building a beautiful form for a business
> that lives on WhatsApp is a common and expensive mistake.

### Method

1. **Establish the channels** the client actually answers.
2. **Place CTAs** where intent peaks, not only at the end.
3. **Cut the form to the minimum** that lets them reply usefully.
4. **Set expectations in the copy** — who replies, and when.
5. **Measure enquiries by channel**, and iterate on the drop-off.

### Channels — WhatsApp is not secondary

For a service business in India, ranked by likely volume:

| Channel | Notes |
|---|---|
| **WhatsApp** | Often the highest-volume channel. Low commitment, familiar, asynchronous. **Give it equal or greater prominence than the form** |
| **Phone** | High intent. A `tel:` link that dials on tap |
| **Form** | For people who prefer not to speak first, and for after-hours |
| **Instagram DM** | Already happening; link the profile |
| Email | Lowest volume here, but expected on a premium site |

```html
<a href="https://wa.me/91XXXXXXXXXX?text=Hi%20the studio%2C%20I%27m%20planning%20a%20wedding%20in%20"
   class="cta-whatsapp">WhatsApp us</a>

<a href="tel:+91XXXXXXXXXX" class="cta-phone">Call +91 XXXXX XXXXX</a>
```

**Prefill the WhatsApp message.** A blank chat makes the visitor compose an
opener, which is friction at exactly the wrong moment. A half-written sentence
they complete converts better.

**Never make the phone number an image or plain text.** It must be tappable.

### CTA placement

Intent peaks after evidence, not at the end of the page.

| Location | CTA |
|---|---|
| Header | Persistent, quiet — "Check your date" |
| After the home hero | Primary |
| **End of every case study** | **Highest-intent moment on the whole site** — they have just seen proof |
| Services page, per service | Contextual — "Ask about wedding planning" |
| Footer | Full contact block: WhatsApp, phone, address, hours |
| Sticky mobile bar | WhatsApp + call, always reachable |

**The end of a case study is the single most valuable CTA position** and it is
the one most often missing. Someone who has read a whole case study is warmer
than anyone who reached the contact page by navigation.

**A sticky bottom bar on mobile with WhatsApp and Call** is worth the screen
space here. Keep it small, keep it out of the way of content, and make sure it
does not obscure focused form fields → `accessibility`.

### The form

**Fewer fields, more enquiries.** Every field is a reason to leave. Ask only what
is needed to reply usefully.

| Field | Required | Why |
|---|---|---|
| Name | Yes | |
| Phone / WhatsApp | Yes | **The only contact field that matters here.** They will be called |
| Event type | Yes — select | Routes the reply |
| Event date | Optional | "Approximate is fine" — many do not know yet |
| Guest count | Optional | Rough range, not a number |
| Message | Optional | Let them say what they want |
| How did you hear about us? | Optional | Attribution → `local-discovery` |
| Email | Optional | Lower value than phone in this market |

**Seven fields, two required.** If the client wants budget, venue, and function
count, push back: those are the first phone call, not the form.

**Do not require email.** In this market phone is the real identifier, and
requiring email loses enquiries.

```html
<form method="post" action="/api/enquiry">
  <label for="name">Your name</label>
  <input id="name" name="name" type="text" autocomplete="name" required>

  <label for="phone">Phone or WhatsApp number</label>
  <input id="phone" name="phone" type="tel" autocomplete="tel" inputmode="tel"
         required aria-describedby="phone-help">
  <p id="phone-help">We will call or message you — whichever you prefer.</p>

  <label for="type">What are you planning?</label>
  <select id="type" name="type" required>
    <option value="">Choose one</option>
    <option>Wedding</option><option>Sangeet or Mehendi</option>
    <option>Corporate event</option><option>Birthday</option><option>Something else</option>
  </select>

  <label for="date">Event date <span>(approximate is fine)</span></label>
  <input id="date" name="date" type="text" inputmode="text" placeholder="e.g. February 2027">

  <p class="hidden-field" aria-hidden="true">
    <label for="company">Company</label>
    <input id="company" name="company" tabindex="-1" autocomplete="off">
  </p>

  <button type="submit">Check your date</button>
  <p>We reply within one working day.</p>
</form>
```

- **`type="tel"` with `inputmode="tel"`** brings up the numeric keypad.
- **`autocomplete` attributes** let a phone fill the form in one tap — a real
  conversion gain and a WCAG requirement → `accessibility`.
- **The date is free text, not a date picker.** Mobile date pickers are painful,
  and the honest answer is often "sometime next February".
- **The honeypot** (`company`) is hidden from users and `aria-hidden` from screen
  readers; bots fill it → `static-deploy`.
- **The button says what happens**, not "Submit" → `web-copywriting`.

### Setting expectations

Trust comes from specificity about what happens next.

- Under the button: **"We reply within one working day."** Then honour it.
- On the thank-you page: what happens next, and the WhatsApp link again in case
  they would rather not wait.
- **Name the person who will reply** if the client is comfortable with it.
  "Sonal will call you" converts better than "our team will be in touch."

### After submission

- **Redirect to a real thank-you page**, not an inline message alone — it gives
  an analytics conversion target and a page to share.
- **Confirm what was received**, including the date they gave.
- **Offer the immediate channel** for anyone who does not want to wait.
- **Do not ask for anything else.** The enquiry is complete.

### Measuring

- Thank-you page views = form conversions
- `tel:` and `wa.me` clicks as tracked events
- "How did you hear about us?" for channel attribution
- **Ask the client how many enquiries became conversations.** Nothing on the site
  measures the thing that actually matters.

```bash
grep -rn 'wa.me\|whatsapp' src/ --include=*.astro | wc -l
grep -rn 'tel:' src/ --include=*.astro | wc -l
grep -rn 'required' src/pages/contact.astro | wc -l        # should be ~3
grep -rn '<input' src/pages/contact.astro | grep -v autocomplete
grep -rn 'type="email".*required' src/ --include=*.astro   # should be empty
```

### Caveats

- **Test the form on the production domain before launch.** CSP `form-action`
  and endpoint allow-lists are domain-specific and pass on staging →
  `launch-review`.
- **A form that is not answered is worse than no form.** Confirm the client has a
  routine for checking enquiries, and that the notification email is not going to
  a mailbox nobody opens.
- **Do not add live chat.** It is a third-party script, a privacy question, and a
  promise of immediacy the client cannot keep → `performance-budget`.
- **Conversion advice here is reasoning from the market, not from this site's
  data.** Once there is traffic, measure and correct.
- **The WhatsApp number must be the business number** and someone must answer it.

### Checklist

- [ ] Channels confirmed with the client, including which they answer fastest
- [ ] WhatsApp given equal or greater prominence than the form
- [ ] WhatsApp link prefilled with an opening message
- [ ] Phone number is a tappable `tel:` link everywhere it appears
- [ ] CTA at the end of every case study
- [ ] Sticky mobile contact bar that does not obscure content or focus
- [ ] Form is seven fields or fewer, with at most three required
- [ ] Email not required
- [ ] `autocomplete` and `inputmode` on every field
- [ ] Event date is free text, not a date picker
- [ ] Honeypot present; no CAPTCHA
- [ ] Reply-time promise stated and honoured
- [ ] Named person replying, if the client agrees
- [ ] Thank-you page with next steps and an immediate channel
- [ ] Attribution question included
- [ ] Form tested on the production domain, email received
- [ ] Client has a routine for answering enquiries

## References

- **Nielsen Norman Group — web form design and the cost of additional fields**
  <https://www.nngroup.com/articles/web-form-design/>
- **Nielsen Norman Group — "Placeholders in Form Fields Are Harmful"**
  <https://www.nngroup.com/articles/form-design-placeholders/>
- **WCAG 2.2 — SC 1.3.5 Identify Input Purpose, SC 3.3.2 Labels or Instructions,
  SC 3.3.7 Redundant Entry**
  <https://www.w3.org/TR/WCAG22/#identify-input-purpose>
- **MDN — `autocomplete` attribute values and `inputmode`**
  <https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Attributes/autocomplete>
- **WhatsApp — click-to-chat link format (`wa.me`) with prefilled text**
  <https://faq.whatsapp.com/5913398998672934>

**Not sourced — written for this framework:** the channel ranking for this
market, the "end of case study is the highest-intent CTA" claim, the seven-field
form specification, the do-not-require-email position, the free-text date
recommendation, and the detection commands.
