# [UDALI](udali-22-layer-model.md) as AADM delivery architecture

Within **AADM** (**Agentic Authority Delivery Model**), **[UDALI](udali-22-layer-model.md)** is the **delivery architecture** your team uses to place work, reviews, and evidence in consistent buckets—before and during agent-assisted implementation.

**UDALI** is a mnemonic for **five** collaboration stances—the letters are **U, D, A, L, I**:

- **U — Unifier**  
- **D — Designer**  
- **A — Adapter**  
- **L — Logician**  
- **I — Integrator**

Together, these five groupings describe **how capability moves from idea to production** without losing boundaries.

This document is **not** a folder layout mandate and **not** proprietary tooling. Map these groupings onto your repositories, services, and pipelines in whatever structure fits—then **record** the mapping for each meaningful change ([layer map template](../templates/layer-map.md)).

---

## Why layers (groupings) matter

Delivery groupings reduce ambiguity about:

- **Where** a decision belongs  
- **Who** should scrutinize it  
- **What evidence** counts as “good enough” before scope expands  

Agentic workflows amplify mistakes because automation can touch many surfaces quickly. Shared groupings help humans and agents agree on **allowed scope** and **review ownership**.

---

## Public role groupings

Roles are **hats**, not job titles. The same person may wear different hats on different tasks.

### Unifier (U)

Focus: coherence of what users touch and how safe, understandable surfaces hang together.

- **UI safety** — predictable controls, safe defaults, failure visibility appropriate to risk  
- **User experience** — flows, affordances, clarity of system behavior  
- **Adapter boundaries** — clarity where UI or clients stop and backend contracts begin (without hiding authorization realities behind presentation-only checks)

### Designer (D)

Focus: domain meaning and the shapes that carry it across boundaries.

- **Schema** — contracts at boundaries (events, payloads, persistence shapes where owned here)  
- **DTOs** — explicit data crossing process boundaries  
- **Domain logic** — rules and invariants tied to the problem domain  
- **Mapping** — translations between external representations and domain meaning (bounded, testable)

### Adapter (A)

Focus: controlled access to data and platform capabilities.

- **Repositories** — persistence access patterns; consistency choices  
- **Auth** — identity establishment appropriate to your architecture (where your stack places it)  
- **Authorization** — permission enforcement aligned with domain operations (not “UI-only security”)  
- **Caching** — performance optimizations that must remain correct under AUTH and freshness expectations

### Logician (L)

Focus: orchestrated behavior across time and systems—where “what happens next” must remain explicit.

- **Services** — application services coordinating domain outcomes  
- **Workflows** — multi-step processes, sagas, retries/compensation where applicable  
- **Orchestration** — sequencing and policy across collaborators  
- **AI services** — model-invoking paths treated as **bounded capabilities** with explicit inputs/outputs and governance  
- **Integrations** — calls across organizational or vendor boundaries with explicit failure semantics

### Integrator (I)

Focus: shipping discipline—what must be true for software to reach users safely (integration of build artifacts into environments users touch, end-to-end validation).

- **Controllers** — HTTP handlers, RPC entrypoints, message consumers—thin edges that delegate inward  
- **Routing** — traffic rules, API gateways as relevant  
- **QA** — planned verification (automated tests, exploratory checks) mapped to risk  
- **Deployment** — promotion, rollback posture, configuration discipline  
- **Integration validation** — proving external dependencies behave as assumed in target environments  

---

## Why skipping groupings creates system risk

Skipping a grouping does not remove the underlying concern—it **relocates** it into informal channels (chat, prompts, tacit assumptions).

Typical failure modes:

| Skipped emphasis | Risk |
|------------------|------|
| **Unifier** | Unsafe UX, ambiguous failure states, “looks fine” surfaces that leak authority assumptions |
| **Designer** | Implicit schemas, unstable DTOs, domain rules scattered or duplicated |
| **Adapter** | Data access sprawl, AUTH gaps, cache incoherence under edge cases |
| **Logician** | Hidden orchestration, brittle AI paths, integrations without explicit failure handling |
| **Integrator** | Untested routing, deploy drift, “works on my machine” integrations |

