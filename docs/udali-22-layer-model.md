# UDALI 22-layer model

Within **AADM** (**Agentic Authority Delivery Model**), **[UDALI](udali-personas.md)** names five collaboration stances (**U, D, A, L, I**). This page is the **optional finer slice**: **twenty-two numbered layers (L1–L22)** you can use for reviews, routing, and evidence—without replacing the five-persona spine.

- **Accountability spine (required for AADM framing):** the five personas — see **[UDALI coding personas](udali-personas.md)**.  
- **Discussion lattice (optional):** L1–L22 below — same program may adopt, adapt, or ignore numbering; if you ignore it, record **N/A** with a one-line rationale where your process expects a layer callout.

This document is **not** a repository folder mandate. Map layers onto your stack however it fits; record the mapping per meaningful change ([layer map template](../templates/layer-map.md)).

---

## Why a 22-layer slice exists

Finer slices help teams answer:

- **Where** a specific concern lives (UI rendering vs DTO vs gateway policy).  
- **What evidence** is expected before promotion (tests, reviews, integration checks).  
- **How** agent or human work stays bounded so cross-layer “helpful” edits do not silently violate invariants.

The numbers are a **shared vocabulary**, not a linear factory line. Real delivery revisits layers out of order; production promotion should still **close gaps** rather than assume skipped layers “do not apply.”

---

## Layer index (L1–L22)

| Layer | Name | Persona band | One-line responsibility |
|------:|------|---------------|-------------------------|
| **L1** | UI implementation | Unifier (U) | Renders UI and handles direct user interaction. |
| **L2** | Adapter layer | Unifier (U) | Translates between UI concerns and backend-oriented contracts. |
| **L3** | UI guards | Unifier (U) | Client-side routing, affordances, and UX protections (without pretending to replace server AUTH). |
| **L4** | Database | Designer (D) | Persistence technology and schema the application owns or consumes deliberately. |
| **L5** | Entity | Designer (D) | Typed shapes that represent stored or transmitted data in a bounded way. |
| **L6** | Domain | Designer (D) | Business rules and invariants tied to domain meaning. |
| **L7** | DTO | Designer (D) | Contracts at API and integration boundaries. |
| **L8** | Mapper | Designer (D) | Translations between external representations and domain meaning—testable and explicit. |
| **L9** | Repository interface | Adapter (A) | Contracts for how the application reads and writes data. |
| **L10** | Repository | Adapter (A) | Implements data access against real stores, transactions, and consistency choices. |
| **L11** | Auth and authorization | Adapter (A) | Identity establishment and permission enforcement next to real operations. |
| **L12** | Cache | Adapter (A) | Performance optimizations that must stay correct under AUTH and freshness rules. |
| **L13** | Service | Logician (L) | Application orchestration coordinating domain outcomes. |
| **L14** | Events | Logician (L) | Asynchronous messaging, outbox-style patterns, and event contracts. |
| **L15** | AI services | Logician (L) | Model-invoking paths as bounded capabilities with explicit inputs, outputs, and governance. |
| **L16** | Integrations | Logician (L) | Calls across vendor or organizational boundaries with explicit failure semantics. |
| **L17** | Middleware | Integrator (I) | Cross-cutting request or message pipeline concerns at the edge. |
| **L18** | Controller | Integrator (I) | Thin entrypoints: HTTP handlers, RPC surfaces, consumers—delegate inward. |
| **L19** | Routes | Integrator (I) | Endpoint definitions, dispatch rules, and public API shape. |
| **L20** | Gateway | Integrator (I) | External policy enforcement, traffic rules, and publish barriers where applicable. |
| **L21** | QA and verification | Integrator (I) | Planned automated and manual verification mapped to risk. |
| **L22** | System integration and release | Integrator (I) | Deployment, configuration discipline, rollback posture, and production-like validation. |

---

## Zones (roll-up view)

Grouping L1–L22 into six zones is a common teaching aid; zone names are not a second standard—they summarize the table above.

| Zone | Layers | Theme |
|------|--------|--------|
| **1 — Client surface** | L1–L3 | What users touch; coherence, safety, and honest client/server boundaries. |
| **2 — Data and meaning** | L4–L8 | Persistence shapes, domain truth, boundary contracts, and mappings. |
| **3 — Access and performance** | L9–L12 | Data access, AUTH, caching—must stay correct under real loads and threats. |
| **4 — Behavior through time** | L13–L16 | Orchestration, async, AI, and external systems with explicit failure modes. |
| **5 — Edge and traffic** | L17–L20 | Middleware, controllers, routes, gateways—how the system is entered and governed. |
| **6 — Proof and ship** | L21–L22 | Verification evidence and release readiness against realistic environments. |

