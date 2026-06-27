# Data-flow stacks — five MECE pipelines that share an English vocabulary

> Disambiguate the small set of English words (*producer, writer, orchestrator, resolver, thrower, assembler*) that mean **different things in different data-flow pipelines**. Engineers cite a role today without saying *which pipeline* the role lives in; reviewers then argue past each other. This doctrine names the five pipelines, defines their roles, and resolves the two category collisions (SEAM-13 and realm-bridge) so the vocabulary stays MECE. Stack-agnostic.

## One-line principle

A system has **five MECE data-flow stacks** — **Mutation, Projection, Guidance, Artifact, Execution** — plus **Realm-Bridge** as a horizontal seam between them. UDALI (U → D → A → L → I) is the orthogonal delivery process; SIGL-01…12 is orthogonal per-surface proof. Cite every role with the stack it lives in (`mutation.producer`, not bare `producer`) and the collision dissolves.

---

## The five stacks (what each one moves)

| Stack | What flows | One-line shape |
|-------|------------|----------------|
| **Mutation** | A user-initiated state change | route → validate → service → command writer → emit |
| **Projection** | A derived read shape built from emitted events / write rows | write → emit → produce → read → fetch → hook → guardrail |
| **Guidance** | A *verdict* about workflow state (severity / readiness / conflict) | verdict producer → resolver → mapper → affordance (SEAM-13) |
| **Artifact** | An opaque blob with identity (file, document, payload) | parse → hash → envelope → single-dispatch persist |
| **Execution** | A side effect against the outside world (or an autonomous agent run) | preview → approve → apply  **OR**  orchestrator → dispatcher → agent → composer → accept |

**MECE test.** A given module/function belongs to **exactly one** stack at the role level, even if the same English word names a role in several. If you cannot decide, you are usually conflating Mutation (write-side state) with Projection (read-side shape) — the cut between them is *who owns the row*: the command writer owns it for Mutation, the projection writer owns a derived copy for Projection.

---

## Realm-Bridge — not a sixth stack

A **realm-bridge** is a **horizontal seam** that hands data from one stack into another or across an authority/trust realm. It is not its own pipeline because nothing originates inside it — it only forwards.

Cite a realm-bridge as a `HORI_*` boundary on attestation closure, paired with the two stacks it bridges. It never appears as a stack-qualified role.

---

## SEAM-13 / Guidance — same plumbing, two angles

Guidance is **both** a stack (the pipeline that turns rules into a verdict) **and** the outbound seam where that verdict crosses into presentation:

- **Stack view (this doctrine).** `guidance.verdict-producer → guidance.resolver → guidance.mapper → guidance.affordance` — the internal shape.
- **Seam view ([guidance-feedback-seam.md](guidance-feedback-seam.md), [SEAM-13](seam-catalog.md#seam-13--guidancefeedback)).** The same chain treated as one server-authoritative cross-cutting seam — the outbound contract.

Use the stack qualifier for internals (who produces, who resolves). Use SEAM-13 for the contract that crosses into UI. They are not competing categories.

---

## Same word, different stack — disambiguation table

This is the load-bearing payload. Stop saying bare *producer*. Always qualify.

| Term | Mutation | Projection | Guidance | Artifact | Execution |
|------|----------|------------|----------|----------|-----------|
| **producer** | — | recompiler | verdict producer | envelope builder | orchestrator output |
| **writer** | command repo | projection row writer | drift edge writer | envelope persist | terminal agent run row |
| **orchestrator** | mutation orchestrator (registry) | — | — | — | build orchestrator |
| **assembler** | — | optional page bundle (SIGL-07) | — | — | composer |
| **resolver** | — | — | stale / guidance resolver | — | eligibility / tool-id resolver |
| **thrower** | 409 at command boundary only | — | — | — | gate before dispatch |

**Reading the table.** A blank cell means the role does not exist in that stack — using the word there is a category error. "Projection orchestrator" is almost always a misnamed *recompiler* (the Projection `producer`).

**Assembler is not a sixth stack.** It is SIGL-07 — the ephemeral read-shape builder used when a compiled projection has not been cut over to yet. It lives inside the Projection stack as a transient role and inside the Execution stack as the composer that assembles an agent's final output.

---

## Orthogonal axes (do not collapse into the stacks)

Two axes cut across all five stacks. Cite them **in addition to** the stack, not instead of it.

1. **UDALI delivery process — U → D → A → L → I.** The maturity of a slice of work moving through delivery. A Mutation stack module can be at any UDALI letter; so can a Projection module.
2. **Signal Stack (SIGL-01…12).** Per-surface vertical proof from metal to UI. SIGL rows are *evidence* a surface ships correctly, regardless of which stack produced the data the surface renders.

**Forbidden conflation:** Treating UDALI letters as stacks, or SIGL rows as stacks. UDALI is *when* (delivery phase). SIGL is *what to prove* (surface evidence). Stacks are *what is flowing*.

---

## Citation grammar

When a finding, AUTH binding, or attestation touches a role, cite it as:

```
<stack>.<role> @ UDALI:<letter> [SIGL-NN] [SEAM-NN | AUTH-NN | TRACE-NN | VERT_… | HORI_…]
```

Examples:

- `mutation.command-writer @ UDALI:A AUTH-11` — write-side state, Adapter band, staleness-bound.
- `projection.producer @ UDALI:L SIGL-04` — recompiler in Logician band.
- `guidance.resolver @ UDALI:L SEAM-13` — the single resolver inside the Guidance seam.
- `execution.dispatcher → execution.agent @ HORI_realm-bridge` — agent run crossing trust realms.

Bare `producer` / `writer` / `orchestrator` without a stack qualifier should be treated by reviewers as **drift**.

---

## Anti-patterns

- **Stack drift.** Using a role name from a stack it does not exist in ("the projection thrower"). Re-cite or rename.
- **Sixth-stack creep.** Inventing a new stack when the work is actually a realm-bridge between two existing ones.
- **UDALI-as-stack.** Treating Logician or Implementer as a pipeline. They are phases of delivery, not flows.
- **SIGL-as-stack.** Treating SIGL-07 (assembler) as a peer of Mutation / Projection. It is a row of surface proof that names a role inside two stacks.
- **Unqualified roles.** Saying *the producer* in a review without stack — the next reviewer will hear a different stack and disagree without realizing it.

---

## Forbidden

- Bare role names (`producer`, `writer`) in any reviewable artifact — always stack-qualified.
- A sixth data-flow stack — model the case as Realm-Bridge.
- "Assembler stack" — it is SIGL-07, used as a role inside Projection or Execution.
- Treating UDALI letters or SIGL rows as stacks.
- Re-deriving the Guidance verdict outside `guidance.verdict-producer` (also forbidden by SEAM-13).
