# What is AADM?

**AADM** (Architecture-Aware Delivery Model) is an **open delivery standard** for building modern software—including systems that use AI agents—without surrendering structural integrity.

## The problem AADM addresses

Fast iteration and agent assistance often collapse boundaries:

- Implementation starts before architecture intent is stable.  
- Automation reaches across layers that should remain separated.  
- Authorization and data access become implicit “someone will fix it later.”  

AADM responds with a **small set of enforceable ideas**:

1. **Intent before churn** — what you are building, for whom, and under what constraints is written down and revisable.  
2. **Named layers and roles** — work is placed in a consistent model so handoffs are explicit (see [UDALI 22-layer model](udali-22-layer-model.md)).  
3. **Human accountability** — people remain responsible for high-risk decisions; automation assists within declared limits ([Human in the loop](../standards/human-in-the-loop.md)).  
4. **No accidental monolith of concerns** — unrelated responsibilities are not fused into a single unmaintainable surface ([No monolith rule](../standards/no-monolith-rule.md)).

## What AADM is not

- **Not a framework** you install to “solve security.”  
- **Not a replacement** for language-specific best practices or platform controls.  
- **Not a certification program** (this public repo contains no proprietary scoring or exams).  
- **Not a vendor product** — adopters bring their own tooling and governance.

## What you get from adopting AADM

- Shared vocabulary across engineering, architecture, and operations  
- Templates that turn fuzzy requests into reviewable artifacts  
- A disciplined place for agent-assisted work **without** pretending agents are architects-of-record  

## Relationship to UDALI

**UDALI** names roles and layers used throughout AADM materials. Think of AADM as the **delivery posture** and UDALI as the **organizing map**. See [UDALI 22-layer model](udali-22-layer-model.md).

## Next steps

- [Architecture principles](architecture-principles.md)  
- [Build intent specification](build-intent-specification.md)  
- [Glossary](glossary.md)  
