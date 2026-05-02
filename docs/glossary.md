# Glossary

Short definitions for terms used across this repository.

## AADM

**Architecture-Aware Delivery Model** — An open standard framing for disciplined delivery with explicit architecture intent, layered responsibilities, and human accountability—especially when using automation or AI agents.

## UDALI

**U / D / A / L** collaboration stances that structure AADM delivery architecture, plus **Implementer** for release closure (controllers, QA, deployment, integration validation). Optional finer slices (~22 discussion layers) may subdivide these groupings. See [UDALI 22-layer model](udali-22-layer-model.md).

## Layer

A **conceptual stratum** of concerns (contracts, validation, persistence, observability, etc.). Layers guide responsibility; they need not map one-to-one to repository folders.

## Role hat (U / D / A / L / Implementer)

A **collaboration stance**, not a job title. Public buckets:

- **Unifier** — UI safety, user experience, adapter (client/server) boundary clarity  
- **Designer** — schema, DTOs, domain logic, mapping  
- **Adapter** — repositories, auth, authorization, caching  
- **Logician** — services, workflows, orchestration, AI services, integrations  
- **Implementer** — controllers, routing, QA, deployment, integration validation  

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

A protocol for clients to obtain **structured context** from servers (resources, prompts, tools). Useful for distributing standard definitions; not a substitute for application security. See [MCP quickstart](mcp-quickstart.md).

## Agent

An **automated actor** (LLM-driven or otherwise) that proposes actions, code, or plans. Treated as **powerful but not authoritative** for high-risk decisions unless explicitly governed.

## Human in the loop (HITL)

Practice where **people retain approval authority** for sensitive, irreversible, or policy-bound operations. See [`standards/human-in-the-loop.md`](../standards/human-in-the-loop.md).

## Monolith (anti-pattern in AADM sense)

An **undifferentiated lump** of unrelated responsibilities—often exacerbated when agents paste cross-cutting code without boundaries. See [`standards/no-monolith-rule.md`](../standards/no-monolith-rule.md).

## Evidence

**Artifacts or signals** supporting claims—tests, metrics, audit trails, reviewed documents—appropriate to the claim’s strength.

## Adoption repo

This **public documentation repository**—templates and guidance without proprietary engines or client secrets.