These risks compound under agent-generated churn because defects replicate faster than intuition catches them.

---

## How features move through the groupings

Think of a feature as a **thread** that should leave evidence in each relevant bucket—not necessarily in strict linear order day-by-day, but **without silent gaps** by the time you ship.

A practical mental sequence:

1. **Intent and coherence (Unifier-heavy)** — Who experiences this? What must never happen by mistake? Where does the client boundary sit?  
2. **Domain truth and contracts (Designer-heavy)** — What entities and rules apply? What crosses boundaries as DTOs/schemas?  
3. **Access and enforcement (Adapter-heavy)** — How is identity established and authorization enforced next to real operations? How is data read/written and cached safely?  
4. **Behavior through time (Logician-heavy)** — What services/workflows/integration calls execute? How do AI-invoking paths stay bounded?  
5. **Shipping proof (Integrator-heavy)** — What controllers/routes expose this? What QA proves it? How is it deployed and validated against real integrations?

Real teams iterate—early spikes may start mid-stack—but **promotion to production** should recover missing evidence rather than pretending skipped areas “don’t apply.”

---

## Public layer checklist

Use this per meaningful change (adjust wording to your program). Check **N/A** only with a one-line rationale recorded in your ticket or doc.

### Unifier

- [ ] UX states cover success, failure, and permission denial clearly  
- [ ] UI safety reviewed for destructive or irreversible actions  
- [ ] Client/server boundary documented (what the UI assumes vs what the API guarantees)

### Designer

- [ ] Schemas/DTOs updated deliberately (compatibility noted)  
- [ ] Domain logic changes reviewed for invariant breakage  
- [ ] Mappings remain bounded and testable

### Adapter

- [ ] Repository access consistent with transactional expectations  
- [ ] Auth path appropriate for the surface  
- [ ] Authorization enforced where domain meaning is interpreted  
- [ ] Caching choices reviewed for staleness and correctness

### Logician

- [ ] Service/workflow behavior explicit (including failure modes)  
- [ ] Integration assumptions documented  
- [ ] AI-invoking paths have scoped inputs/outputs and human checkpoints where required by policy

### Integrator

- [ ] Controllers/routes remain thin; delegation matches grouping responsibilities  
- [ ] QA evidence planned and executed for the risk level  
- [ ] Deployment notes include rollback/feature-flag posture if used  
- [ ] Integration validation performed in an environment representative of production constraints

---

## What “no feature ships until it clears the layers” means

This slogan is **not** claiming every checkbox requires a separate committee. It means:

- **Traceability:** For each grouping touched by the change, there is **named evidence** (review, test, runbook note, or explicit N/A rationale).  
- **No silent skips:** If a grouping looks “not involved,” the team records why—especially for AUTH, workflows, and deploy surfaces.  
- **Human authority:** Where your policy requires human approval (high-risk operations, broad privilege changes, irreversible data actions), that checkpoint remains **outside** informal agent output.  

If something ships without clearing its relevant groupings, you are accepting **unowned risk**—often invisible until production teaches it.

---

## Relationship to “22 layers”

Many teams subdivide these five groupings into finer slices for discussion (historically toward **~22** concerns across intent, contracts, validation, persistence, observability, pipeline, and operations). That finer map is optional. The **five public groupings** remain the accountability spine: they decide **what kind of review** a change needs.

---

## Further reading

- [Architecture principles](architecture-principles.md)  
- [AUTH-aware delivery](auth-aware-delivery.md)  
- [Build intent specification](build-intent-specification.md)  
- [UDALI layer boundaries](../standards/udali-layer-boundaries.md)  
- [Glossary](glossary.md)  
