# What is AADM?

**AADM** (**Agentic Authority Delivery Model**) is an **open delivery posture** for building modern software—including systems that use AI agents—without surrendering structural integrity.

AADM is **not** a statute, ISO publication, or automatic “certification.” It is **documentation-first guidance** your organization may adopt, extend, or pair with **your own** governance and tooling.

## The problem AADM addresses

Fast iteration and agent assistance often collapse boundaries:

- Implementation starts before architecture intent is stable.  
- Automation reaches across layers that should remain separated.  
- Authorization and data access become implicit “someone will fix it later.”  

AADM responds with a **small set of enforceable ideas**:

1. **Intent before churn** — what you are building, for whom, and under what constraints is written down and revisable.  
2. **Named layers and roles** — work is placed in a consistent model so handoffs are explicit (see [UDALI coding personas](udali-personas.md) and [UDALI 22-layer model](udali-22-layer-model.md)).  
3. **Human accountability** — people remain responsible for high-risk decisions; automation assists within declared limits ([Human in the loop](../standards/human-in-the-loop.md)).  
4. **No accidental monolith of concerns** — unrelated responsibilities are not fused into a single unmaintainable surface ([No monolith rule](../standards/no-monolith-rule.md)).

## What AADM is not

- **Not a framework** you install to “solve security.”  
- **Not a replacement** for language-specific best practices or platform controls.  
- **Not a legal or regulatory certification**—this public repo offers no exams, seals of approval, or proprietary scoring engines.  
- **Not a runtime** — this repository publishes **markdown** for humans and tickets; proof lives in **your** tests and CI ([Repository scope](repository-scope.md)).

## Standard artifacts vs proof

- **Here:** principles, templates, examples, and vocabulary—easy to fork, review in PRs, and attach to tickets.  
- **In your application:** failing tests, CI gates, code review, and operational signals that **prove** behavior.  

Shared language helps: **Module**, **Interface**, **Seam**, UDALI placement, AUTH themes, and delivery lanes (**Navigator**, **Investigator**, **Sentinel**)—see [Glossary](glossary.md).

## Using the AADM MCP (hosted)

For **coding agents**, AADM also exposes a **hosted MCP**: overview and **connection instructions** on **[www.aadm.io](https://www.aadm.io)**, MCP URL **`https://mcp.aadm.io/mcp`**. This repo links there rather than duplicating setup steps—see [Using the AADM MCP](mcp-quickstart.md).

## Optional contract trace (TRACE) and SEAM scope

When the dominant risk is **data contract drift**, teams may add a **contract trace** slice: bound the slice, pick critical fields, walk material seams, record PASS / FAIL / DEFERRED with repo anchors. Practice is described in [Sentinel audit programs](sentinel-audit-programs.md) and the [Glossary](glossary.md).

Use **SEAM-NN** archetypes ([`standards/seam-catalog.md`](../standards/seam-catalog.md)) to declare **which crossing types** are in scope—including normalization and dispatch paths that never appear as database columns. Hosted MCP subscribers load archetypes with **`fetch_seam`** ([MCP quickstart](mcp-quickstart.md)).

## What you get from adopting AADM

- Shared vocabulary across engineering, architecture, and operations  
- Templates that turn fuzzy requests into reviewable artifacts  
- A disciplined place for agent-assisted work **without** pretending agents are architects-of-record  

## Relationship to UDALI

**UDALI** names roles and delivery groupings used throughout AADM materials. Think of AADM as the **delivery posture** and UDALI as the **organizing map**. See [UDALI coding personas](udali-personas.md) for the five groupings (U, D, A, L, I) and [UDALI 22-layer model](udali-22-layer-model.md) for the optional **L1–L22** slice.

## Next steps

- [UDALI coding personas](udali-personas.md) and [UDALI 22-layer model](udali-22-layer-model.md)  
- [Architecture principles](architecture-principles.md)  
- [Build intent specification](build-intent-specification.md)  
- [Glossary](glossary.md)  
- [Creating and aligning skills with the AADM standard](skills.md)  
- [Using the AADM MCP](mcp-quickstart.md) · [www.aadm.io](https://www.aadm.io)  
- [Repository scope](repository-scope.md)  
