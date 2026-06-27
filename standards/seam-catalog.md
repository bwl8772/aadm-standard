# SEAM catalog — auditable handoff archetypes

> Stable, append-only catalog of seams — places where responsibility, trust, or representation changes inside a software system. SEAMs name *which crossings must be in scope* when you trace data, audit alignment, or review agent-assisted changes. Stack-agnostic.

## What SEAM is — and is not

SEAM answers one question: **what handoff are we proving?** It does not answer:

| Family | Question it answers |
|--------|---------------------|
| **AUTH-NN** | What **obligation** must be enforced? (governance) |
| **TRACE-NN** | What **drift class** failed? (defect pattern) |
| **VERT_** / **HORI_** | What is the **attestation geometry** (depth and boundary kind)? |
| **L1–L22** | Where does code **belong**? (UDALI placement) |
| **Feathers seam** (lowercase) | Where can behavior be **substituted** at a Module Interface? (design vocabulary) |

A single finding can — and often should — cite several families together:

```
SEAM-04 + TRACE-01 + AUTH-21 + VERT_APP + HORI_SCHEMA + L8
```

That string says: an identity-normalization handoff (SEAM-04) drifted with a semantic mismatch (TRACE-01), violating the DTO/contract boundary obligation (AUTH-21), at the application vertical band (VERT_APP), as a schema boundary (HORI_SCHEMA), located at the Mapper layer (L8).

---

## Why SEAM exists

Many teams trace **persistent fields** (database columns, event schemas) and miss defects where meaning changes **inside a process** — canonical agent ids, dispatch-time tool ids, alias tables with no column anchor. SEAM adds a checklist of crossing types so those paths are explicitly in or out of scope, not afterthoughts.

---

## Anchor lifecycle classes

Every SEAM declares an anchor lifecycle — how long the thing on either side of the handoff persists. This determines what kind of evidence proves the seam.

| Class | Meaning | Proof anchor |
|-------|---------|--------------|
| **A — Persistent** | Survives commit (database row, object store, durable outbox) | `table.column`, migration |
| **B — Durable contract** | Versioned shared shape (OpenAPI spec, package DTO, event schema) | schema file, registry |
| **C — Process-scoped** | One workflow run or interaction (saga, conversation) | correlation id, state machine |
| **D — Transient composed** | Built per step; no canonical store | `symbol@path`, call chain |
| **E — Ephemeral** | Request/UI only; must not become authority | proof that it does not reach persistence |

Persistent-only traces miss classes **D** and **E** unless SEAM-04 (Identity Normalization) and SEAM-05 (Dispatch / Orchestration Composition) are explicitly in scope.

---

## Catalog

### Foundation

#### SEAM-00 — System Seam Model

**Purpose.** The meta-seam. Defines the catalog itself: namespaces, anchor lifecycles, and the SEAM check discipline below. Cite SEAM-00 when discussing the framework, not a specific crossing.

**When in scope.** Always — implicitly.

---

### Persistent & contract crossings

#### SEAM-01 — Persistent field handoff (write → store → read)

**Purpose.** A field is written by one path, stored persistently, and later read by another. The handoff is across time and possibly across services.

**Anchor lifecycle.** A — Persistent.

**Typical mapping.** VERT_DATA · HORI_SCHEMA · L4 (DB), L7 (DTO), L10 (Repository).

**Related AUTH.** AUTH-11 (Staleness), AUTH-17 (Sync State), AUTH-21 (DTO/Contract Boundary).

**Pass criteria.** Schema source named; writes and reads use the same canonical name and type; tests cover round-trip semantics; null/optional disagreement is impossible.

**Fail criteria.** Silent type coercion; rename in only one direction; cached read disagrees with stored truth.

**Evidence.** Migration files, schema definitions, round-trip tests.

#### SEAM-02 — Wire ↔ Validated ingress contract

**Purpose.** The boundary between an incoming wire payload (HTTP body, gRPC message, queue message) and the validated DTO the rest of the system trusts.

**Anchor lifecycle.** B — Durable contract.

**Typical mapping.** VERT_APP · HORI_SCHEMA · L7 (DTO), L18 (Controller).

**Related AUTH.** AUTH-10 (Spec Version), AUTH-18 (L2 Content Inputs), AUTH-21 (DTO/Contract Boundary).

**Pass criteria.** Schema enforced at the boundary; rejection on unknown fields where policy requires; spec version is observable.

**Fail criteria.** Direct deserialization to a domain object; loose `any`-typed parsing; partial validation.

