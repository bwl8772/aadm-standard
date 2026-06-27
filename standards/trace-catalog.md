# TRACE catalog — contract boundary drift patterns

> Stable, append-only catalog of data-contract drift patterns. Pair with [`seam-catalog.md`](seam-catalog.md) (which crossings) and [`auth-catalog.md`](auth-catalog.md) (which obligations). Stack-agnostic.

## Purpose

**TRACE** is a compact, repeatable way to audit whether **meaning survives handoffs** as data moves through an application. It operationalizes [AUTH-21 (DTO / Contract Boundary)](auth-catalog.md#auth-21--dto--contract-boundary-authority) and the L7 (DTO) / L8 (Mapper) band so reviewers hunt **contract drift**, not only "was a control cited?"

TRACE does **not** replace AUTH or placement. It complements them: a single finding may cite an AUTH obligation, a SEAM archetype, *and* a TRACE pattern class.

---

## How a TRACE pass works

For a bounded slice (feature, route, capability):

1. **Pick 3–5 critical fields.** The smallest set whose drift would hurt — ids, money, status, tenancy, consent. Not whole schemas.
2. **List material handoffs per field.** Use SEAM ids when scope is formally bound; informal names (wire ↔ DTO, DTO ↔ domain, domain ↔ persistence, read path ↔ response, async ↔ sync) are fine in early drafts.
3. **Status each cell.** PASS | FAIL | DEFERRED (named owner). Blank cells are not allowed.
4. **Cite TRACE-NN on failures.** Use the pattern table below.
5. **Attach evidence.** Paths, tests, golden payloads, schema files.

Done means: every material seam row is proven, failed with evidence, or explicitly deferred.

---

## TRACE pattern catalog

| Id | Name | What goes wrong | Typical evidence |
|----|------|-------------------|------------------|
| **TRACE-01** | Semantic mismatch | Same name, different meaning across layers (status enums diverge; "open" means different things). | Enum / type / schema definitions across layers. |
| **TRACE-02** | Rename drift | Field renamed at one boundary only; mapper still uses old name. | Mapper/DTO diff; OpenAPI vs types. |
| **TRACE-03** | Optional/required drift | Nullability or "missing vs empty" differs across seams. | Validation rules, DB NOT NULL, UI optional state. |
| **TRACE-04** | Silent coercion | Strings parsed to numbers or dates without shared rules; locale or timezone risk. | Parsing helpers, schema, DB driver behavior. |
| **TRACE-05** | Dual source of truth | Two unconstrained copies of one fact diverge. | Parallel caches, shadow columns, duplicate config. |
| **TRACE-06** | Lost or forged identifier | IDs or tenant keys dropped, swapped, or client-supplied when server-owned. | AUTH tenancy rules, ingress validation, mapper tests. |
| **TRACE-07** | Boundary bypass | Data skips validation/mapper (raw persistence, spread assignments, direct ORM in wrong layer). | Direct ORM use in wrong layer; bypass routes. |
| **TRACE-08** | Round-trip asymmetry | A → B → A changes semantics or loses fields. | Mapper tests; golden payloads. |
| **TRACE-09** | Version skew | Contract version N consumed by version N−1 (events, mobile, partners). | Version headers, consumer contracts, feature flags. |
| **TRACE-10** | Event/API parity | Async payload doesn't match documented API or domain event schema. | Schema registry, contract tests. |
| **TRACE-11** | Computed field inconsistency | Derived fields recomputed differently across paths (read vs write, CQRS). | Both code paths with identical-input fixture. |
| **TRACE-12** | Observation/select leakage | Fields exposed in logs/metrics/errors that policy forbids. | Redaction layer, error payloads, observability config. |

---

## Tie-ins

| Family | Tie-in |
|--------|--------|
| **AUTH-21** | Primary AUTH anchor for contract boundaries; cite alongside TRACE findings at DTO/mapper seams. |
| **AUTH-10 / AUTH-18** | Spec/version and validation discipline; often surface TRACE-09 / TRACE-03. |
| **AUTH-22** | Read model / CRQ; often surfaces TRACE-11. |
| **L7 / L8** | Primary UDALI ownership for ingress/egress shapes and transforms; other layers appear per trace. |
| **SEAM-01..07** | Persistent and contract crossings — TRACE patterns most often appear here. |
| **SEAM-04 / SEAM-05** | In-process crossings — required when ids are synthesized at dispatch time. |

---

## Worked mini-example

> Illustrative only — paths are placeholders.

**Slice.** "Change billing plan" API (`PATCH /accounts/:id/plan`).
**Critical field.** `plan_tier` (enum: `free` | `pro` | `enterprise`).

| Handoff | Result | Notes + evidence | Finding ids |
|---------|--------|------------------|-------------|
| Wire ↔ DTO | PASS | Ingress DTO requires enum; OpenAPI + schema agree. | — |
| DTO ↔ Domain | FAIL | Mapper maps unknown string to `free` instead of rejecting → silent downgrade. | `TRACE-04`, `AUTH-21`, `L8`, `HORI_SCHEMA`, `VERT_DOMAIN` |
| Domain ↔ Persistence | PASS | Single `plan_tier` column with check constraint. | — |
| Read path | DEFERRED | Owner: Billing team. Reconcile after plan rename effort. | — |

---

## Minimal worksheet (paste into audit intake)

```text
Slice / feature:
Critical fields (3–5):

For each field:
  Field name (canonical):
  UDALI touch layers (L1–L22, only those that matter):
  Sentinel bands touched (if closure run): VERT_* / HORI_* from sentinel_closure output
  Seam checklist (wire↔DTO / DTO↔domain / …): PASS | FAIL | DEFERRED — notes + paths

Finding ids: SEAM-NN, TRACE-NN, AUTH-NN, VERT_*, HORI_*, L*
Open gaps / owners:
```

---

## Non-goals

- TRACE is not a second AUTH catalog; the 12 pattern ids stay stable and small.
- TRACE does not assign compliance scores; proof remains repository evidence.
- TRACE does not substitute for AUTH-24 (Verification) — patterns name failure shapes; tests prove pass/fail.
