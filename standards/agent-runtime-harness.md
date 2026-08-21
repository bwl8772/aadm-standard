# Agent runtime harness — the control wrapper

> Normative short for the **deterministic code around the model**. Names the control wrapper as one concept, states the agent loop, separates what reasons from what governs, and records the inversion that autonomy *increases* the control requirement. Stack-agnostic.

Assembles [AUTH-28](auth-catalog.md#auth-28--async-job--long-running-task-authority), [AUTH-38](auth-catalog.md#auth-38--rate-limiting-authority), [AUTH-39](auth-catalog.md#auth-39--cost-guardrail-authority), [AUTH-45](auth-catalog.md#auth-45--agent-loop--budget-authority), [AUTH-46](auth-catalog.md#auth-46--context-trust-boundary-authority), and [AUTH-47](auth-catalog.md#auth-47--agent-memory-integrity-authority) into a single design surface. Those controls each name one obligation; this document says what holds them.

---

## Problem

Most agent implementations are described as three parts: a model, a set of tools, and a prompt. Nothing in that description is accountable for deciding whether a proposed step is *allowed to happen*. So the decision defaults to the model — which is the one component that cannot be held to a guarantee.

The result is an agent whose bounds exist only as intentions in a prompt. It stops looping because the prompt asked it to, not because anything counts iterations. It avoids dangerous tools because the prompt discouraged them, not because anything checks authorization. When the model behaves, the system looks correct; the controls were never there.

---

## One-line principle

An autonomous agent is **a model wrapped in deterministic control**. The model reasons; the wrapper governs. Autonomy is a property of the **wrapper**, not of the model — and a capability with no wrapper is not autonomous, only unbounded.

---

## The inversion

Teams reach for autonomy expecting to *remove* structure: fewer checkpoints, less plumbing, the model figures it out. The relationship runs the other way.

| Autonomy | What the human no longer does | What the wrapper must therefore do |
|----------|-------------------------------|-------------------------------------|
| Low — agent drafts, human executes | Nothing; the human still performs every effect | Very little. The human review *is* the control. |
| Medium — agent acts, human approves each effect | Stops reading every intermediate step | Enforce the gate; make the proposed effect legible enough to approve |
| High — agent acts unattended within a scope | Stops seeing the run at all | Enforce every budget, authorize every call, classify every input, record every step, and terminate itself |

Each rung of autonomy transfers a judgement the human used to make. That judgement does not evaporate — it has to be re-implemented in deterministic code. **More autonomy means more control surface, not less.** An architecture that gets simpler as it becomes more autonomous has not moved the judgement anywhere; it has dropped it.

The corollary for review: "we are making this more autonomous" is a request for *additional* engineering, and a plan that does not grow the wrapper is not a plan.

---

## Reasons versus governs

Every part of an agent belongs on exactly one side of this line. Ambiguity here is the root of most agentic defects.

| Reasons (model, non-deterministic) | Governs (wrapper, deterministic) |
|-------------------------------------|-----------------------------------|
| Chooses which tool would help | Decides whether that tool may be called in this context |
| Proposes arguments for a call | Validates arguments against the tool schema |
| Judges whether the goal is met | Counts iterations and enforces the budget |
| Summarizes what a document said | Classifies where that document came from and what trust it carries |
| Suggests the run is finished | Terminates the run and writes the terminal state |
| Drafts the change | Routes it to the required approval gate |

**The rule:** anything on the right that is implemented by asking the model nicely is not implemented. A budget the model is told to respect is not a budget. An authorization check the model is trusted to perform is not a check.

---

## The agent loop (normative shape)

A conformant harness runs this loop. Steps 2, 4, 6, and 7 are wrapper responsibilities and MUST NOT be delegated to the model.

1. **Accept the goal.** Record the request, the requesting actor, and the authority under which the run executes.
2. **Assemble context.** Gather only what this decision needs, each item tagged with its trust level ([AUTH-46](auth-catalog.md#auth-46--context-trust-boundary-authority)). See [`agent-context-and-state.md`](agent-context-and-state.md).
3. **Model proposes one step.** A tool call, a message, or a claim that the goal is met.
4. **Admit or deny the step.** Check budgets ([AUTH-45](auth-catalog.md#auth-45--agent-loop--budget-authority), [AUTH-38](auth-catalog.md#auth-38--rate-limiting-authority), [AUTH-39](auth-catalog.md#auth-39--cost-guardrail-authority), [AUTH-40](auth-catalog.md#auth-40--latency-budget-authority)), authorize the tool invocation ([AUTH-44](auth-catalog.md#auth-44--tool-invocation-authority)), and apply any required human gate ([AUTH-14](auth-catalog.md#auth-14--approval-authority)). A denied step is a recorded outcome, not an error to retry blindly.
5. **Execute the admitted step.** Return the result as *data*, never as instruction.
6. **Record the step.** Append to the run record — turn, proposal, verdict, result, cost. See [`agent-run-record.md`](agent-run-record.md).
7. **Evaluate termination.** Goal met, budget exhausted, gate refused, or unrecoverable error. Every run ends in exactly one declared terminal state.

A loop with no step 7 does not terminate; it merely usually stops.

---

## The wrapper's duties

| Duty | Obligation | Notes |
|------|------------|-------|
| **Bound the loop** | [AUTH-45](auth-catalog.md#auth-45--agent-loop--budget-authority) | Maximum iterations, tool calls, spawned subtasks, wall-clock. Correctness concern, not only cost. |
| **Cap spend** | [AUTH-39](auth-catalog.md#auth-39--cost-guardrail-authority) | Declared ceiling with declared behaviour at the ceiling. |
| **Limit rate** | [AUTH-38](auth-catalog.md#auth-38--rate-limiting-authority) | Per actor, tenant, or surface — agents amplify call volume. |
| **Authorize each call** | [AUTH-44](auth-catalog.md#auth-44--tool-invocation-authority) | Registration is not authorization. See [`agent-tool-design.md`](agent-tool-design.md). |
| **Classify incoming content** | [AUTH-46](auth-catalog.md#auth-46--context-trust-boundary-authority) | Tool results, retrieved documents, upstream agent output — data, not instructions. |
| **Protect memory** | [AUTH-47](auth-catalog.md#auth-47--agent-memory-integrity-authority) | Provenance on every persisted fact; a path to detect and recover from poisoning. |
| **Report progress** | [AUTH-28](auth-catalog.md#auth-28--async-job--long-running-task-authority) | A long agent run is a long-running task: observable, bounded, cancellable. |
| **Hold the gate** | [AUTH-14](auth-catalog.md#auth-14--approval-authority) | Named approver, recorded decision. See [`human-in-the-loop.md`](human-in-the-loop.md). |
| **Record the run** | [AUTH-29](auth-catalog.md#auth-29--audit-log-authority), [AUTH-30](auth-catalog.md#auth-30--traceability-authority), [AUTH-31](auth-catalog.md#auth-31--decision-logging-authority) | Audit trail, correlation id, explainable decision. |
| **Constrain output** | [AUTH-36](auth-catalog.md#auth-36--output-constraint-authority) | Model output that drives action is schema-validated before it acts. |

---

## UDALI placement

| Concern | Owner |
|---------|-------|
| Agent loop, budget enforcement, context assembly | **Logician** band — primary home is **L15 (AI services)** |
| Tool authorization, trust classification on ingress | **Adapter** band — **L11 (auth)** and **L17 (middleware)** |
| Run record persistence and emission | **L14 (events)**, with audit storage per your retention policy |
| Harness verification | **Integrator** band — **L21 (QA / verification)** |
| Presentation of run state to users | **Unifier** band — renders state; never derives it |

The wrapper is **not** presentation-layer code, and it is not a utility folder beside the prompt. It is a named module with an interface, per [`module-interface-seam-vocabulary.md`](module-interface-seam-vocabulary.md).

---

## Forbidden patterns

- **Prompt-as-control.** Budgets, authorization rules, or stop conditions expressed only as instructions to the model.
- **Registration as authorization.** Treating "the tool is in the tool list" as the decision that it may be called now, by this agent, on this resource.
- **Instruction-shaped tool output.** Returning tool or retrieval results into context without a trust tag, so retrieved text can steer the loop.
- **Unbounded recursion.** Sub-agent spawning with no depth or fan-out budget. See [`multi-agent-restraint.md`](multi-agent-restraint.md).
- **Terminal state by exhaustion.** Runs that end because the process died, the request timed out, or the loop ran out of tokens — with no declared terminal state recorded.
- **The invisible run.** An unattended agent whose only artifact is its side effects, with no run record to reconstruct what it did.
- **Harness bypass.** A second code path that reaches the same tools without passing through the wrapper — the agentic form of [HANDOFF-02](handoff-patterns.md) write-path bypass.

---

## Evidence (what assurance expects)

Bind proof in **your application repository** — documentation of a wrapper is not a wrapper.

| Claim | Evidence anchor |
|-------|-----------------|
| Loop is bounded | Test that drives the agent past each budget and asserts the declared stop, not a timeout |
| Every tool call is authorized | Test asserting an unauthorized call is denied *with the tool still registered* |
| Untrusted content cannot instruct | Test injecting instruction-shaped text through a tool result or retrieved document, asserting no behaviour change |
| Memory has provenance | Schema showing source, writer, and timestamp on persisted facts; recovery procedure |
| Runs are recorded | A retrievable run record for a completed run, including denied steps |
| Terminal states are declared | Enumeration of terminal states plus a test per state |
| No harness bypass | Inventory of call sites reaching the tool layer; test proving the wrapper is the only path |

Per [AUTH-24](auth-catalog.md#auth-24--test--verification-authority), each obligation above has a paired test. Pass/fail lives in your CI.

---

## Delivery lanes

| Lane | Use |
|------|-----|
| **Navigator** | Design the wrapper before the capability. The budget, gate, and terminal-state enumeration belong in the scope statement, not in a later hardening pass. |
| **Investigator** | Symptoms such as runaway cost, a loop that "sometimes doesn't stop," or an agent that took an action nobody authorized are usually missing wrapper duties — not prompt-tuning problems. Check the run record before theorizing. |
| **Sentinel** | Attest the wrapper as a horizontal concern. Declare `HORI_AUTH` for tool authorization and `HORI_REALM_BRIDGE` where an agent run crosses a trust realm. |

---

## Public checklist

- [ ] The wrapper is a named module with an interface, not scattered helpers.
- [ ] Every item in *Governs* is implemented in deterministic code, not prompt text.
- [ ] Iteration, tool-call, subtask, wall-clock, and cost budgets are all declared and enforced.
- [ ] Every tool call is independently authorized in context.
- [ ] Every context item carries a trust classification.
- [ ] Terminal states are enumerated, and every run ends in exactly one.
- [ ] Progress is observable and the run is cancellable while in flight.
- [ ] The run record is written for successful, denied, and failed steps alike.
- [ ] No second code path reaches tools without the wrapper.
- [ ] The autonomy rung claimed for this capability matches the control actually built ([`human-in-the-loop.md`](human-in-the-loop.md)).

---

## Related

- [`agent-tool-design.md`](agent-tool-design.md) — the tool contract the wrapper authorizes against
- [`agent-context-and-state.md`](agent-context-and-state.md) — what step 2 assembles and what state persists
- [`agent-run-record.md`](agent-run-record.md) — what step 6 writes
- [`agentic-failure-modes.md`](agentic-failure-modes.md) — the named anti-patterns this document prevents
- [`human-in-the-loop.md`](human-in-the-loop.md) — the autonomy ladder and approval triggers
- [`agent-evaluation.md`](agent-evaluation.md) — proving the wrapper still holds after deployment

These documents do **not** constitute legal or regulatory certification. Formal gates are defined by **your** policies, tests, and CI.