**Evidence.** Schema files (OpenAPI, JSON Schema, protobuf), validator code, negative tests for malformed input.

#### SEAM-03 — DTO ↔ Domain mapping

**Purpose.** Translation between the validated DTO and the domain object — where invariants apply, defaults resolve, and computed fields populate.

**Anchor lifecycle.** B — Durable contract on the DTO side; domain object is process-scoped.

**Typical mapping.** VERT_DOMAIN · HORI_SCHEMA · L8 (Mapper), L6 (Domain).

**Related AUTH.** AUTH-21, AUTH-12 (Completeness), AUTH-06 (Lifecycle).

**Pass criteria.** Mapper has tests for all branches; rejection (not silent default) for invalid combinations; round-trip is symmetric where required.

**Fail criteria.** Mapper silently downgrades enum values; mapper invents fields; mapper drops fields the domain expects.

**Evidence.** Mapper module, unit tests, golden-payload tests.

#### SEAM-06 — Domain ↔ Persistence

**Purpose.** The crossing between the domain object and the persistence representation (table row, document, key-value).

**Anchor lifecycle.** A — Persistent.

**Typical mapping.** VERT_DATA · HORI_SCHEMA · L5 (Entity), L10 (Repository).

**Related AUTH.** AUTH-21, AUTH-04 (L2 Version), AUTH-27 (Transaction Boundary).

**Pass criteria.** Domain object has clear persistence representation; transactions are explicit where atomicity is required; migrations preserve semantics.

**Fail criteria.** Domain logic implemented in stored procedures or triggers; persistence layer doing business rules; partial writes leaving inconsistent state.

**Evidence.** Repository module, migration history, transaction tests.

#### SEAM-07 — Read path / egress / response shaping

**Purpose.** Outbound shaping from domain to wire — the inverse of SEAM-02, with potentially different schema considerations (response DTOs, projections).

**Anchor lifecycle.** B — Durable contract.

**Typical mapping.** VERT_APP · HORI_SCHEMA · L7 (DTO), L8 (Mapper), L18 (Controller).

**Related AUTH.** AUTH-21, AUTH-22 (CRQ/Read Model), AUTH-33 (Field-Level Access).

**Pass criteria.** Outbound DTOs are explicit; field-level redaction is enforced server-side; spec version is observable.

**Fail criteria.** Domain objects serialized directly to wire; PII leaked because UI was "supposed to hide it."

**Evidence.** Response DTOs, redaction layer, integration tests.

---

### In-process & orchestration

#### SEAM-04 — Identity normalization (alias / canonical / case)

**Purpose.** When multiple string shapes refer to the same concept — `toolId` aliases, vendor IDs, case-insensitive matches, recipe-scoped slug variations — and the canonical form must be resolved at runtime. Often **no DB column anchors this**; it lives at a function boundary.

**Anchor lifecycle.** D — Transient composed (primary). B when the canonical authority lives in a shared enum/package.

**Typical mapping.** VERT_APP · VERT_DOMAIN (in-process). HORI_SCHEMA if a wire form is involved.

**Related AUTH.** AUTH-21, AUTH-44 (Tool Invocation, candidate), AUTH-02 (Recipe Authority) when recipe-scoped.

**Related TRACE.** TRACE-01 (semantic mismatch), TRACE-04 (silent coercion), TRACE-06 (lost/forged id), TRACE-08 (round-trip asymmetry).

**Triggers.** Multiple string shapes for the same concept; `toCanonical*` functions; case-sensitive comparisons; legacy alias sets like `LEGACY_*_V1`.

**Pass criteria.** Single canonical authority per id family (enum or function — named in evidence); round-trip table documented (every producer → canonical → every consumer); no raw string compare on aliases at dispatch gates; fix at the boundary covers the entire enumerated universe.

**Fail criteria.** Case drift (`content` vs `Content`); prefix/suffix mismatch; bypass path skipping canonicalizer; second hop assuming the first hop ran.

**Evidence.** Symbol list with paths; unit test per id family; dispatch builder + consumer chain. No `table.column` required.

**When N/A.** Only opaque UUIDs with no aliases anywhere in the slice.

#### SEAM-05 — Dispatch / orchestration composition

**Purpose.** A request is decomposed into a sequence of tool/agent/service calls, and ids or payloads are composed at runtime. The composition lives in code, often produced per request, with no canonical persistent form.

**Anchor lifecycle.** D — Transient composed.

**Typical mapping.** VERT_APP · HORI_SERVICE · L13 (Service), L15 (AI Services).

