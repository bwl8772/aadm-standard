# Shared backend, separate pages — one workflow, many screens

> When multiple UI surfaces should stay **separate experiences** while **sharing one implementation spine** for business rules, persistence, and orchestration. Stack-agnostic.

## Headline

Use **one shared backend** (services, domain rules, persistence paths) and **separate pages or routes** when the screens are **different jobs for the user** but **the same underlying business process**.

---

## Illustrative shape

Think of flows where several screens each do something distinct — scope, structure, fine-grained editing — but mutations apply to the same aggregate (course, module, interaction, campaign, pipeline stage, case file, etc.), permissions are unified, and "what happens next" is workflow state, not navigation whim.

**A common failure mode.** Each page behaves like its own mini-platform, with duplicated save rules, auth checks, and orchestration. Reviews then show the same rule updated in one place and forgotten in another.

**Target end state.** Do **not** merge the user experiences into one undifferentiated screen. **Merge the logic underneath** so rules live once.

---

## Criteria — when to use this pattern

Treat this pattern as a strong fit when **three or more** of the following are true:

1. **Same object.** All pages operate on the same primary entity.
2. **Same workflow.** Actions on page A change what page B or C may allow or show next.
3. **Same permissions.** The same user and authorization rules apply across those pages.
4. **Same save or update logic.** Multiple pages persist overlapping or related fields of the same backend record or aggregate.
5. **Same "next step" logic.** The system must route or gate progression consistently (not ad hoc per page).
6. **Same AI or orchestration logic.** Prompting, generation, validation, or recommendations are shared across pages (not copy-pasted variants).
7. **Shared cache or query keys start coupling pages.** Use this as a **warning sign**: caching should support the architecture, not replace explicit shared services. If cache shape is the only thing tying pages together, you likely still owe a real shared workflow boundary.

---

## UDALI rule of thumb

- **Separate pages** when the user jobs differ (different tasks, different mental models, different guardrails in the UI).
- **Shared backend** when the business rules are the same (one workflow, one aggregate, one permission story).

### Mapped to UDALI bands

| Band | Role here |
|------|-----------|
| **Unifier (L1–L3)** | Separate pages, layouts, and UX affordances per job. |
| **Designer (L4–L8)** | Shared schema, entities, DTOs, and meaning — one contract for the aggregate. |
| **Adapter (L9–L12)** | Shared repositories, auth enforcement, caching strategy — not per-page forks. |
| **Logician (L13–L16)** | Shared workflow and orchestration; one place for "what state allows what." |
| **Integrator (L17–L22)** | Separate routes and UI entry points, tests and wiring that all call the same services or application APIs. |

---

## Non-coder tests

**Rule synchronization.**

> If I change a business rule on one page, do I have to remember to change it on another page?

If yes, you need a shared service or domain workflow, not duplicated page logic.

**Journey vs platform.**

> Are these pages different screens for the same journey?

If yes: **separate UI, shared logic underneath.**

---

## Pattern summary

| Merge | Do not merge |
|-------|---------------|
| Domain rules, workflow state, validation, persistence orchestration | Distinct user tasks and screen-level UX |
| Permission checks and "what's allowed next" | One giant undifferentiated page "because reuse" |

---

## Closing

One workflow spine, many purposeful surfaces — not many silent copies of the same spine.

---

## Related

- [`business-logic-placement.md`](business-logic-placement.md) — where decisions belong.
- [`udali-layer-boundaries.md`](udali-layer-boundaries.md) — layer accountability.
- [`operational-design-heuristics.md`](operational-design-heuristics.md) — repeated bugs signal missing spine.
