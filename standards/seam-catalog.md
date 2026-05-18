# SEAM catalog — auditable handoff archetypes

## Purpose

**SEAM** names **which crossings** must be in scope when you trace data, audit alignment, or review agent-assisted changes. It answers: *what handoff are we proving?*

SEAM does **not** replace:

| Family | Answers |
|--------|---------|
| **AUTH** | What **must** be enforced (governance). |
| **TRACE** | What **kind of drift** failed (optional pattern labels such as TRACE-01). |
| **VERT_* / HORI_*** | **Where** in the stack assurance attestation runs (vertical depth and boundary **kind**). |

One finding may cite several families (for example SEAM-04 + TRACE-01 + AUTH-21).

---

## Why SEAM exists

Many teams trace **persistent fields** (database columns, event schemas) and miss defects where meaning changes **inside a process**—canonical agent ids, dispatch-time tool ids, alias tables with no column anchor.

SEAM adds a **checklist of crossing types** so those paths are explicitly in or out of scope—not an afterthought.

---

## Archetype families (overview)

| Id range | Theme | Examples |
|----------|--------|----------|
| **SEAM-00** | Model | Namespaces, lifecycle anchors, how to run a **SEAM check** |
| **SEAM-01–07** | Data path | Persistent field, wire ingress, DTO↔domain, domain↔persistence, egress |
| **SEAM-04–05** | In-process | Identity normalization, dispatch / orchestration composition |
| **SEAM-08–11** | Cross-cutting | Async parity, external integration, dual source of truth, trust / tenancy |
| **SEAM-12–15** | Derived & process | Computed fields, guidance–feedback UX, config / flags, UDALI role handoff |

Hosted **AADM MCP** subscribers load full archetype text (**SEAM-00** through **SEAM-15**) via the **`fetch_seam`** tool and the seam check worksheet resource. This public document states the **policy**; the MCP bundle carries operational detail aligned with **TRACE** practice ([Glossary — TRACE](../docs/glossary.md)).

---

## SEAM check (minimum discipline)

For a bounded slice:

1. Pick **in-scope SEAM-NN** ids (include **SEAM-04** and **SEAM-05** when agents, recipes, or tools dispatch with alias or synthesized ids).
2. Mark each row **PROVEN**, **FAILED**, or **DEFERRED** (named owner)—no blank rows.
3. Attach **repository evidence** (paths, tests, CI)—documentation does not substitute for proof.
4. On failure, name a **TRACE** drift class where your catalog uses one.

---

## “Seam” in other senses

| Term | Meaning |
|------|---------|
| **SEAM-NN** (this catalog) | Auditable **handoff archetype** for scope and checklists |
| **VERT_* / HORI_*** | Assurance **geometry** (depth and boundary kind) |
| **Feathers seam** | Design substitution point at a module **interface** ([Glossary — Interface](../docs/glossary.md)) |
| **Colloquial “seam”** | Any boundary—prefer **SEAM-NN** or **VERT/HORI** in audit notes |

---

## Related

- [Glossary](../docs/glossary.md) — TRACE, VERT_*, HORI_*, Seam, SEAM-NN  
- [Sentinel audit programs](../docs/sentinel-audit-programs.md)  
- [Using the AADM MCP](../docs/mcp-quickstart.md)  
- [Evidence-first defect resolution](../examples/evidence-first-defect-resolution.md)

These documents do **not** constitute legal or regulatory certification. Formal gates are defined by **your** policies, tests, and CI.
