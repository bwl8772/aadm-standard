# Agent evaluation

> Normative short for **measuring an agent after it ships**. Names the post-deployment metric catalog, and states why adaptive routing *raises* the validation burden rather than lowering it. Stack-agnostic.

Computed over the population of records defined in [`agent-run-record.md`](agent-run-record.md). Supplies the evidence that earns an autonomy rung in [`human-in-the-loop.md`](human-in-the-loop.md).

---

## Problem

Agents are validated the way deterministic software is validated: a suite of cases, expected outputs, a green result, ship. Afterwards the only things measured are cost and latency, because those are what the platform emits by default.

This leaves the interesting failure invisible. An agent's behaviour is a function of its inputs, its model, its prompt, its tools, its retrieved content, and the state of the data — and every one of those changes after launch without anyone editing the agent. The suite that passed was a sample of a distribution, and the distribution moves.

The practical symptom is a capability that everyone believes is working, whose failure rate nobody can state. When the question finally arrives — usually as a request to remove a human gate — there is no answer, because the evidence that would support it was never collected.

---

## One-line principle

An agent's correctness is a property of its **live population**, not of its test suite. Post-deployment measurement is part of building the capability, not an operational afterthought — and without it, autonomy cannot be justified past the rung the pre-deployment tests support.

---

## Pre-deployment and post-deployment do different jobs

Both are required. Neither substitutes.

