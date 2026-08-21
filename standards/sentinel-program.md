# Sentinel program — attestation closure, audit programs, evidence binding

> Normative description of how Sentinel assurance work is shaped. Builds on [`three-lanes.md`](three-lanes.md) (Sentinel as one of three lanes) and the [SEAM](seam-catalog.md) / [AUTH](auth-catalog.md) / [TRACE](trace-catalog.md) catalogs. Stack-agnostic.

## The Sentinel mandate

Sentinel proves alignment with the standard for **existing** posture. It is the lane that turns scattered "are we aligned?" questions into a deterministic pass with declared scope, bound evidence, and a human attestation.

Sentinel does not score adopter repositories. It produces **closure**: a list of what must be proven, what may be proven, and what is explicitly out of scope.

---

## Attestation geometry — VERT_ and HORI_

Sentinel uses two stable seam vocabularies that are distinct from UDALI layer numbers and SEAM-NN archetypes.

### Vertical bands (`VERT_*`) — depth

| Id | Band | Covers |
|----|------|--------|
| `VERT_UI` | Presentation | UI surfaces, client state, presentation logic. |
| `VERT_APP` | Application | Controllers, services, orchestrators, DTOs, mappers. |
| `VERT_DOMAIN` | Domain | Invariants, rules, lifecycle. |
| `VERT_DATA` | Data | Persistence, schema, replication, caches. |
| `VERT_PLATFORM` | Platform | Runtime, infrastructure, environment, deploy. |

Vertical bands are **coarser** than UDALI L-numbers. A finding may cite both: `L13 + VERT_APP` is consistent and useful.

### Horizontal kinds (`HORI_*`) — boundary family

