# No monolith rule

A foundational AADM principle. Companion: [`aadm-core-principles.md`](aadm-core-principles.md) §4.

---

## Definition

In AADM terms, a **monolith** is not automatically “one deployable unit.” It is an **undifferentiated accumulation** of unrelated responsibilities behind a boundary—where cohesion is weak, ownership fuzzy, and interfaces accidental—often accelerated when automation adds code without structural discipline.

The rule: **do not merge unrelated concerns** into one unmaintainable lump **without an explicit architectural decision** that records why consolidation is justified and what internal boundaries remain.

---

## Why it matters

Low-cohesion bundles obscure defects, duplicate AUTH checks inconsistently, and magnify blast radius. Agent-assisted throughput makes the pile grow faster than humans can safely review.

---

## What good looks like

- **Bounded contexts** (or equivalent modules) with **explicit contracts** between them.  
- Shared libraries have a **single clear purpose** and an owner.  
- When services are merged, the decision captures **remaining internal seams**, **AUTH**, and **observability** responsibilities.

---

## Common failure mode

An ever-growing `common`, `utils`, or `helpers` package importing half the system—or a single service implementing unrelated product verticals with cyclic imports and scattered authorization.

---

## Public checklist

- [ ] New features land in a named area aligned to responsibility (domain, integration, presentation).  
- [ ] Shared code growth triggers an ownership review.  
- [ ] Consolidation proposals include: rationale, internal boundaries, AUTH/data/observability impact.  
- [ ] Cyclic dependency trends are treated as signals, not tolerated silently.  
- [ ] Integration-specific logic stays behind **adapters**, not spread through domain cores.

---

## Signals you may be drifting

- Authorization duplicated inconsistently across many entrypoints  
- Files that change for unrelated reasons on every feature  
- Imports that fan out across most of the codebase from one module  

---

## Further reading

- [`docs/architecture-principles.md`](../docs/architecture-principles.md)  
- [`standards/udali-layer-boundaries.md`](udali-layer-boundaries.md)  