---

## Principles for using the stack

1. **No silent skips** — If a layer is “not involved,” record why (especially AUTH, async, and deploy surfaces).  
2. **Single primary home** — Each change should have a clear primary layer; cross-layer work moves through **contracts** (DTOs, events, interfaces), not leaked internals.  
3. **AUTH follows meaning** — Presentation hints are not a substitute for enforcement where domain operations are interpreted (see [AUTH-aware delivery](auth-aware-delivery.md)).  
4. **Client stability** — L1–L3 should not absorb backend volatility without an explicit contract change (L7/L8) and review.  
5. **Evidence, not labels** — Layer tags in tickets help; tests, reviews, and integration runs **prove** readiness.

---

## Layer deep dive (L1–L22)

Each subsection follows the same shape: **band**, **purpose**, **review and evidence** (prompts for humans and agents—not a tooling spec).

### L1 — UI implementation (Unifier)

**Purpose:** Render interactive interfaces: components, layout, accessibility, and local UI state that does not invent authority.

**Review and evidence:** Visual and accessibility checks; loading and error states; destructive action confirmations; alignment with design tokens or agreed UI patterns.

### L2 — Adapter layer (Unifier)

**Purpose:** Bridge UI needs to backend-shaped data: formatting, view models, and client-side adaptation without smuggling business invariants out of the server’s authority.

**Review and evidence:** Clear mapping to DTOs or API fields; no duplicate domain rules that conflict with L6/L13; documented assumptions when the API changes.

### L3 — UI guards (Unifier)

**Purpose:** Route protection, feature flags at the UX level, optimistic UX constraints—**clarity** for users, not a substitute for L11.

**Review and evidence:** Users see denial and recovery paths; guards match server decisions; security-sensitive paths do not rely on hiding controls alone.

### L4 — Database (Designer)

**Purpose:** Tables, indexes, migrations, and storage choices the program depends on—owned deliberately, not as an accidental dump.

**Review and evidence:** Migration review; rollback story; performance-sensitive access patterns noted; separation from ad hoc reporting hacks where applicable.

### L5 — Entity (Designer)

**Purpose:** Typed records or aggregates representing persisted or message-carried data—bounded fields, nullability, and invariants at the shape level.

**Review and evidence:** Types align with migrations; breaking changes flagged; validation at boundaries coordinated with L7.

### L6 — Domain (Designer)

**Purpose:** Pure domain rules: invariants, policies, and calculations that express **business meaning** independent of HTTP or SQL dialect.

**Review and evidence:** Unit tests on rules; no direct I/O; conflicts with DTO or UI naming resolved through L8 mappers, not duplication.

### L7 — DTO (Designer)

**Purpose:** Stable contracts for requests, responses, events, and integration payloads—versioning and compatibility conscious.

**Review and evidence:** Schema or OpenAPI (or equivalent) updated with the change; deprecation path; validation rules co-owned with consumer expectations.

### L8 — Mapper (Designer)

**Purpose:** Translate between L7 shapes and L5/L6 meaning—one direction for ingress, one for egress—kept thin and testable.

**Review and evidence:** Mapper tests for edge cases; no hidden defaults that bypass domain rules; round-trip clarity for critical flows.

### L9 — Repository interface (Adapter)

**Purpose:** Define how the application **asks** for data: queries, commands, pagination—without leaking ORM details to L13 callers.

**Review and evidence:** Interface changes ripple to implementations deliberately; transactional boundaries visible; naming matches ubiquitous language where possible.

### L10 — Repository (Adapter)

**Purpose:** Implement L9 against real databases, APIs, or stores; enforce consistency, transactions, and idempotency choices.

**Review and evidence:** Integration tests or contract tests; N+1 and migration risk called out; alignment with L4 migrations.

### L11 — Auth and authorization (Adapter)

**Purpose:** Establish identity and enforce permissions **where operations acquire authority**—aligned with domain verbs, not only route prefixes.

**Review and evidence:** Threat model touchpoints; denial paths tested; audit or logging hooks where policy requires; no “UI-only” security claims.

### L12 — Cache (Adapter)