**Related AUTH.** AUTH-09 (Modality), AUTH-25 (Idempotency), AUTH-44 (Tool Invocation), AUTH-46 (Context Trust Boundary).

**Pass criteria.** Composition is auditable from logs (correlation id, step list); composition is reproducible from inputs; agent tool calls authorized per AUTH-44.

**Fail criteria.** Step list invisible to operators; composition includes unsanitized user content reaching tool arguments; one path bypasses the composer.

**Evidence.** Composer module, step logs, replay tests.

#### SEAM-12 — Computed / derived field (write vs read path)

**Purpose.** A field is computed — at write time, at read time, or both. The same logical value can be calculated through more than one code path; SEAM-12 names that risk.

**Anchor lifecycle.** A or D depending on whether the value is stored.

**Typical mapping.** VERT_APP · HORI_SCHEMA · L6 (Domain), L13 (Service), L22 (Read model).

**Related AUTH.** AUTH-22, AUTH-11.

**Related TRACE.** TRACE-11 (computed field inconsistency).

**Pass criteria.** One canonical computation; both paths exercised against an identical-input fixture; stored vs computed reconciliation strategy is named.

**Fail criteria.** Read path computes differently from write path; cached projection diverges from on-the-fly calculation.

**Evidence.** Shared computation module; cross-path test; reconciliation job.

---

### Async, external, dual-source

#### SEAM-08 — Async / event / fan-out parity

**Purpose.** The synchronous API and the asynchronous event for the same operation may diverge — different fields, different versions, different ordering guarantees.

**Anchor lifecycle.** B — Durable contract.

**Typical mapping.** VERT_APP · HORI_ASYNC · L14 (Events).

**Related AUTH.** AUTH-25, AUTH-26, AUTH-28, AUTH-30 (Traceability).

**Related TRACE.** TRACE-10 (event/API parity), TRACE-09 (version skew).

**Pass criteria.** Sync and async payloads share a versioned schema or have an explicit mapper; consumers can identify version; ordering and at-least-once / exactly-once semantics declared.

**Fail criteria.** Event payload silently lags API schema; consumers crash on new fields with no fallback.

**Evidence.** Schema registry; consumer contract tests; outbox or saga test.

#### SEAM-09 — External integration (vendor id alphabet)

**Purpose.** Boundary with a third-party API where vendor identifiers, error vocabularies, and rate limits are not yours to control.

**Anchor lifecycle.** B (their contract) / A (your mapping).

**Typical mapping.** VERT_APP · HORI_EXT · L16 (Integrations).

**Related AUTH.** AUTH-26, AUTH-32, AUTH-38, AUTH-39, AUTH-41.

**Pass criteria.** Vendor errors mapped to your structured errors; vendor ids stored separately from your canonical ids; retries respect vendor rate limits; secrets handled per AUTH-41.

**Fail criteria.** Vendor errors leak to user-facing surfaces; tight retry loops; vendor id used as your primary key.

**Evidence.** Integration module, error mapping table, rate-limit test.

#### SEAM-10 — Dual source of truth

**Purpose.** Two stores can answer the same question and may disagree (cache vs database, search index vs primary, feature flag vs config service).

**Anchor lifecycle.** A on both sides.

**Typical mapping.** VERT_DATA · HORI_SCHEMA · L12 (Cache), L4 (DB).

**Related AUTH.** AUTH-01 (Reconcile), AUTH-11, AUTH-17.

**Related TRACE.** TRACE-05 (dual source of truth).

**Pass criteria.** Authoritative store named; sync mechanism documented; reconciliation strategy in code; consumers can ask for "freshest" vs "cached" explicitly.

**Fail criteria.** Two stores quietly drift; consumer reads from whichever is faster without freshness info.

**Evidence.** Sync module, drift detection job, freshness tests.

---

### Trust, config, UX, ownership

#### SEAM-11 — Trust / tenancy

**Purpose.** Crossings where the right to supply an id, an actor identity, or a tenant scope is at stake — anywhere a caller might forge what should be a server-asserted fact.

**Anchor lifecycle.** B / A (identity tokens, tenant claims).

**Typical mapping.** VERT_APP · HORI_AUTH · L11 (Auth), L7 (DTO), L18 (Controller).

**Related AUTH.** AUTH-16, AUTH-32, AUTH-33, AUTH-34.

**Related TRACE.** TRACE-06 (lost or forged identifier).

**Pass criteria.** Server asserts actor and tenant from authenticated context, not from request body; tenant-bound queries always carry tenant constraint; cross-tenant access is explicit and audited.

**Fail criteria.** Tenant id read from request body; "user_id from JWT" pattern but actually trusts the client.

