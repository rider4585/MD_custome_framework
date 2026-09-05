---
name: excessive-data-exposure
version: 1.0.0
description: |
  Detect API responses that return more data than the client needs — internal
  fields, cost prices, password hashes, staff PII — on the assumption the UI will
  filter it. Use when reviewing any endpoint response, serializer, or ORM include,
  or when asked "are we leaking data". OWASP API3:2023, CWE-213.
allowed-tools:
  - Read
  - Grep
  - Glob
  - Bash
---

## Excessive Data Exposure (API3:2023 / CWE-213)

An endpoint returns the whole record and relies on the client to display only
part of it. Anyone can open developer tools or call the API directly, so every
returned field is disclosed regardless of what the UI shows.

### The retail fields that matter

In an inventory and POS system, these should never reach a low-privilege client:

| Field | Why it matters |
|---|---|
| `costPrice`, `margin`, `supplierPrice` | Commercially sensitive; a cashier can compute markup, a customer-facing surface leaks it to competitors |
| `passwordHash`, `resetToken`, `mfaSecret` | Credential material |
| Staff `email`, `phone`, `salary`, `address` | Personal data, likely regulated |
| Customer contact details on a shared till | Exposed to every cashier |
| Internal ids, `deletedAt`, `internalNotes` | Enables enumeration and leaks process |
| Other shops' identifiers via relations | Cross-tenant disclosure |

### Detection

```bash
# Whole-entity returns
grep -rnE "res\.(json|send)\(\s*(user|product|sale|customer|entity|result)\s*\)" src/ --include=*.ts
# ORM includes that pull relations wholesale
grep -rnE "include:\s*\{|relations:\s*\[|populate\(" src/ --include=*.ts
# Absence of field selection
grep -rnE "find(Many|All|First|Unique)\(" src/ -A 4 --include=*.ts | grep -v "select:"
```

Then compare each response shape against what the consuming screen actually
renders.

### Remediation

**Select explicitly at the query.** Cheapest and safest — the sensitive field
never enters the process.

```ts
const products = await db.product.findMany({
  where: { shopId: user.shopId },
  select: { id: true, name: true, unitPrice: true },   // costPrice absent
});
```

**Serialise through an explicit response DTO.** Never return an entity directly.
Map to a response type whose fields are enumerated in code.

**Make exclusion structural, not incidental.** With `class-transformer`, mark
sensitive fields `@Exclude()` and enable `excludeExtraneousValues`. A field that
must be opted *in* cannot leak by forgetting.

**Vary the shape by role.** A manager's product response legitimately includes
cost; a cashier's does not. Build separate response mappers rather than one
conditional shape — conditionals are where the leak returns.

### Commonly missed places

- Error responses embedding the offending entity
- Log output and audit records
- Nested relations pulled by an `include`
- List endpoints, which get less scrutiny than detail endpoints
- CSV and PDF exports — usually built from a different, unreviewed query
- WebSocket and server-sent event payloads

### Severity

`CRITICAL` for credential material or cross-tenant data. `HIGH` for cost prices
reaching a non-privileged role, and for staff or customer PII. `MEDIUM` for
internal metadata.

### Checklist

- [ ] No endpoint returns an ORM entity directly
- [ ] Every query selects fields explicitly
- [ ] Response DTOs enumerate fields; sensitive ones excluded by default
- [ ] Response shape differs by role where the data does
- [ ] Nested relations reviewed for over-fetching
- [ ] List endpoints reviewed as carefully as detail endpoints
- [ ] Exports and streams reviewed
- [ ] Error responses contain no entity data
- [ ] Test asserts sensitive fields are absent

## References

- **OWASP API Security Top 10 (2023) — API3 Broken Object Property Level
  Authorization** (excessive exposure half)
  <https://owasp.org/API-Security/editions/2023/en/0xa3-broken-object-property-level-authorization/>
- **CWE-213** — intentional information exposure
  <https://cwe.mitre.org/data/definitions/213.html>
- **OWASP REST Security Cheat Sheet** — response filtering guidance
  <https://cheatsheetseries.owasp.org/cheatsheets/REST_Security_Cheat_Sheet.html>
- **NestJS documentation — Serialization (`@Exclude`,
  `excludeExtraneousValues`)** <https://docs.nestjs.com/techniques/serialization>

**Not sourced — written for this framework:** the retail sensitive-field table,
the detection commands, the role-varying response recommendation, and the
commonly-missed list.
