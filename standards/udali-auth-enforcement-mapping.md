# UDALI ↔ AUTH enforcement mapping

> Normative table mapping each UDALI L-layer to its primary AUTH controls — where in the architecture each obligation is enforced. Stack-agnostic.

## Purpose

[UDALI](../docs/udali-22-layer-model.md) tells you **where code belongs**. The [AUTH catalog](auth-catalog.md) tells you **what obligations must be enforced**. This document bridges them: for each layer, which AUTH controls are *primarily* enforced there.

This is not a substitute for designing per-feature placement — it is a default mapping that adopters can ratify or override in their internal standard.

---

## Layer responsibility matrix

| Layer | Name | Responsibility | Forbidden behaviors | Primary AUTH controls |
|-------|------|----------------|---------------------|------------------------|
| L1 | UI Implementation | Render UI, handle interaction | Business logic; direct validation of backend rules | AUTH-19 |
| L2 | Adapter (UI↔DTO) | UI ↔ DTO shaping | Business rules | AUTH-09 |
| L3 | UI Guards | Route protection, UX safety | Data mutation, backend orchestration | AUTH-19, AUTH-23 |
| L4 | Database | Persistence, constraints | Business logic | AUTH-03, AUTH-04, AUTH-05 |
| L5 | Entity | Typed persistence models | External calls | AUTH-04, AUTH-05 |
| L6 | Domain | Pure business rules, invariants | I/O, DB calls | AUTH-06, AUTH-07 |
| L7 | DTO | Contracts, validation | Business logic | AUTH-10, AUTH-18, AUTH-21 |
| L8 | Mapper | Entity ↔ DTO transforms | Business decisions | AUTH-21 |
| L9 | Repository Interface | Data access contracts | Business logic | AUTH-17 |
| L10 | Repository | Persistence implementation | Business rules | AUTH-17, AUTH-27 |
| L11 | Auth | Identity, RBAC, tenancy | Business logic | AUTH-16, AUTH-32, AUTH-33, AUTH-34 |
| L12 | Cache | Performance | Source-of-truth logic | AUTH-11 |
| L13 | Service | Orchestration, workflows | UI logic; DTO schema definition | AUTH-06, AUTH-12, AUTH-13, AUTH-14, AUTH-15 |
| L14 | Events | Async, messaging | Core decision authority | AUTH-25, AUTH-26, AUTH-28, AUTH-29 |
| L15 | AI Services | Model calls, prompts | Unvalidated output | AUTH-35, AUTH-36, AUTH-37, AUTH-44, AUTH-45, AUTH-46, AUTH-47 |
| L16 | Integrations | External APIs | Business rules | AUTH-32, AUTH-38, AUTH-39 |
| L17 | Middleware | Cross-cutting | Business logic | AUTH-30, AUTH-32, AUTH-38 |
| L18 | Controller | HTTP / RPC boundary | Business rules | AUTH-15, AUTH-21 |
| L19 | Routes | Wiring | Logic execution | — |
| L20 | Gateway | Exposure, policies | Business logic | AUTH-20, AUTH-38, AUTH-40 |
| L21 | QA / Verification | Tests | Runtime logic | AUTH-24 |
| L22 | System Integration / Release | CI/CD, release gates | Application logic | AUTH-41, AUTH-43 |

---

## Enforcement by UDALI zone

### L1–L3 — UI / Unifier

**Primary enforcement.** UI guards, authority-safe exposure of capabilities, UX states that mirror server truth.

**Key controls.** AUTH-19 (UI Authority Exposure), AUTH-23 (Client State / Hooks).

**Forbidden.** No API orchestration, no domain rules, no authoritative validation in the UI.

### L4–L8 — Data / Designer

**Primary enforcement.** Schema validation, DTO validation, mapper correctness, domain invariants.

**Key controls.** AUTH-10 (Spec Version), AUTH-18 (L2 Content Inputs), AUTH-21 (DTO/Contract Boundary).

**Forbidden.** No workflow orchestration, no HTTP handlers, no external calls from domain.

### L9–L12 — Access / Adapter

**Primary enforcement.** Repository boundaries, RBAC, tenancy, caching correctness, staleness signals.

**Key controls.** AUTH-11 (Staleness), AUTH-16 (Tenancy), AUTH-17 (Sync State), AUTH-32 (Policy Enforcement).