**Evidence.** Auth middleware, tenant constraint tests, negative cross-tenant tests.

#### SEAM-14 — Config / environment / feature flag

**Purpose.** Behavior changes based on configuration, environment, or feature flag — a runtime branch that's often invisible to static analysis.

**Anchor lifecycle.** A — Persistent (config); B — Durable contract (flag schema).

**Typical mapping.** VERT_PLATFORM · L17 (Middleware), L22 (System Integration).

**Related AUTH.** AUTH-41, AUTH-42.

**Pass criteria.** Configuration is named, typed, and tested; flag has owner and retirement plan; environment differences are explicit.

**Fail criteria.** Flag accumulates without retirement; production behavior depends on undeclared environment variable; staging silently differs from production.

**Evidence.** Config schema, flag registry, environment matrix tests.

#### SEAM-13 — Guidance–Feedback

**Purpose.** The boundary between a server-computed verdict (workflow state, readiness, conflict severity) and how it surfaces to the user. The seam exists once; it must not be re-implemented per screen.

**Anchor lifecycle.** B (verdict contract); E (presentation).

**Typical mapping.** VERT_UI · VERT_APP · HORI_GUIDANCE_FEEDBACK · L13 (Service, verdict producer), L1 (UI, renderer).

**Related AUTH.** AUTH-02, AUTH-11, AUTH-15, AUTH-20.

**Pass criteria.** Single verdict producer; exactly one resolver (deletion test passes — removing a legacy map does not break callers); one affordance reused on every surface; class correctly partitions corrective / refresh / transient.

**Fail criteria.** Client derives severity; duplicate copy maps per screen; modal shown for transient state; deep-link away from unsaved work on a refresh-class verdict.

**Evidence.** Verdict type, resolver module, shared affordance component, tests covering each class.

See [`guidance-feedback-seam.md`](guidance-feedback-seam.md) for the full doctrine.

#### SEAM-15 — UDALI role handoff (ownership transfer)

**Purpose.** When ownership of a piece of work moves from one UDALI band to another (Unifier hands collected input to Designer; Designer hands a domain object to Logician; Logician hands a workflow outcome to Integrator).

**Anchor lifecycle.** C — Process-scoped.

**Typical mapping.** VERT_APP · HORI_SERVICE.

**Related AUTH.** AUTH-02 (Recipe), AUTH-06 (Lifecycle), AUTH-29 (Audit Log).

**Pass criteria.** Each handoff is explicit (a function call, a queue message, a state machine transition); the receiving band's input contract is enforced; audit log captures the handoff.

**Fail criteria.** Implicit handoffs (shared mutable state); receiving band reaching back into the sending band's internals; missing audit trail across the transfer.

**Evidence.** Module interfaces between bands, audit logs, integration tests across band boundaries.

---

## SEAM check (the assurance discipline)

For any scoped slice (feature, capability, change):

1. **Pick in-scope SEAM-NN ids.** Include SEAM-04 and SEAM-05 whenever agents, recipes, or tools dispatch with alias or synthesized ids.
2. **Status every row.** PROVEN | FAILED | DEFERRED (named owner). No blank rows.
3. **Attach repository evidence.** Paths, tests, CI links. Documentation does not substitute for proof.
4. **Cite TRACE on failures.** Add at least one [TRACE-NN](trace-catalog.md) drift class when a seam is FAILED.
5. **Cite AUTH where applicable.** AUTH-NN identifies which obligation the seam is bound to.
6. **Map to Sentinel geometry** when this pass is part of a Sentinel assurance run — vertical hops (`VERT_*`) and horizontal kinds (`HORI_*`).

---

## "Seam" in other senses

| Term | Meaning |
|------|---------|
| **SEAM-NN** (this catalog) | Auditable handoff archetype for scope and checklists |
| **VERT_** / **HORI_** | Assurance geometry (depth and boundary kind) — see [`sentinel-program.md`](sentinel-program.md) |
| **Feathers seam** | Design substitution point at a Module Interface — see [`module-interface-seam-vocabulary.md`](module-interface-seam-vocabulary.md) |
| Colloquial "seam" | Any boundary. Prefer SEAM-NN or VERT/HORI in audit notes. |

---

## Related

- [TRACE catalog](trace-catalog.md) — drift pattern classes
- [AUTH catalog](auth-catalog.md) — obligation catalog
- [Sentinel program](sentinel-program.md) — VERT_/HORI_ geometry, attestation closure
- [Module–Interface–Seam vocabulary](module-interface-seam-vocabulary.md) — design language
