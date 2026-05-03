# Glossary

Short definitions for terms used across this repository.

## AADM

**Agentic Authority Delivery Model** — An open framing for disciplined delivery of agentic systems: explicit intent, architecture boundaries, human checkpoints, and layer-aware execution—especially when using automation or AI agents.

AADM is **community-published guidance**, not an ISO/IEC standard, statutory requirement, or universal “certification” unless **your** organization separately asserts one.

## UDALI

**U / D / A / L / I** collaboration stances that structure AADM delivery architecture—mnemonic **UDALI**. **I** is **Integrator** (release closure: controllers, QA, deployment, integration validation), not “implementer.” Optional finer slices (~22 discussion layers) may subdivide these groupings. See [UDALI 22-layer model](udali-22-layer-model.md).

## Layer

A **conceptual stratum** of concerns (contracts, validation, persistence, observability, etc.). Layers guide responsibility; they need not map one-to-one to repository folders.

## Role hat (U / D / A / L / Integrator)

A **collaboration stance**, not a job title. Public buckets:

- **Unifier** — UI safety, user experience, adapter (client/server) boundary clarity  
- **Designer** — schema, DTOs, domain logic, mapping  
- **Adapter** — repositories, auth, authorization, caching  
- **Logician** — services, workflows, orchestration, AI services, integrations  
- **Integrator (I)** — controllers, routing, QA, deployment, integration validation  

See [UDALI 22-layer model](udali-22-layer-model.md).

## Build intent

The **declared purpose and constraints** of a change before implementation churn dominates. See [Build intent specification](build-intent-specification.md).

## BIS-lite

A **lightweight technical intent** document emphasizing interfaces, boundaries, and verification—companion to narrative product docs. Template: [`templates/bis-lite.md`](../templates/bis-lite.md).

## Contract

A **stable agreement** between producers and consumers: API schemas, event payloads, tool inputs/outputs, compatibility expectations.

## AUTH

Joint shorthand for **authentication** and **authorization**. AADM stresses separating identity establishment from permission enforcement. See [AUTH-aware delivery](auth-aware-delivery.md).

## MCP (Model Context Protocol)

A protocol for clients to obtain **structured context** from servers (resources, prompts, tools). In AADM programs, an **AADM MCP server** commonly exposes standard material to agents **alongside** this documentation repo—not as a replacement for application security. See [MCP quickstart](mcp-quickstart.md).

## Navigator, Investigator, and Sentinel (MCP workflow personas)

**Workflow-level labels** used by many AADM MCP deployments to separate **how** agents should behave across a ticket—distinct from **UDALI band hats** (Unifier, Designer, Adapter, Logician, Integrator). Treat them as **three equal paths**, not “troubleshooting only”:

- **Navigator** (`delivery`) — Planned change: **scope → verify → readiness** for net-new capability or material improvement to an existing feature.  
- **Investigator** (`defect`) — Broken versus expected: **investigate → diagnose → remediate**, with evidence-gated progression before architect-style diagnosis; primary surface is typically **`debug_defect`** (exact tool names depend on your server build).  
- **Sentinel** (`assurance`) — Prove **existing** alignment with the standard **across** vertical slices and horizontal seams—not scoped to a single feature brief **nor** a single defect thread; framing and audit-style tools, not a substitute for **`debug_defect`** on a concrete bug.

External orchestrators usually load a fixed capability snapshot (often exposed as **`standard_brief`** on reference servers) including persona selection rules and structured follow-on hints. Your application repo still owns tests, CI, and AUTH enforcement.

## standard_brief (reference MCP tool name)

On **reference** AADM MCP servers, a **`standard_brief`**-style tool returns a **versioned snapshot** of workflows, persona blocks, orchestrator-oriented contract text, and (where published) a **`follow_on_catalog`** of stable ids for host-side branching—**not** a compliance verdict. Exact JSON shapes vary by deployment; pin server version when you rely on reproducibility.

## Reference MCP tool ids

The **canonical enumeration** of tool names on the reference enforcement MCP bundle lives in [MCP quickstart — Reference AADM enforcement MCP tool ids](mcp-quickstart.md#reference-aadm-enforcement-mcp-tool-ids). Always reconcile internal docs with **`tools/list`** from the server you actually run.

## Legacy MCP tool names

Older examples or tickets may use retired identifiers. Typical replacements: **`aadm_capabilities`** → **`standard_brief`**; **`map_feature_to_layers`** → **`map_feature`**; **`udali_route_delivery`** → **`plan_delivery`**; **`aadm_troubleshooter`** → **`debug_defect`**. See the migration table in [MCP quickstart](mcp-quickstart.md#reference-aadm-enforcement-mcp-tool-ids).

## Agent

An **automated actor** (LLM-driven or otherwise) that proposes actions, code, or plans. Treated as **powerful but not authoritative** for high-risk decisions unless explicitly governed.

## Human in the loop (HITL)

Practice where **people retain approval authority** for sensitive, irreversible, or policy-bound operations. See [`standards/human-in-the-loop.md`](../standards/human-in-the-loop.md).

## Monolith (anti-pattern in AADM sense)

An **undifferentiated lump** of unrelated responsibilities—often exacerbated when agents paste cross-cutting code without boundaries. See [`standards/no-monolith-rule.md`](../standards/no-monolith-rule.md).

## Evidence

**Artifacts or signals** supporting claims—tests, metrics, audit trails, reviewed documents—appropriate to the claim’s strength.

## Adoption repo

This **public documentation repository**: templates and narrative guidance for humans and tickets. **Runtime MCP distribution** is maintained separately.