**Forbidden.** No lifecycle or approval business rules in repositories (orchestrate in L13 instead).

### L13–L16 — Business Logic / Logician

**Primary enforcement.** Lifecycle transitions, approvals, AI output validation, integration boundaries, async work.

**Key controls.** AUTH-06 (Lifecycle), AUTH-14 (Approval), AUTH-25 (Idempotency), AUTH-35..37 (AI behavior), AUTH-38 (Rate Limiting), AUTH-44..47 (agentic AI candidates).

**Forbidden.** No DTO schema ownership; no raw unvalidated AI output reaching clients.

### L17–L20 — API / Integrator

**Primary enforcement.** Middleware cross-cutting, controller boundaries, routing, readiness gates, rate limiting at the edge.

**Key controls.** AUTH-20 (Readiness), AUTH-30 (Traceability), AUTH-32, AUTH-38, AUTH-40 (Latency Budget).

**Forbidden.** No new domain rules in controllers; wire and gate only.

### L21–L22 — QA / Release / Integrator

**Primary enforcement.** Test gates, release gates, environment isolation.

**Key controls.** AUTH-24 (Verification), AUTH-41 (Environment Isolation), AUTH-43 (Release Gate).

**Forbidden.** No application logic in release pipelines.

---

## Primary vs supporting enforcement

For most AUTH controls, the *risk* is introduced or transformed at one specific layer (the **primary** enforcement layer), but additional layers may apply **supporting** checks (defense in depth).

Example: AUTH-21 (DTO/Contract Boundary) is **primarily** enforced at L7 (DTO) but is **supported** by checks at L18 (Controller) and L20 (Gateway).

When citing an enforcement gap in a finding, name the primary layer where the obligation should have applied.

---

## Example enforcement flow

A typical authoritative read-or-write request, in execution order rather than ID order:

```
Request
   → L19 Routes
   → L20 Gateway        (AUTH-20, AUTH-38, AUTH-40)
   → L17 Middleware     (AUTH-30, AUTH-32, AUTH-38)
   → L18 Controller     (AUTH-15, AUTH-21)
   → L7  DTO            (AUTH-21)
   → L11 Auth           (AUTH-16, AUTH-32, AUTH-33, AUTH-34)
   → L13 Service        (AUTH-06, AUTH-14, AUTH-25, AUTH-27)
   → L6  Domain         (AUTH-06, AUTH-07)
   → L10 Repository     (AUTH-17, AUTH-27)
   → L4  DB             (AUTH-03, AUTH-04, AUTH-05)
                       ↳ writes audit log per AUTH-29
                       ↳ emits trace per AUTH-30
   → L21 QA verifies behavior in CI per AUTH-24
   → L22 Release gates exposure per AUTH-43
```

Numeric AUTH IDs are stable identifiers; **execution order follows this pipeline, not ID sort order**.

---

## Forbidden behaviors by band

- **Unifier (L1–L3).** No API orchestration; no domain rules in UI.
- **Designer (L4–L8).** No workflow orchestration; no HTTP handlers.
- **Adapter (L9–L12).** No lifecycle or approval business rules — orchestrate in L13.
- **Logician (L13–L16).** No DTO schema ownership; no raw unvalidated AI output to clients.
- **Integrator (L17–L22).** No new domain rules — wire and gate only.

---

## How adopters use this mapping

1. **At Navigator Scope.** When mapping a planned change to layers, this table is the starting point for "which AUTH should I be enforcing?"
2. **At Investigator Stage 2.** Use this table to name the *primary* layer where the defect's AUTH obligation should have been enforced.
3. **At Sentinel Stage 1.** Use this table to scope which layers are in scope for a vertical trace.
4. **In code review.** When a reviewer asks "where should this rule live?", this table answers — to a first approximation.

This mapping is a default. Adopters may override per AUTH or per layer; overrides should be written down.

---

## Related

- [`auth-catalog.md`](auth-catalog.md) — full AUTH catalog
- [UDALI 22-layer model](../docs/udali-22-layer-model.md) — layer definitions
- [UDALI personas](../docs/udali-personas.md) — five collaboration bands
- [`udali-layer-boundaries.md`](udali-layer-boundaries.md) — boundary discipline
