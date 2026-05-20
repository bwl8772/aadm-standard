# Signal Stack compliance

Normative short for **multi-surface products** where each **named UI surface** (page, editor, wizard step, panel) must expose a complete vertical stack from persistence through presentation. Companion: [Guidance–feedback seam](guidance-feedback-seam.md) (status slice of the stack), [UDALI layer boundaries](udali-layer-boundaries.md), [Sentinel audit programs](../docs/sentinel-audit-programs.md), [Architecture principles](../docs/architecture-principles.md) §12.

---

## Problem

Teams ship many screens that all show **workflow state**, **next actions**, and **gates**—but document ownership per feature instead of per **layer**. That produces split-brain status (each page computes its own “stale”), duplicate client mappers, UI that invents reveal rules, and assurance passes that cannot answer “where does this behaviour live?” in one place.

---

## One-line principle

For every in-scope UI surface, maintain **one SURF column**: twelve **SIGL** rows (metal → UI → reveal), each with **repository proof**, an explicit **gap**, or **n/a by design**—and enforce **row parity** so the same layer behaves the same way on every surface.

**Signal Stack** = the program (twelve layers × N surfaces). **SURF** = one surface’s filled column. **SIGL-NN** = one layer row in the stack (not a UDALI layer number—cite both when useful, e.g. SIGL-08 with UDALI **L13**).

---

## Naming (avoid confusion)

| Term | Meaning |
|------|---------|
| **Signal Stack** | The discipline / matrix program |
| **SIGL-01 … SIGL-12** | Stack layer row ids for findings and checklists |
| **SURF** | One surface’s attestation column (from **sur**face) |
| **Guidance–feedback seam** | Horizontal pattern for verdict → resolver → affordance (subset of SIGL-08–12) |

Do **not** use **SIG** alone as a label when your product already uses “Signal” for another domain (gates, intelligence, messaging)—use **Signal Stack** or **SIGL-NN**.

---

## Twelve stack layers (SIGL)

These rows describe **product placement** for one surface. Typical [UDALI 22-layer](../docs/udali-22-layer-model.md) mapping is shown for orientation; organizations may align slices differently if handoffs stay explicit.

| SIGL | Layer | Typical UDALI slice | What must be locatable |
|------|-------|---------------------|-------------------------|
| **SIGL-01** | Database column | ~L5 (persistence) | Tables, constraints, tenant scope |
| **SIGL-02** | Schema mapping | ~L6 | ORM/schema rows ↔ columns |
| **SIGL-03** | Typed contract / DTO | ~L7 | Versioned read/write shapes |
| **SIGL-04** | Registry / enum | ~L8 | Phases, gate ids, 409 reason codes |
| **SIGL-05** | Write command | ~L9 | Mutations, conflict emitters |
| **SIGL-06** | Read query | ~L10 | Tenant-scoped reads |
| **SIGL-07** | Read-model service | ~L11 | One assembler per surface route |
| **SIGL-08** | Verdict producer | ~L13 | Server status / readiness / blocking |
| **SIGL-09** | Action eligibility | ~L14 | Allowed actions, phase gates |
| **SIGL-10** | Single client mapper | ~L15 | Verdict → affordance (one resolver policy) |
| **SIGL-11** | UI component | ~L21 | Dumb renderer of affordance only |
| **SIGL-12** | Visibility / reveal | ~L22 | Hidden → shown → enabled → blocked |

---

## Matrix semantics

| Direction | Question |
|-----------|----------|
| **Column down** (one **SURF**) | Is this surface **ship-ready**—all twelve rows addressed? |
| **Row across** (one **SIGL**) | Do all surfaces share the same shape at this layer (or file an explicit **gap**)? |

**Cell states** (recorded in **your** repository or ticket system):

| State | Meaning |
|-------|---------|
| **path:line** (or equivalent anchor) | Authoritative implementation cited |
| **gap** | Known coverage hole—track owner; not necessarily a production defect today |
| **n/a** | Explicitly absent by design for this surface |
| **partial** | Present but not uniform—document risk until parity |

---

## Invariants (normative)

1. **No parallel verdict producer (SIGL-08).** One server-authoritative computation of status per product policy—or document **gap** and risk. Surfaces must not re-derive “stale” or “blocking” locally.
2. **No parallel resolver (SIGL-10).** One mapping from verdict to renderable affordance everywhere it applies—or **gap** with a migration plan. The **deletion test** applies: removing a legacy map must not break callers.
3. **Dumb UI (SIGL-11).** Components consume affordance contracts only; they do not compose severity, next-action copy, or readiness logic.
4. **Server-shaped reveal (SIGL-12).** Every show/hide/enable rule traces to SIGL-08, SIGL-09, or SIGL-04—not ad-hoc UI-only conditions.

