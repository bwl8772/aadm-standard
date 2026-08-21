# Multi-agent restraint

> Normative short for **when a second agent is justified**, and for governing agent-to-agent authority as carefully as agent-to-tool authority. Stack-agnostic.

Catalogued as [`agentic-failure-modes.md`](agentic-failure-modes.md) **AGENT-09**. Primary obligations: [AUTH-45 (Agent Loop & Budget)](auth-catalog.md#auth-45--agent-loop--budget-authority), [AUTH-46 (Context Trust Boundary)](auth-catalog.md#auth-46--context-trust-boundary-authority), [AUTH-14 (Approval)](auth-catalog.md#auth-14--approval-authority).

---

## Problem

Multi-agent systems are usually designed by analogy to an organization chart. The work has a planning part, a research part, a writing part and a review part, so the system gets a planner, a researcher, a writer and a critic. The decomposition feels principled because it mirrors how people would divide it.

But the reason a human team splits work is that humans cannot be duplicated, parallelized, or called as functions. None of those constraints apply here, and the analogy imports a structure whose justification does not transfer.

What it does import is cost. Each agent adds non-determinism, latency, spend, and a boundary. The boundaries are the expensive part: they are almost always implicit trust, where agent B accepts agent A's output as established fact, so an early error becomes a premise rather than a symptom and the chain reports confident nonsense.

---

## One-line principle

A second agent is justified only when the work requires **independent non-deterministic judgement, a different authority, or hard isolation** — and every agent-to-agent boundary is an **authority boundary and a trust boundary**, governed exactly as strictly as agent-to-tool.

---

## The justification test

Before adding an agent, the cheaper options are ruled out in order. Each is more predictable, cheaper, and easier to test than the one after it.

| Option | Use when | Why prefer it |
|--------|----------|---------------|
| **1. A function** | The step is deterministic — parse, validate, transform, calculate, look up | No non-determinism, no cost, fully testable. Most "agents" in a pipeline are this |
| **2. A tool** | The step needs a system effect but not judgement | Governed by the existing tool contract; one authorization surface ([`agent-tool-design.md`](agent-tool-design.md)) |
| **3. Another step in the same agent** | The step needs judgement, on the same task, with the same authority | Shared state, one budget, one run record, no boundary to govern |
| **4. A second agent** | One of the three justifications below actually holds | Only now |

The question that settles most cases: **would a deterministic implementation be worse?** If a function would produce the same result more reliably, the agent is decoration with a cost.

### The three legitimate justifications

**Different authority.** The work must run under a narrower or different credential — processing another tenant's data, or performing a step the parent must not be able to perform directly. Here separation *is* the feature, and isolation is the point rather than a side effect.

**Different trust domain.** The work must handle untrusted content, so it runs in a constrained agent with no dangerous tools and returns a structured result. This is a deliberate blast-radius reduction: the agent that reads the hostile document cannot act on it. Compare [AUTH-46](auth-catalog.md#auth-46--context-trust-boundary-authority).

**Genuine independent judgement.** The value depends on the second opinion being uncontaminated — adversarial review, or a check whose worth comes from not having seen the first agent's reasoning. Sharing the context would defeat the purpose. Note this justification fails if the reviewer is given the author's reasoning, which is the usual implementation.

### Not justifications

- Mirroring an org chart or a job-title decomposition.
- "Specialist" agents whose specialization is a paragraph of prompt.
- A sequential pipeline where each stage transforms the previous stage's output — that is a function chain.
- Parallelism for speed, when the same model could be called concurrently without separate agent identities.
- Making the architecture diagram legible to stakeholders.

---

## Agent-to-agent authority (normative)

Delegation is an authority transfer, and it is where multi-agent systems leak. Five rules.

### 1 — Authority only narrows

A child run receives a subset of its parent's authority, never a superset and never a sibling set the parent lacks. If the parent may not delete, no descendant may delete.

Delegation is the most natural privilege-escalation path in an agentic system precisely because it does not look like one: nobody granted the child anything, it simply got its own credential from configuration.

### 2 — Budgets derive, they do not reset

A child draws from the parent's remaining budget. A child that starts with a fresh allowance makes the parent's budget meaningless, because the parent can always spawn again — recursion becomes an unbounded resource multiplier while every individual budget looks correctly enforced.

Declare **depth** and **fan-out** budgets in addition to the ordinary ones, per [AUTH-45](auth-catalog.md#auth-45--agent-loop--budget-authority).

### 3 — A gate cannot be laundered by delegation

If an action requires human approval, it requires approval regardless of which agent in the chain performs it. Delegating a gated action to a subagent that "just executes the plan" does not satisfy the gate — the gate attaches to the **effect**, not to the actor.

This is worth stating explicitly because it is an easy accident rather than a deliberate evasion: the parent gets approval for a plan, and a child later performs a specific effect that would have needed its own approval.

### 4 — Agent output is untrusted input

Output from another agent is **untrusted content** ([AUTH-46](auth-catalog.md#auth-46--context-trust-boundary-authority)) — the same class as a retrieved web page. It is data with provenance, never instruction, and it is validated against a schema before use ([AUTH-36](auth-catalog.md#auth-36--output-constraint-authority)).

Two failures follow from skipping this. Injection propagates: hostile text reaching a low-privilege agent gets relayed to a higher-privilege one as summary. And errors compound: unvalidated confident output becomes a premise, so the chain's final answer carries the first agent's mistake with none of its uncertainty.

### 5 — The graph is recorded

Every child records its `parent_run_id`, and every spawn is an event on the parent's record ([`agent-run-record.md`](agent-run-record.md)). A chain that cannot be reconstructed cannot be debugged, evaluated, or attested — and orphaned children make the fan-out budget unverifiable after the fact.

---

## UDALI placement

| Concern | Owner |
|---------|-------|
| Spawn decision, depth and fan-out budgets | **Logician** band — **L15 (AI services)** |
| Authority derivation for the child | **Adapter** band — **L11 (auth)** |
| Inter-agent message schema | **Designer** band — **L7 (DTO)** — an agent boundary is a contract boundary ([AUTH-21](auth-catalog.md#auth-21--dto--contract-boundary-authority)) |
| Trust tagging of agent output | **L17 (middleware)** |
| Graph recording | **L14 (events)** |
| Chain tests | **Integrator** band — **L21 (QA / verification)** |

An agent boundary is a [SEAM-15](seam-catalog.md#seam-15--udali-role-handoff-ownership-transfer) ownership transfer and, where authority changes, a `HORI_REALM_BRIDGE` crossing. Treat it with the same seam discipline as any other handoff — meaning survives it or it does not.

---

## Forbidden patterns

- **Org-chart decomposition.** Agents named after job titles.
- **The deterministic agent.** An agent whose work a function would do better.
- **Fresh budgets on spawn.** Child allowances independent of the parent's remainder.
- **Unbounded depth or fan-out.** Recursion with no declared limit.
- **Authority by configuration.** A child holding a credential the parent lacks.
- **Gate laundering.** A gated effect performed by a delegate under a plan-level approval.
- **Implicit trust in the chain.** Predecessor output consumed as fact, unvalidated and untagged.
- **Free-text interfaces.** Agents exchanging prose instead of a validated schema.
- **Orphan runs.** Spawned runs with no `parent_run_id`.
- **Contaminated review.** A "critic" given the author's reasoning, then cited as independent judgement.

---

## Evidence (what assurance expects)

| Claim | Evidence anchor |
|-------|-----------------|
| Each agent is justified | Written justification per agent naming which of the three applies, plus why a function or tool does not |
| Authority narrows | Test asserting a child cannot perform an effect the parent may not |
| Budgets derive | Test showing child consumption decrements the parent's remainder |
| Depth and fan-out are bounded | Test driving recursion and fan-out past the limit, asserting the declared stop |
| Gates survive delegation | Test attempting a gated effect via a subagent, asserting refusal without approval |
| Agent output is validated | Schema plus a test rejecting malformed inter-agent output |
| Injection does not propagate | Test injecting instruction-shaped text into a low-privilege agent, asserting no effect on the parent |
| The graph is reconstructable | Test rebuilding a multi-level chain from records |

---

## Delivery lanes

| Lane | Use |
|------|-----|
| **Navigator** | Run the justification test during scope. "We need a multi-agent system" is a design conclusion, not a requirement — the requirement is the work, and most of it is deterministic. |
| **Investigator** | Confident wrong answers, cost far above expectation, and errors with no clear origin are chain symptoms. Rebuild the graph from run records and find the first agent whose output was already wrong, rather than debugging the last one. |
| **Sentinel** | Attest authority narrowing, budget derivation, gate survival, and output validation at every boundary. Declare `HORI_REALM_BRIDGE` where authority changes and `SEAM-15` for the ownership transfer. |

---

## Public checklist

- [ ] Every agent has a written justification naming one of the three legitimate reasons.
- [ ] A deterministic alternative was considered and recorded as worse.
- [ ] Child authority is a subset of parent authority, proven by test.
- [ ] Child budgets derive from the parent's remainder.
- [ ] Depth and fan-out budgets are declared and enforced.
- [ ] Gated effects require approval regardless of which agent performs them.
- [ ] Inter-agent messages use a validated schema, not prose.
- [ ] Agent output is tagged untrusted and validated before use.
- [ ] Every child records `parent_run_id`; the graph is reconstructable.
- [ ] Any agent presented as independent review has not seen the author's reasoning.

---

## Related

- [`agent-runtime-harness.md`](agent-runtime-harness.md) — the wrapper each agent needs
- [`agent-tool-design.md`](agent-tool-design.md) — the cheaper option at step 2
- [`agent-context-and-state.md`](agent-context-and-state.md) — handoff as a trust boundary
- [`agent-run-record.md`](agent-run-record.md) — `parent_run_id` and the agent graph
- [`agentic-failure-modes.md`](agentic-failure-modes.md) — AGENT-09
- [`reuse-before-build.md`](reuse-before-build.md) — the same restraint applied to dependencies

These documents do **not** constitute legal or regulatory certification. Formal gates are defined by **your** policies, tests, and CI.