| Id | Boundary | Covers |
|----|----------|--------|
| `HORI_SCHEMA` | Schema / contract | DTOs, schemas, contract versions, data shape. |
| `HORI_SERVICE` | Service-to-service | Module or service boundaries within the application. |
| `HORI_AUTH` | Auth / trust | Authentication, authorization, tenancy. |
| `HORI_EXT` | External integration | Third-party APIs, vendor systems. |
| `HORI_ASYNC` | Async / event | Queues, events, fan-out. |
| `HORI_GUIDANCE_FEEDBACK` | Guidance ↔ UI | Server-computed verdict to UI affordance (see [SEAM-13](seam-catalog.md#seam-13--guidancefeedback)). |
| `HORI_REALM_BRIDGE` | Realm bridge | Handoff from one data-flow stack into another, or across an authority/trust realm (see [`data-flow-stacks.md`](data-flow-stacks.md)). |

A pass may also see `META_SCOPE` and `META_ATTEST` rows — these are scope framing and human attestation, not data fields.

---

## Attestation closure

A **closure** is the deterministic table of seams an assurance pass must address, given three declared dimensions.

### Inputs

| Dimension | Values | Meaning |
|-----------|--------|---------|
| **Attestation depth** | `narrow` / `standard` / `expansive` | How wide proof is for this pass; controls required vs latent tiers. |
| **Vertical claim** | `presentation_only` / `through_application_boundary` / `through_domain_and_data` / `full_vertical_ui_to_metal` | The deepest stack hop the pass claims to cover. |
| **Horizontal seams** | Any subset of the `HORI_*` family | Which cross-cutting boundary types are explicitly in play. |

### Output tiers

Every seam in the closure is classified into one of three tiers:

| Tier | Meaning |
|------|---------|
| **Required** | Must be proven (anchor) or explicitly deferred (with owner) before Stage 3 attestation. |
| **Additional** | Recommended for widening; not blocking. |
| **Latent** | Out of scope for this pass; risk acknowledged, not erased. |

### Example closures

> Illustrative; exact tiering is a property of the implementation.

**Narrow / presentation only / schema contract:**

- Required: `VERT_UI`, `HORI_SCHEMA`
- Additional: `VERT_APP`
- Latent: `VERT_DOMAIN`, `VERT_DATA`, `VERT_PLATFORM`, all other `HORI_*`

**Standard / through application boundary / schema + auth + async:**

- Required: `VERT_UI`, `VERT_APP`, `HORI_SCHEMA`, `HORI_AUTH`, `HORI_ASYNC`
- Additional: `VERT_DOMAIN`, `HORI_SERVICE`
- Latent: `VERT_DATA`, `VERT_PLATFORM`, `HORI_EXT`, `HORI_GUIDANCE_FEEDBACK`

**Expansive / full vertical / all horizontals:**

- Required: all `VERT_*`, all `HORI_*` in scope
- Additional: cross-pass anchors (regression, post-incident review)
- Latent: explicit non-goals declared at Stage 1

A conformant implementation derives the closure deterministically from inputs. Adopters can reproduce the rule themselves; no MCP is required to know which tier a seam falls into.

---

## Audit programs

Sentinel passes are usually launched against a named **audit program** — a template scoped to one shape of assurance work. Adopters MAY define their own; the catalog below names the shapes used in practice.

| Program id | Scope model | Lead band | What it proves |
|------------|-------------|-----------|----------------|
| `sentinel_audit_aadm_assurance` | slice or app | program-level | AADM assurance overall: UDALI trace + AUTH + enforcement locus + evidence + agent posture. |
| `sentinel_audit_slice_holistic_udali` | slice | holistic | Trace UI-to-metal + horizontal seams; lead role per seam; evidence matrix vs gaps. |
| `sentinel_audit_slice_unifier` | slice | Unifier | Presentation vs server truth; UX errors vs backend reality. |
| `sentinel_audit_slice_designer` | slice | Designer | Schemas, DTOs, domain invariants, cross-boundary mappings. |
| `sentinel_audit_slice_adapter` | slice | Adapter | Persistence, auth plumbing, adapters, trust boundaries. |
| `sentinel_audit_slice_logician` | slice | Logician | Orchestration, workflows, integrations, async/event seams. |
| `sentinel_audit_slice_integrator` | slice | Integrator | Routes, job entrypoints, CI gates, deploy posture for what ships. |
| `sentinel_audit_slice_seam_coverage` | slice | holistic | SEAM check — selected SEAM-01..15 archetypes PROVEN/FAILED/DEFERRED. |
| `sentinel_audit_slice_contract_trace` | slice | Designer | TRACE spine on a slice: 3–5 critical fields across UDALI handoffs. |
| `sentinel_audit_slice_authoring_surface` | slice | Logician | Signal Stack on one UI surface: twelve SIGL rows. |
| `sentinel_audit_slice_fullstack` | slice | holistic | Balanced full-stack: closure + cross-surface + journey + evidence binder. |

Each program runs the same three Sentinel stages with the same human gates. The program scopes the *content*, not the *shape*.

---

## Evidence model

Findings cite stable namespaces so they dedupe across tools, tickets, and agents.

| Prefix | Role | Source |
|--------|------|--------|
| `AUTH-NN` | Governance control | [`auth-catalog.md`](auth-catalog.md) |
| `SEAM-NN` | Handoff archetype | [`seam-catalog.md`](seam-catalog.md) |
| `TRACE-NN` | Drift pattern class | [`trace-catalog.md`](trace-catalog.md) |
| `VERT_*` | Sentinel vertical band | This document |
| `HORI_*` | Sentinel horizontal kind | This document |
| `L1`–`L22` | UDALI placement | [UDALI 22-layer model](../docs/udali-22-layer-model.md) |
| `SIGL-NN` | Signal Stack row | [`signal-stack-compliance.md`](signal-stack-compliance.md) |
| `SURF:<name>` | Filled surface column | [`signal-stack-compliance.md`](signal-stack-compliance.md) |

### Rules

1. **Do not mix prefixes.** Each prefix has one meaning. `VERT_*` is not an alternate spelling of `L*`.
2. **Combine prefixes freely in one finding.** `SEAM-04 + TRACE-01 + AUTH-21 + VERT_APP + HORI_SCHEMA + L8` is correct and useful — each prefix names a different facet.
3. **SIGL-NN is not UDALI L*.** SIGL-08 (verdict producer row) usually lives at UDALI L13, but the numbers are not interchangeable.
4. **Evidence anchor goes in the application repository.** A finding without a path, test, or CI link is not bound — it is, at best, a deferred gap.

### Deferred gaps

A row may be `DEFERRED` instead of proven — but with discipline:

- **Named owner.** Who is responsible for proving or fixing.
- **Stated risk.** What goes wrong if this stays unproven.
- **Follow-up.** A linked issue, ticket, or Navigator slice.

A `DEFERRED` row with no owner is a non-conformant finding. The Sentinel pass should refuse to attest.

---

## Sentinel exit kinds

When closing a Sentinel pass, the implementation indicates how the thread should continue:

| Exit kind | Meaning |
|-----------|---------|
| `stay_assurance` | More assurance work needed on this slice or program — schedule the next pass. |
| `open_investigator` | A concrete defect was surfaced; carry the anchors over and open Investigator. |
| `open_navigator` | A change program is needed; open a Navigator slice with Scope already roughed out. |
| `clarify_intent` | Intent was ambiguous; do not attest; return to the user. |

---

## Worked closure example

> Illustrative.

**Slice.** "Change billing plan" — `PATCH /accounts/:id/plan`.

**Stage 1 — Scope & frame.**

- Attestation depth: `standard`.
- Vertical claim: `through_application_boundary`.
- Horizontal seams in scope: `HORI_SCHEMA`, `HORI_AUTH`.
- SEAMs declared: SEAM-02 (wire ↔ DTO), SEAM-03 (DTO ↔ domain), SEAM-11 (trust/tenancy).

**Closure (derived):**

- Required: `VERT_UI`, `VERT_APP`, `HORI_SCHEMA`, `HORI_AUTH`.
- Additional: `VERT_DOMAIN`, `HORI_SERVICE`.
- Latent: `VERT_DATA`, `VERT_PLATFORM`, `HORI_ASYNC`, `HORI_EXT`, `HORI_GUIDANCE_FEEDBACK`.

**Stage 2 — Trace & bind evidence.**

| Seam | Status | Evidence / finding |
|------|--------|---------------------|
| `VERT_UI` | PROVEN | UI test `plan-change.spec.ts` — covers states; visual regression baseline. |
| `VERT_APP` | PROVEN | Service `PlanService.changePlan` + DTO `PlanChangeRequest`; mapper test `plan-mapper.test.ts`. |
| `HORI_SCHEMA` | FAILED | Mapper silently downgrades unknown enum to `free`. Cite `TRACE-04 + AUTH-21 + SEAM-03 + L8`. Owner: Billing team. Fix scope: Investigator slice. |
| `HORI_AUTH` | PROVEN | Tenancy enforced server-side; negative cross-tenant test passes. Cite `AUTH-16 + SEAM-11`. |
| `VERT_DOMAIN` (additional) | DEFERRED | Owner: Domain team. Risk: plan tier transitions not validated against domain state machine. Follow-up: Navigator slice. |
| `HORI_SERVICE` (additional) | DEFERRED | Owner: Platform. Risk: PlanService called from two upstream paths with different rate-limit posture. |

**Stage 3 — Attest readiness.**

- Required tier: `HORI_SCHEMA` FAILED → cannot attest. Open Investigator for `TRACE-04 + SEAM-03 + AUTH-21` finding.
- Other required tiers proven.
- Exit kind: `open_investigator`.

Human attester: signed.

---

## Related

- [`three-lanes.md`](three-lanes.md) — Sentinel as one lane in the three-lane model
- [`seam-catalog.md`](seam-catalog.md) — SEAM archetypes used in scope
- [`auth-catalog.md`](auth-catalog.md) — obligations cited in findings
- [`trace-catalog.md`](trace-catalog.md) — drift patterns cited on failed seams
- [`signal-stack-compliance.md`](signal-stack-compliance.md) — SURF columns for UI surfaces
- [`udali-auth-enforcement-mapping.md`](udali-auth-enforcement-mapping.md) — which AUTH lives at which layer
