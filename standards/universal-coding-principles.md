# Universal coding principles

> Skeptical checklist before scope grows, contracts harden, or code spreads. Complement to [`universal-engineering-truths.md`](universal-engineering-truths.md) (twenty stack-agnostic truths) and [`engineering-doctrine-top-10.md`](engineering-doctrine-top-10.md) (priority-ordered doctrines).

## Truths that stay true across stacks

| Theme | Practice |
|-------|----------|
| **Simplicity** | Prefer the design you can explain in one breath; delete speculative generalization. |
| **Explicit boundaries** | Declare inputs, outputs, errors, and ownership at seams — silence is not neutrality. |
| **Testability** | If it is hard to test, the design is probably hiding dependencies or time. |
| **Fail visibly** | Errors should be structured, attributable, and user-respecting — not swallowed. |
| **Security & privacy** | Treat data classification, authorization, and secret handling as design inputs. |
| **Change safety** | Small steps, reversible commits, and clear rollback beat heroic merges. |
| **Consistency** | Match existing patterns unless there is a documented reason and migration path. |
| **Honest scope** | "Out of scope" is a feature — it protects delivery and review load. |

---

## Phase guide

### Phase 1 — Scope

Challenge vague ambition early. A scope worth building names the beneficiary, the constraint, and the cost of delay. If "everyone" is the user, you still don't have a scope.

**Red flags.**

- No explicit "not now" list.
- Success defined only as "we shipped code."
- Dependencies on people or systems you cannot access or simulate.

### Phase 2 — Define (requirements / contracts)

Replace opinion with checkable statements. "Fast" needs a number or a scenario; "secure" needs a threat or control reference; "done" needs observable acceptance.

**Red flags.**

- Requirements that cannot fail — if nothing falsifies them, they are not requirements.
- Hidden compatibility promises (published interfaces to other modules, stored data, mobile clients).
- Mixing user stories with implementation prescriptions without separation.

### Phase 3 — Architect

Architecture is the set of decisions that are expensive to reverse. Name modules, seams, and failure ownership before optimizing locally — use the [Module–Interface–Seam vocabulary](module-interface-seam-vocabulary.md) exactly.

**Related patterns.**

- **Multi-page workflows.** When several pages serve different user jobs but one business process (same aggregate, shared saves, shared "what's next"), use [`shared-backend-separate-pages.md`](shared-backend-separate-pages.md) — separate UI, shared backend spine.
- **Logic placement.** When debating business rules in the frontend vs backend, use [`business-logic-placement.md`](business-logic-placement.md) — authority, multi-client truth, topology trade-offs.

**Red flags.**

- New abstraction without two concrete call sites that need it.
- "We will observe it in production" as the only failure plan.
- Cross-cutting concerns (auth, logging, retries) smeared into random layers.
- Each page implements its own copy of the same authoring or workflow rules — duplicate platforms.

### Phase 4 — Implement

Implementation proves the plan. Prefer the smallest diff that demonstrates behavior; let tests and CI argue with you early.

**Related.**

- Repeated bugs or repeated clarifications in a thread often mean you owe an invariant, doc, or seam fix — not another one-off. See [`operational-design-heuristics.md`](operational-design-heuristics.md).
- For singleton vs injection, default to explicit composition; justify globals with coordination and tenant safety.

**Red flags.**

- Drive-by refactors bundled with the feature.
- Copy-paste diverging from an existing pattern without noting why.
- Silent defaults that change behavior for existing users.

---

## Relationship to lanes

- **Navigator (delivery).** Use this gate at Scope and Verify — before broadening files or dependencies.
- **Sentinel (assurance).** Pair with seam and evidence framing — principles ask *why* proof should exist, not *what score you earned*.
- **Investigator (defect).** If work is broken vs expected, start with the Investigator stages — do not substitute this checklist for classified remediation.

---

## Closing

Good engineering is not maximal engineering. When this document conflicts with local team standards, **local documented standards win** — after you reconcile the gap explicitly.
