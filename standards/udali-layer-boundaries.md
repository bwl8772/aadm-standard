# UDALI layer boundaries

Covers **no skipped layers** and **separation of concerns** across delivery groupings. Companion: [`aadm-core-principles.md`](aadm-core-principles.md) §§5 and 7.

---

## Definition

**Layer boundaries** (in AADM public materials: Unifier, Designer, Adapter, Logician, Integrator—see [`docs/udali-22-layer-model.md`](../docs/udali-22-layer-model.md)) organize **accountability**. Work belongs primarily where its **invariant lives**. Crossing boundaries happens through **contracts**, not through leaked implementation detail.

**No skipped layers** means: for material changes, each relevant grouping is considered and documented; silent omission is treated as unowned risk.

---

## Why it matters

Skipping shows up as production defects: UI-only validation while APIs stay porous; domain rules scattered in controllers; integrations rewriting domain meaning; missing audits because “shipping” skipped Integrator evidence.

Agentic workflows amplify this because changes touch many surfaces quickly.

---

## What good looks like

- A **layer map** per meaningful change (see [`templates/layer-map.md`](../templates/layer-map.md)).  
- **N/A** with a **one-line rationale** when a grouping truly does not apply.  
- Fixes applied at the **owning** grouping (or coordinated explicitly across groupings).  
- **Contracts** are the narrowest stable surfaces between teams or modules.

---

## Common failure mode

Symptom patching: slap validation or AUTH checks at the outer edge while the domain operation remains unchecked—or let agents paste repository calls wherever convenience dictates.

---

## Public checklist

- [ ] Layer map attached to ticket or doc for non-trivial features.  
- [ ] Cross-grouping impacts listed (contracts, AUTH, orchestration, deploy).  
- [ ] Presentation surfaces do **not** silently substitute for server-side authorization.  
- [ ] Foreign/system models isolated behind adapters—not copied into domain cores.  
- [ ] Persistence and schema choices do not leak across unrelated domains without mapping.  
- [ ] Operational scaling/retry behavior remains traceable to business outcomes.

---

## Boundary rules (normative hints)

1. **Own your invariant** — fix root cause at the grouping that owns the rule, not only where the symptom appears.  
2. **Declare crossings** — multi-grouping changes list every grouping touched.  
3. **Stable edges** — evolve contracts deliberately with compatibility notes.  
4. **AUTH meaning** — enforce permissions adjacent to domain interpretation, not only in templates.  
5. **Integration hygiene** — translate external idioms at boundaries; keep cores provider-agnostic.  
6. **Evidence** — Integrator grouping carries planned QA/deploy validation appropriate to risk.

---

## Anti-patterns

- Edge-only validation while mutations remain unguarded internally  
- Shared DTOs that blend unrelated contexts  
- Agents generating data access scattered without repository discipline  

---

## Further reading

- [`docs/udali-22-layer-model.md`](../docs/udali-22-layer-model.md)  
- [`docs/glossary.md`](../docs/glossary.md)  