**Purpose:** Memoization, distributed cache, or read models that speed access without violating freshness or permission semantics.

**Review and evidence:** Invalidation rules; TTL rationale; cache poisoning and stale-read risks reviewed with L11 and L6.

### L13 — Service (Logician)

**Purpose:** Application services orchestrating use cases: call repositories, domain, integrations, and emit events with explicit ordering and failure behavior.

**Review and evidence:** Service-level tests; timeouts and retries documented; sagas or compensations where partial failure is possible.

### L14 — Events (Logician)

**Purpose:** Publish and consume async messages with contracts, ordering expectations, and idempotent handlers where needed.

**Review and evidence:** Event schema versioning; dead-letter and replay posture; consumer tests; alignment with L7 event DTOs.

### L15 — AI services (Logician)

**Purpose:** Model calls, retrieval, tool use, and guardrails—**bounded** inputs and outputs, human checkpoints when policy demands.

**Review and evidence:** Prompt and tool contracts reviewed; PII and secret handling; evaluation or golden traces for regressions; cost and latency limits.

### L16 — Integrations (Logician)

**Purpose:** Third-party APIs, partner systems, file transfers—explicit failure modes, backoff, and semantic mapping to domain outcomes.

**Review and evidence:** Sandbox or contract tests; mapping errors surfaced to operators; secrets rotation path; vendor incident assumptions.

### L17 — Middleware (Integrator)

**Purpose:** Cross-cutting pipeline: authentication handoff, correlation IDs, rate limits, request logging—**policy** without replacing L18 logic.

**Review and evidence:** Order of middleware documented; failure responses consistent; no business rules that belong in L6/L13 alone.

### L18 — Controller (Integrator)

**Purpose:** Thin adapters from transport (HTTP/RPC/message) to services: parse, authorize gate, call L13, map errors to responses.

**Review and evidence:** Controllers stay thin; input validation coordinated with L7; error contract stable for clients (L1–L3).

### L19 — Routes (Integrator)

**Purpose:** Expose paths, methods, and public API tables; versioning and deprecation surfaced consistently.

**Review and evidence:** Route map updated; breaking route changes communicated; gateway (L20) rules stay in sync.

### L20 — Gateway (Integrator)

**Purpose:** Edge policy: TLS termination, WAF, auth federation, traffic splitting, publish gates—**organizational** safety nets in addition to L11.

**Review and evidence:** Rule changes reviewed with security; canary and rollback tie to L22; rate limits match abuse expectations.

### L21 — QA and verification (Integrator)

**Purpose:** Automated tests, exploratory passes, and release checklists mapped to **risk** of the change.

**Review and evidence:** Test plan linked to ticket; critical paths covered; flake policy; non-functional checks (performance, a11y) when touched.

### L22 — System integration and release (Integrator)

**Purpose:** Deploy pipelines, environment config, feature flags, database promotion, smoke tests in **production-like** constraints.

**Review and evidence:** Rollback verified; config diff reviewed; integration smoke in staging; observability for new failure modes (conceptual: your org defines tooling).

---

## Execution flow (conceptual)

Requests and jobs rarely move in a single straight line, but a **common read/write mental path** helps teach boundaries:

**Client (L1–L3)** → **edge (L17–L20)** → **L18** uses **L7** validation and **L11** → **L13** coordinates **L6**, **L10**, **L14–L16** → persistence via **L4** — then **L21–L22** prove and ship.

Use this as a **communication aid**, not a mandatory runtime diagram.

---

## Relationship to AUTH and evidence

AUTH controls in AADM materials are mapped to layers in companion standards (for example [AUTH-aware delivery](auth-aware-delivery.md) and AUTH control references your program adopts). In general:

- **Contracts and validation** lean on **L7** (and callers L18).  
- **Enforcement** leans on **L11** adjacent to real operations (**L10**, **L13**).  
- **Publish barriers** may involve **L20** and **L22** together with organizational policy.

Your evidence system should show **which layers** a change touched and **what proof** exists per layer—not only a layer tag in a title.

---

## Further reading

- [UDALI coding personas](udali-personas.md) — five groupings (U, D, A, L, I) that roll up L1–L22  
- [Architecture principles](architecture-principles.md)  
- [AUTH-aware delivery](auth-aware-delivery.md)  
- [Build intent specification](build-intent-specification.md)  
- [UDALI layer boundaries](../standards/udali-layer-boundaries.md)  
- [Glossary](glossary.md)  