| | Pre-deployment tests | Post-deployment evaluation |
|---|---|---|
| **Question** | Does the control hold on the cases we thought of? | What is actually happening on the cases we did not? |
| **Input** | Fixtures and adversarial cases | The live run population |
| **Proves** | The wrapper enforces its obligations ([AUTH-24](auth-catalog.md#auth-24--test--verification-authority)) | The rate, trend, and distribution of real outcomes |
| **Fails when** | A control regresses | Reality drifts away from the fixtures |
| **Gates** | Merge and release | Autonomy rung, and continued operation |

A green suite says the budget is enforced. Only the population says the budget is hit on nine percent of runs and rising.

---

## The metric catalog

Six families. Each is computed from run records, so a capability with no run record cannot be evaluated at all — that is why AGENT-07 caps autonomy.

### 1 — Outcome

| Metric | What a bad trend means |
|--------|------------------------|
| **Completion rate** — share of runs reaching `completed` | Falling: the capability is losing ground against real inputs |
| **Incompletion rate** — share reaching `incomplete` | Rising: the agent is quietly giving up. The most commonly hidden metric, because `incomplete` is often mis-filed as success |
| **Rework rate** — tasks re-attempted by a human or a second run | High: nominal success is not real success. Completion rate alone is misleading here |

Completion rate in isolation is the metric most likely to reassure falsely. Read it beside incompletion and rework or not at all.

### 2 — Intervention

| Metric | What a bad trend means |
|--------|------------------------|
| **Gate rejection rate** — approvals declined | Rising: the agent's judgement is degrading. Near zero: the gate is unnecessary, or nobody is reading it. Both are findings |
| **Correction rate** — approved-but-edited before applying | High: output is directionally right and unusable, which aggregate success rates will not show |
| **Escalation rate** — runs handed to a human | Rising: scope has drifted beyond the capability |
| **Reversal rate** — rung-6 actions undone in the window | Any sustained level: the reversal window is doing real work, so the rung is not yet earned |

This family is the direct input to a rung decision. A promotion argument that cites completion rate but not intervention rate is not an argument.

### 3 — Control health

| Metric | What a bad trend means |
|--------|------------------------|
| **Budget exhaustion rate**, by budget | Rising: either the work outgrew its bounds or the loop is degenerating. Distinguish the two before raising a limit |
| **Loop-length distribution** | A long tail means near-runaway runs that budgets are catching. The budget is working; the loop is not |
| **Authorization denial rate** | Rising: the agent is repeatedly attempting what it may not do — a prompt or tool-surface mismatch, or an injection attempt |
| **Injection detection rate** | Any: untrusted content is trying to instruct. Zero forever probably means nothing is looking |

Control health measures the *wrapper*, and it is the family teams skip. Denials and budget stops feel like non-events because nothing bad happened — which is exactly what makes their rate informative.

### 4 — Cost and efficiency

| Metric | Note |
|--------|------|
| **Cost per completed task** | The honest denominator. Cost per call falls while cost per outcome rises whenever runs get longer and less successful |
| **Cost distribution, not mean** | Agent cost is long-tailed; the mean hides the runs that matter |
| **Latency to outcome** | Per [AUTH-40](auth-catalog.md#auth-40--latency-budget-authority), against the declared budget |

### 5 — Drift

| Metric | Note |
|--------|------|
| **Behaviour change across `model_ref` versions** | The reason `model_ref` is on the run record. A provider-side update is a change you did not make and must still detect ([AUTH-37](auth-catalog.md#auth-37--model-selection-authority)) |
| **Behaviour change across `prompt_version` and `harness_version`** | Attributes a shift to a change you did make |
| **Input distribution shift** | The fixtures aged. Pre-deployment coverage silently decayed |

Drift is only measurable if the run record captures the governing configuration. This is the concrete reason those fields are mandatory.

### 6 — Safety

| Metric | Note |
|--------|------|
| **Hard-stop trigger rate** | Attempts at anything on [`ai-coder-hard-stops.md`](ai-coder-hard-stops.md) |
| **Cross-realm or cross-tenant attempt rate** | [AUTH-16](auth-catalog.md#auth-16--tenancy-authority); cite `HORI_REALM_BRIDGE` |
| **Memory poisoning detections** | [AUTH-47](auth-catalog.md#auth-47--agent-memory-integrity-authority), plus whether invalidate-by-source was exercised |
| **Unrecorded-run rate** | Runs with side effects and no complete record. Should be zero; anything else is AGENT-07 in production |

---

## Adaptive routing raises the validation burden

Adaptive routing — letting the system choose its model, tool path, or strategy at runtime — is normally introduced as an optimization: use the cheap model when the task is easy, escalate when it is hard. Framed that way it reads like a cost win with no correctness cost.

It is a correctness change, and it moves the burden the wrong way.

**Before routing** you shipped one configuration. Pre-deployment validation covered that configuration, and a green suite described the thing in production.

**After routing** you shipped a family of configurations, and which one serves a given request is decided at runtime by the system. Validation that exercised the default path covered a *sample* of what is deployed. The routing decision itself is now a component — one that can be wrong in ways neither branch is wrong.

Three consequences follow, and all of them are more work:

1. **Every route needs its own validation.** Coverage on the aggregate is not coverage. A route taken by four percent of traffic is still in production, and it is the route with the least evidence and the least attention.
2. **Every metric needs a per-route cut.** Aggregates hide route-level failure with particular efficiency, because the cheap route usually carries the most volume — so a badly failing expensive route barely moves the overall number. A healthy aggregate is compatible with a route that fails most of the time.
3. **The router is a governed decision.** Which model served a request is an [AUTH-37](auth-catalog.md#auth-37--model-selection-authority) obligation: deliberate, recorded, and changeable through a controlled path. A router that silently reallocates traffic is a silent model change at runtime. Record the chosen route on the run.

**The general rule.** Any mechanism that lets the system decide its own behaviour at runtime multiplies the configuration space that must be validated. Routing, dynamic tool selection, self-modifying plans, and model fallback are all instances. Each is legitimate; none is free; and the plan that introduces one without adding per-route measurement has not accounted for what it did.

The inversion is the same one in [`agent-runtime-harness.md`](agent-runtime-harness.md): moving a decision from a human or a fixed configuration into the system does not remove the decision. It relocates it somewhere that now needs evidence.

---

## Evaluation earns autonomy

The relationship is directional and worth stating plainly, because it is usually assumed in the other order.

Teams tend to raise autonomy first and measure afterwards, on the reasoning that unattended operation is what generates the volume worth measuring. That inverts the dependency: the rung is what needs justifying, and the measurement is the justification. A capability with no post-deployment measurement is capped at the rung its pre-deployment tests can support — typically rung 4, where a human sees every effect.

The promotion argument requires, at minimum: completion and incompletion rates, intervention rate with a trend, the failure profile with a statement of whether the removed gate is the one catching those failures, and control-health rates showing the wrapper holds. See [`human-in-the-loop.md`](human-in-the-loop.md) — *Autonomy is earned by evidence*.

---

## UDALI placement

| Concern | Owner |
|---------|-------|
| Metric definition and computation | **Logician** band — **L13 (service)** over the run record store |
| Run record emission | **L14 (events)** — see [`agent-run-record.md`](agent-run-record.md) |
| Route recording | **L15 (AI services)** |
| Evaluation fixtures and adversarial suites | **Integrator** band — **L21 (QA / verification)** |
| Rung decisions | Governance, per [`human-in-the-loop.md`](human-in-the-loop.md) |
| Dashboards and alerts | **Unifier** band — renders; never derives a verdict ([`guidance-feedback-seam.md`](guidance-feedback-seam.md)) |

---

## Forbidden patterns

- **Ship and forget.** A green pre-deployment suite treated as ongoing evidence.
- **Cost and latency only.** Measuring what the platform emits by default and calling it evaluation.
- **Completion rate alone.** Reported without incompletion and rework.
- **`incomplete` as success.** Abandonment folded into the completion numerator.
- **Aggregate-only metrics under routing.** No per-route cut.
- **Unrecorded routes.** The chosen model or path absent from the run record, making drift unattributable.
- **Means over long tails.** Averaging cost or latency for a long-tailed distribution.
- **Gate theatre.** A gate with a near-zero rejection rate left in place and counted as a control.
- **Promotion without evidence.** A rung raised on demo quality or stakeholder confidence.
- **Evaluation on the agent's own judgement alone.** Self-assessed success with no independent signal — the agent grading its own homework.

---

## Evidence (what assurance expects)

| Claim | Evidence anchor |
|-------|-----------------|
| Runs are measurable | Complete run records for the evaluation window |
| Outcome metrics are computed | The computation, with `incomplete` separated from `completed` |
| Intervention is tracked | Gate rejection and correction rates, with trend |
| Control health is tracked | Budget-exhaustion, denial, and injection rates |
| Cost is per outcome | Definition showing completed tasks as denominator |
| Drift is attributable | Metrics cut by `model_ref`, `prompt_version`, `harness_version` |
| Routing is measured per route | Per-route metric cuts, and the route recorded on each run |
| The rung is justified | The promotion record citing the metrics above |
| Fixtures are current | Review date and the input-distribution comparison that triggered it |

---

## Delivery lanes

| Lane | Use |
|------|-----|
| **Navigator** | Define the metrics with the capability, in the same scope statement as the budgets and gates. Metrics added later cannot describe the period before they existed, which is the period the first rung decision will ask about. |
| **Investigator** | Population metrics separate a one-off generation from a pattern. A rising incompletion or denial rate turns "the agent is flaky" into a bounded defect with a start date and a suspect version. |
| **Sentinel** | Attest that measurement exists, that the claimed rung is backed by it, and that routed configurations are measured per route. |

---

## Public checklist

- [ ] Every capability has post-deployment metrics defined before launch.
- [ ] `completed`, `incomplete`, and rework are reported separately.
- [ ] Intervention rate is tracked with a trend, and near-zero gate rejection is investigated.
- [ ] Budget-exhaustion, authorization-denial, and injection rates are tracked.
- [ ] Cost is expressed per completed task, with distributions not means.
- [ ] Metrics are cut by model, prompt, and harness version.
- [ ] Every adaptive route is recorded on the run and measured separately.
- [ ] Fixtures are reviewed against the live input distribution on a stated cadence.
- [ ] The autonomy rung in force is backed by named metrics.
- [ ] Success is not assessed solely by the agent itself.

---

## Related

- [`agent-run-record.md`](agent-run-record.md) — the population these metrics are computed over
- [`human-in-the-loop.md`](human-in-the-loop.md) — the ladder this evidence moves
- [`agent-runtime-harness.md`](agent-runtime-harness.md) — the controls measured by control health
- [`agentic-failure-modes.md`](agentic-failure-modes.md) — AGENT-07, AGENT-08
- [`agent-tool-design.md`](agent-tool-design.md) — the surface routing selects across

These documents do **not** constitute legal or regulatory certification. Formal gates are defined by **your** policies, tests, and CI.
