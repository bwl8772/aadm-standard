# UDALI ↔ AUTH enforcement mapping

> Normative table mapping each UDALI L-layer to its primary AUTH controls — where in the architecture each obligation is enforced. Stack-agnostic.

## Purpose

[UDALI](../docs/udali-22-layer-model.md) tells you **where code belongs**. The [AUTH catalog](auth-catalog.md) tells you **what obligations must be enforced**. This document bridges them: for each layer, which AUTH controls are *primarily* enforced there.

This is not a substitute for designing per-feature placement — it is a default mapping that adopters can ratify or override in their internal standard.

---

## Layer responsibility matrix

> **Candidate controls.** AUTH-44..47, AUTH-53..54 (Agentic AI Behavior) and AUTH-48..52, AUTH-55 (AI Assurance) are CANDIDATE — enforceable now; mapped here so placement is declared from day one, flagged *(c)* in the matrix until ratification stabilizes their wording.

| Layer | Name | Responsibility | Forbidden behaviors | Primary AUTH controls |
|-------|------|----------------|---------------------|------------------------|
| L1 | UI Implementation | Render UI, handle interaction | Business logic; direct validation of backend rules | AUTH-19 |
| L2 | Adapter (UI↔DTO) | UI ↔ DTO shaping | Business rules | AUTH-09 |
| L3 | UI Guards | Route protection, UX safety | Data mutation, backend orchestration | AUTH-19, AUTH-23 |
| L4 | Database | Persistence, constraints | Business logic | AUTH-03, AUTH-04, AUTH-05 |
| L5 | Entity | Typed persistence models | External calls | AUTH-04, AUTH-05 |
| L6 | Domain | Pure business rules, invariants | I/O, DB calls | AUTH-06, AUTH-07 |
| L7 | DTO | Contracts, validation | Business logic | AUTH-10, AUTH-18, AUTH-21, AUTH-54 *(c)* |
| L8 | Mapper | Entity ↔ DTO transforms | Business decisions | AUTH-21 |
| L9 | Repository Interface | Data access contracts | Business logic | AUTH-17 |
| L10 | Repository | Persistence implementation | Business rules | AUTH-17, AUTH-27 |
| L11 | Auth | Identity, RBAC, tenancy | Business logic | AUTH-16, AUTH-32, AUTH-33, AUTH-34, AUTH-53 *(c)* |
| L12 | Cache | Performance | Source-of-truth logic | AUTH-11 |
| L13 | Service | Orchestration, workflows | UI logic; DTO schema definition | AUTH-06, AUTH-12, AUTH-13, AUTH-14, AUTH-15, AUTH-55 *(c)* |
| L14 | Events | Async, messaging | Core decision authority | AUTH-25, AUTH-26, AUTH-28, AUTH-29 |
| L15 | AI Services | Model calls, prompts | Unvalidated output | AUTH-35, AUTH-36, AUTH-37, AUTH-44 *(c)*, AUTH-45 *(c)*, AUTH-46 *(c)*, AUTH-47 *(c)*, AUTH-49 *(c)* |
| L16 | Integrations | External APIs | Business rules | AUTH-32, AUTH-38, AUTH-39 |
| L17 | Middleware | Cross-cutting | Business logic | AUTH-30, AUTH-32, AUTH-38 |
| L18 | Controller | HTTP / RPC boundary | Business rules | AUTH-15, AUTH-21 |
| L19 | Routes | Wiring | Logic execution | — |
| L20 | Gateway | Exposure, policies | Business logic | AUTH-20, AUTH-38, AUTH-40 |
| L21 | QA / Verification | Tests | Runtime logic | AUTH-24, AUTH-48 *(c)*, AUTH-50 *(c)*, AUTH-51 *(c)* |
| L22 | System Integration / Release | CI/CD, release gates | Application logic | AUTH-41, AUTH-43, AUTH-52 *(c)* |

---

## Enforcement by UDALI zone

### L1–L3 — UI / Unifier

**Primary enforcement.** UI guards, authority-safe exposure of capabilities, UX states that mirror server truth.

**Key controls.** AUTH-19 (UI Authority Exposure), AUTH-23 (Client State / Hooks).

**Forbidden.** No API orchestration, no domain rules, no authoritative validation in the UI.

### L4–L8 — Data / Designer

**Primary enforcement.** Schema validation, DTO validation, mapper correctness, domain invariants.

**Key controls.** AUTH-10 (Spec Version), AUTH-18 (L2 Content Inputs), AUTH-21 (DTO/Contract Boundary), AUTH-54 (Tool Contract — an agent tool's schema is a DTO contract; candidate).

**Forbidden.** No workflow orchestration, no HTTP handlers, no external calls from domain.

### L9–L12 — Access / Adapter

**Primary enforcement.** Repository boundaries, RBAC, tenancy, caching correctness, staleness signals.

**Key controls.** AUTH-11 (Staleness), AUTH-16 (Tenancy), AUTH-17 (Sync State), AUTH-32 (Policy Enforcement), AUTH-53 (Agent Scope — the authority envelope is enforced where identity is; candidate).

**Forbidden.** No lifecycle or approval business rules in repositories (orchestrate in L13 instead).

### L13–L16 — Business Logic / Logician

**Primary enforcement.** Lifecycle transitions, approvals, AI output validation, integration boundaries, async work.

**Key controls.** AUTH-06 (Lifecycle), AUTH-14 (Approval), AUTH-25 (Idempotency), AUTH-35..37 (AI behavior), AUTH-38 (Rate Limiting), AUTH-44..47 (agentic AI candidates), AUTH-49 (Claim Grounding), AUTH-55 (Automation Workflow Gate — the automation's state machine lives with orchestration; both candidates).

**Forbidden.** No DTO schema ownership; no raw unvalidated AI output reaching clients.

### L17–L20 — API / Integrator

**Primary enforcement.** Middleware cross-cutting, controller boundaries, routing, readiness gates, rate limiting at the edge.

**Key controls.** AUTH-20 (Readiness), AUTH-30 (Traceability), AUTH-32, AUTH-38, AUTH-40 (Latency Budget).

**Forbidden.** No new domain rules in controllers; wire and gate only.

### L21–L22 — QA / Release / Integrator

**Primary enforcement.** Test gates, release gates, environment isolation.

**Key controls.** AUTH-24 (Verification), AUTH-41 (Environment Isolation), AUTH-43 (Release Gate), AUTH-48 (Output Evaluation), AUTH-50 (Completion Verification), AUTH-51 (Regression Corpus), AUTH-52 (AI Change Qualification — qualification is a release-authority act; all candidates).

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

An **agent run** (agentic automation), same convention — candidates flagged:

```
Goal accepted        (AUTH-53 envelope resolved; run record opened per AUTH-29/30)
   → L13 Service     (AUTH-55 workflow state admits the run)
   → L15 AI Services (model proposes a step)
   → L11 Auth        (AUTH-44 invocation authorized against the AUTH-53 envelope)
   → L7  Tool DTO    (AUTH-54 contract validates arguments)
   → effect executes (AUTH-25 idempotency; AUTH-14 gate if irreversible)
   → L15             (result re-enters context as data per AUTH-46; AUTH-45 budgets checked)
   → loop until a declared terminal state
   → L21 Verification (AUTH-48 output evaluated; AUTH-50 completion verified; AUTH-49 claims grounded)
   → L13              (AUTH-55 gate advances the workflow on recorded results)
   → failures captured to the corpus per AUTH-51
   → L22 configuration changes re-qualify per AUTH-52
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
