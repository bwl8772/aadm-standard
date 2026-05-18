# Glossary

Short definitions for terms used across this repository.

## AADM

**Agentic Authority Delivery Model** — An open framing for disciplined delivery of agentic systems: explicit intent, architecture boundaries, human checkpoints, and layer-aware execution—especially when using automation or AI agents.

AADM is **community-published guidance**, not an ISO/IEC standard, statutory requirement, or universal “certification” unless **your** organization separately asserts one.

## UDALI

**U / D / A / L / I** collaboration stances that structure AADM delivery architecture—mnemonic **UDALI**. **I** is **Integrator** (release closure: controllers, QA, deployment, integration validation). See [UDALI coding personas](udali-personas.md). Optional numbered slices (**L1–L22**) subdivide those groupings for discussion; see [UDALI 22-layer model](udali-22-layer-model.md).

## Layer

A **conceptual stratum** of concerns (contracts, validation, persistence, observability, etc.). Layers guide responsibility; they need not map one-to-one to repository folders.

## Role hat (U / D / A / L / Integrator)

A **collaboration stance**, not a job title. Public buckets:

- **Unifier** — UI safety, user experience, adapter (client/server) boundary clarity  
- **Designer** — schema, DTOs, domain logic, mapping  
- **Adapter** — repositories, auth, authorization, caching  
- **Logician** — services, workflows, orchestration, AI services, integrations  
- **Integrator (I)** — controllers, routing, QA, deployment, integration validation  

See [UDALI coding personas](udali-personas.md) and [UDALI 22-layer model](udali-22-layer-model.md).

## Module

A **cohesive unit of implementation** with a named owner and an explicit boundary. Reviews ask what the Module promises to callers and what it must **not** reach across without an Interface.

## Interface

The **contract surface** between a Module and its collaborators: APIs, events, types, error shapes, or other stable agreements. Design discussions often focus **leverage** (what the Interface lets callers depend on) and **locality** (what stays internal to the Module).

## Seam

A place where **responsibility or trust changes**—for example wire ↔ validated shape ↔ domain ↔ persistence, or your code ↔ an external provider. Sentinel-style audits and contract traces bind evidence **per seam** so drift is visible.

## Guidance–feedback seam

A **horizontal** cross-cutting pattern for workflows that tell users *what state they are in* and *what to do next* (nudges, recommendations, readiness, conflict copy, gates). Normative shape: **verdict** (server truth, Logician-owned) → **resolver** (exactly one) → **affordance** (one renderer on every surface). See [`standards/guidance-feedback-seam.md`](../standards/guidance-feedback-seam.md).

## SEAM-NN (handoff archetype catalog)

Stable ids (**SEAM-00** … **SEAM-15** in the hosted MCP bundle) for **which crossing types** must appear in a trace or assurance pass—persistent columns, wire ingress, normalization at dispatch, async parity, and similar. **SEAM** is scope; **TRACE-NN** is drift class; **AUTH-NN** is obligation; **VERT_* / HORI_*** is attestation geometry. Policy overview: [`standards/seam-catalog.md`](../standards/seam-catalog.md).

## Build intent

The **declared purpose and constraints** of a change before implementation churn dominates. See [Build intent specification](build-intent-specification.md).

## BIS-lite

A **lightweight technical intent** document emphasizing interfaces, boundaries, and verification—companion to narrative product docs. Template: [`templates/bis-lite.md`](../templates/bis-lite.md).

## Contract

A **stable agreement** between producers and consumers: API schemas, event payloads, tool inputs/outputs, compatibility expectations.

## TRACE (contract trace spine)

An **optional** practice for proving that **critical field meanings survive handoffs** (wire, DTO, domain, persistence, events, partner APIs): bound a **slice**, pick **3–5 critical fields**, walk **material seams**, and record **PASS / FAIL / DEFERRED** with **application-repository evidence** (paths, tests, CI) or an explicit gap owner.

Organizations that need machine-stable labels sometimes use **`TRACE-NN`** pattern ids in internal catalogs; **this public repo** describes the **practice** only. Pin **your** catalog version when reproducibility matters.

## Attestation dimensions (attestation geometry)

The **three inputs** that shape what a Sentinel assurance pass must prove. Together they define the geometry of the closure:

- **Attestation depth** (`narrow` / `standard` / `expansive`) — how wide proof is for this pass; controls required vs latent tiers.  
- **Vertical claim** — the deepest stack hop this attestation claims to cover (e.g. `presentation_only`, `through_domain_and_data`, `full_vertical_ui_to_metal`). Vertical = **depth**: one feature traced through all layers.  
- **Horizontal seams** — which cross-cutting boundary types are explicitly in play (e.g. `schema_contract`, `auth_trust_boundary`, `async_or_event`, `guidance_feedback`). Horizontal = **breadth**: one seam type checked across all features that cross it.

The hosted MCP tool **`sentinel_closure`** accepts these three dimensions and returns deterministic **required / additional / latent** seam tiers. See also: VERT_* and HORI_* (below), attestation closure.

## Attestation closure

The **deterministic output** of supplying attestation dimensions to `sentinel_closure`: a list of seams (vertical, horizontal, meta) each classified as **required**, **additional**, or **latent** — with proof hints per seam. The closure tells you *what evidence you must bind* before attestation is defensible.

## VERT_* and HORI_* (Sentinel seam ids)

Short **machine-stable ids** for **scope-of-attestation seams** in assurance workflows — the finding-level output of an attestation closure:

- **`VERT_*`** — coarse vertical hops: `VERT_UI`, `VERT_APP`, `VERT_DOMAIN`, `VERT_DATA`, `VERT_PLATFORM`.  
- **`HORI_*`** — horizontal boundary kinds: `HORI_SCHEMA`, `HORI_INTEGRATION`, `HORI_AUTH`, `HORI_EXTERNAL`, `HORI_ASYNC`, `HORI_GUIDANCE_FEEDBACK`.

They are **not** UDALI **L1–L22** layer numbers. Hosted MCP implementations expose the corresponding input values on `sentinel_closure`. Exact enumerations are **organizational** — this glossary names the concepts only.

## AUTH

Joint shorthand for **authentication** and **authorization**. AADM stresses separating identity establishment from permission enforcement. See [AUTH-aware delivery](auth-aware-delivery.md).

## Navigator, Investigator, and Sentinel (delivery lanes)

**Human-facing workflow labels** for how work should be framed—distinct from **UDALI band hats** (Unifier, Designer, Adapter, Logician, Integrator). Treat them as **three equal paths**, not “troubleshooting only”:

- **Navigator** — Planned change: scope → verify → readiness for net-new capability or material improvement.  
- **Investigator** — Broken versus expected: investigate → diagnose → remediate, with **evidence before deep architectural guesses**.  
- **Sentinel** — Assurance: prove **existing** alignment with declared standards **across** vertical slices and horizontal seams—not the same narrow scope as a single defect thread.

Your application repository still owns tests, CI, and AUTH enforcement.

## Skill (agent skill)

A **packaged playbook** an agent runtime loads for recurring tasks—often authored as a skill file (for example `SKILL.md` in some products). Skills benefit from **explicit delivery lanes**, links to [templates](../templates/README.md), and boundaries that match [AUTH-aware delivery](auth-aware-delivery.md). Authoring guidance: [Creating and aligning skills with the AADM standard](skills.md).

## Agent

An **automated actor** (LLM-driven or otherwise) that proposes actions, code, or plans. Treated as **powerful but not authoritative** for high-risk decisions unless explicitly governed.

## Human in the loop (HITL)

Practice where **people retain approval authority** for sensitive, irreversible, or policy-bound operations. See [`standards/human-in-the-loop.md`](../standards/human-in-the-loop.md).

## Monolith (anti-pattern in AADM sense)

An **undifferentiated lump** of unrelated responsibilities—often exacerbated when agents paste cross-cutting code without boundaries. See [`standards/no-monolith-rule.md`](../standards/no-monolith-rule.md).

## Evidence

**Artifacts or signals** supporting claims—tests, metrics, audit trails, reviewed documents—appropriate to the claim’s strength.

## Adoption repo

This **public documentation repository**: templates and narrative guidance for humans and tickets. **Coding agents:** hosted MCP instructions on **[www.aadm.io](https://www.aadm.io)**; endpoint **`https://mcp.aadm.io/mcp`** — [Using the AADM MCP](mcp-quickstart.md).
