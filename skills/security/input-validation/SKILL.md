---
name: input-validation
version: 1.0.0
description: |
  Validate untrusted input at the trust boundary with schemas — type, range,
  length, format, and business constraints — before it reaches application logic.
  Shared by backend and security agents. Use when reviewing DTOs, request
  handlers, form submissions, imports, or when asked "is this input validated".
  CWE-20.
allowed-tools:
  - Read
  - Grep
  - Glob
  - Bash
---

## Input Validation (CWE-20)

Validation is the first of the three controls — validate, authorise, encode. It
constrains *shape*; it does not replace encoding at the sink or authorisation on
the object. Code that validates well and encodes poorly is still injectable.

### Principles

1. **Allow-list, never deny-list.** Define what is acceptable and reject the
   rest. A denylist is defeated by the input nobody thought of.
2. **Validate at the boundary, once, before the handler body runs.** Scattered
   checks inside business logic get missed and drift.
3. **Parse into a typed value, do not merely check.** After validation you should
   hold a `Product`, not an `any` you have inspected. This makes the compiler
   carry the guarantee forward.
4. **Reject unknown properties.** Silently passing extra fields through is how
   mass assignment happens — see `mass-assignment`.
5. **Fail closed and fail loudly.** Return `400` with field-level detail; never
   coerce silently to a default.
6. **Server-side always.** Client validation is UX. It is not a control.

### What to check on every field

| Dimension | Example |
|---|---|
| Type | string, integer, boolean — not "looks numeric" |
| Presence | required vs optional, and what optional means |
| Range | quantity ≥ 0, price ≥ 0, discount 0–100 |
| Length | bounded strings — unbounded text is a memory and storage risk |
| Format | email, UUID, ISO date, barcode pattern |
| Enum | status values constrained to the known set |
| Precision | currency scale, quantity decimals |
| Cross-field | `endDate > startDate`, discount not exceeding line total |

### Schema-first in TypeScript

```ts
const CreateSaleLine = z.object({
  productId: z.string().uuid(),
  quantity:  z.number().int().positive().max(10_000),
  unitPrice: z.number().int().nonnegative(),        // minor units
  discount:  z.number().min(0).max(100).default(0),
}).strict();                                        // unknown keys rejected

type CreateSaleLine = z.infer<typeof CreateSaleLine>;
```

`.strict()` (Zod) or `whitelist: true, forbidNonWhitelisted: true` (NestJS
`ValidationPipe`) is the part that closes the mass-assignment hole. Verify it is
enabled globally, not per-route.

### Retail-specific constraints worth enforcing

These are the ones whose absence causes real financial damage:

- Quantity is a **positive** integer — a negative quantity on a sale line can
  invert a total or manufacture stock.
- Money is an integer in minor units — reject floats at the boundary.
- Discount bounded 0–100 **and** checked against a floor so it cannot drive price
  below cost.
- Dates bounded to a sane window — a sale dated 1970 or 2099 corrupts reporting.
- Barcode and SKU matched to a fixed pattern before any lookup.

### Detection

```bash
# Handlers taking raw body with no schema
grep -rnE "(req\.body|req\.query|req\.params)" src/ --include=*.ts | grep -v "parse\|validate\|dto"
# Unsafe coercion
grep -rnE "(parseInt|parseFloat|Number)\(\s*(req|query|params|body)\." src/ --include=*.ts
# Global validation configured?
grep -rnE "ValidationPipe|whitelist:\s*true|forbidNonWhitelisted" src/ --include=*.ts
```

`Number(req.query.x)` yields `NaN` on bad input and silently propagates — always
a finding unless immediately checked.

### Where validation is commonly missing

Route bodies get validated; these usually do not:
- Query parameters and path parameters
- CSV and bulk import rows
- Webhook payloads from third parties
- Message queue consumers
- Data read back from the database after an unvalidated write

### Severity

`HIGH` where missing validation reaches a query, a shell, or money/stock
arithmetic. `MEDIUM` for unbounded length or missing format checks. Rate by what
the unvalidated value can reach, not by the field itself.

### Checklist

- [ ] Every entry point has a boundary schema
- [ ] Schemas allow-list and reject unknown properties
- [ ] Validation produces a typed value, not a checked `any`
- [ ] Numeric fields bounded; money is integer minor units
- [ ] Quantities constrained positive
- [ ] Cross-field rules expressed in the schema
- [ ] Query params, imports, webhooks, and consumers validated too
- [ ] No silent coercion or default fallback
- [ ] Errors return `400` with field detail

## References

- **OWASP Input Validation Cheat Sheet** — allow-listing and boundary validation
  <https://cheatsheetseries.owasp.org/cheatsheets/Input_Validation_Cheat_Sheet.html>
- **OWASP ASVS 4.0, V5 Validation, Sanitization and Encoding**
  <https://owasp.org/www-project-application-security-verification-standard/>
- **CWE-20** <https://cwe.mitre.org/data/definitions/20.html>
- **Zod documentation** — schema parsing and `.strict()` <https://zod.dev/>
- **NestJS documentation — Validation** — `ValidationPipe` options
  <https://docs.nestjs.com/techniques/validation>

**Not sourced — written for this framework:** the retail constraint list, the
detection commands, the parse-don't-check framing, and the commonly-missing list.