Layers SIGL-01–07 bound **data and contracts** for the surface; SIGL-08–12 implement [Guidance–feedback seam](guidance-feedback-seam.md) when the surface shows system-status UX.

---

## Relation to other citation families

Signal Stack is a **product lattice** for assurance—it is **not** a peer of **AUTH** or **SEAM** catalogs.

| Family | Role |
|--------|------|
| **AUTH-NN** | Obligations (e.g. no client inference, server-derived state) |
| **SEAM-NN** / **HORI_*** | Which boundary **types** are in scope (e.g. schema, **guidance_feedback**) |
| **VERT_*** | Coarse UI→metal attestation hops |
| **TRACE-NN** | Optional contract-drift **patterns** on handoffs |
| **L1–L22** | UDALI **placement** (fine-grained) |
| **SIGL-NN** | Which **stack row** failed or is missing on a surface |
| **SURF:&lt;name&gt;** | Optional tag for a completed surface column attestation |

Example finding: **SIGL-10** + **L15** + **SEAM-13** + **AUTH-22** + **SURF:EmployeeProfile**.

---

## Architecture review questions

1. For this surface, can you name one owner path per SIGL row—or a filed **gap** / **n/a**?
2. At SIGL-08 and SIGL-10, is there exactly one producer and one resolver policy, or split-brain across surfaces?
3. Does SIGL-11 read projection/affordance only, or embed business rules?
4. For SIGL-12, does each reveal rule cite a server signal (verdict, eligibility, registry phase)?
5. Across surfaces at the same SIGL row, is behaviour **parity** or an explicit **gap** register?

---

## Forbidden patterns

- Per-surface “is it stale?” or “what’s next?” logic at SIGL-08 without a shared producer policy  
- Duplicate guidance/stale mappers at SIGL-10  
- UI components that compute status severity or next-action labels at SIGL-11  
- Reveal rules invented only in the client at SIGL-12  
- Marking a surface “done” with blank SIGL cells  

---

## Evidence (what assurance expects)

Documentation **frames** the pass; **your application repository** (paths, tests, CI) **proves** it.

| Claim | Evidence anchor |
|-------|-----------------|
| Column complete | Twelve SIGL rows filled for the **SURF** name |
| Verdict integrity | SIGL-08 path + tests; aligns with [Guidance–feedback seam](guidance-feedback-seam.md) |
| Mapper parity | SIGL-10 deletion test or inventory of legacy maps |
| Dumb UI | SIGL-11 components consume affordance only |
| Reveal traceability | SIGL-12 rules mapped to server signals |

Sentinel passes should include **guidance_feedback** / **HORI_GUIDANCE_FEEDBACK** when status UX is in scope, plus vertical/horizontal matrices per [Sentinel audit programs](../docs/sentinel-audit-programs.md). Multi-surface products may add an **authoring-surface / Signal Stack** slice: attest one **SURF** column before declaring the surface ready.

---

## Delivery lanes

| Lane | Use |
|------|-----|
| **Navigator** | Define read-model + verdict + mapper contracts **before** per-screen UI churn ([Architecture before code](architecture-before-code.md)). |
| **Investigator** | Symptoms such as hidden CTAs, wrong banners, or disagreeing screens → locate **one SIGL row**, fix there, then check **row parity**. |
| **Sentinel** | Attest SURF columns and seam matrices; proof or deferred risk—no documentation-only sign-off. |

---

## Optional: hosted MCP

Teams using the **hosted AADM MCP** ([www.aadm.io](https://www.aadm.io), endpoint `https://mcp.aadm.io/mcp`) may load scaffolds and assurance routing documented in [Using the AADM MCP](../docs/mcp-quickstart.md). The **standard in this repository** remains authoritative for humans, policy, and citations; hosted tools do **not** scan your repository or issue compliance scores.

---

## Public checklist

- [ ] Signal Stack program adopted for products with multiple workflow surfaces.  
- [ ] Each in-scope surface has a named **SURF** with twelve SIGL rows addressed.  
- [ ] SIGL-08–10 invariants satisfied or **gap** registered with owner.  
- [ ] Row parity reviewed for shared layers (especially SIGL-08, SIGL-10).  
- [ ] Findings cite **SIGL-NN** (and UDALI **L*** / AUTH / SEAM as appropriate).  
- [ ] Sentinel pass records horizontal **guidance_feedback** when status UX is in scope.

Blank column template: [`templates/surf-checklist.md`](../templates/surf-checklist.md).
