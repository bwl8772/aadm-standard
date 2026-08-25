# Human in the loop

A foundational AADM principle. Companion: [`aadm-core-principles.md`](aadm-core-principles.md) §2.

---

## Definition

**Human-in-the-loop (HITL)** means explicit **checkpoints** where accountable people **approve, reject, or attest** before irreversible or high-risk effects occur. Automation—including AI—may **draft, simulate, or recommend**; it does not inherit **organizational authority** for those checkpoints unless policy explicitly says otherwise.

---

## Why it matters

Models and scripts do not carry regulatory, contractual, or fiduciary accountability. When sensitive actions run unattended, organizations lose traceability and invite irreversible harm (data loss, privilege misuse, financial movement).

HITL preserves **traceable judgment** aligned to risk.

---

## An authority mechanism, not a workflow step

HITL is routinely drawn as a box in a flowchart — "human review" between two automated stages. That framing understates what it is and invites the failure modes that follow from understating it.

A HITL checkpoint is an exercise of **authority**: an accountable, named person approving, rejecting, or attesting *before* the effect, under [AUTH-14 (Approval Authority)](auth-catalog.md#auth-14--approval-authority), with a decision record that can be reconstructed later. Three consequences separate it from an ordinary workflow step:

1. **The gate is owned by a control, not by the flow.** A workflow step can be reordered, parallelized, or skipped when the diagram changes. An AUTH-14 gate cannot — the workflow routes *to* it ([AUTH-55](auth-catalog.md#auth-55--automation-workflow-gate-authority)), and a redesigned flow that no longer passes through it has removed an authority, which is itself an approval trigger (see the table below).
2. **It cannot be satisfied by assertion.** An automation that reports "approval obtained" has asserted, not approved. The gate is satisfied only by the gate's own recorded decision — the same rule that governs completion ([AUTH-50](auth-catalog.md#auth-50--completion-verification-authority)) and workflow advancement ([AUTH-55](auth-catalog.md#auth-55--automation-workflow-gate-authority)). An agent may prepare the approval package; it cannot emit the verdict.
3. **The approver is a role with authority over the effect, not an available human.** "A person clicked yes" is a workflow step. Authority means the named role is accountable for that class of effect, the decision basis is recorded, and a near-zero rejection rate is investigated as a finding — a gate nobody ever refuses is either unnecessary or not being read ([`agent-evaluation.md`](agent-evaluation.md), intervention metrics).

Where the [AI Automation Profile](ai-automation-profile.md) routes a requirement to a human, this is the mechanism it means.

---

## What good looks like

- Written **triggers** (“what requires human approval”).  
- Named **roles** responsible for approval—not vague “someone reviewed.”  
- **Evidence**: ticket links, audit entries, or signed artifacts tied to releases.  
- **Least privilege** for automation paths leading up to the checkpoint.

---

## Common failure mode

Treating “the AI agreed” or “the pipeline passed” as approval for production schema drops, org-wide permission grants, or contractual commitments—without a human accountable party matching policy.

---

## Public checklist

- [ ] Inventory high-risk actions for your product class (see starter list below).  
- [ ] Map each to required approver role(s) and system gates.  
- [ ] Ensure agents/tools cannot bypass gates via alternate APIs or credentials.  
- [ ] Record approvals with enough context to reconstruct decisions later.  
- [ ] Verify rollback or mitigation paths exist **before** approving irreversible steps.

---

## Starter categories (tune to your policy)

Representative triggers—**not exhaustive**:

- Bulk deletion or destructive migrations  
- Issuance of powerful credentials or broad role grants  
- Financial or billing-impacting changes  
- Customer-visible contractual or pricing commitments  
- Safety- or compliance-critical configuration in regulated contexts  

### Additional triggers for agentic systems

Teams that inventory the categories above still routinely miss these four, because each one changes what automation is permitted to do rather than performing a risky action itself. They are approval triggers in their own right.

| Trigger | Why it needs a named approver |
|---------|-------------------------------|
| **Raising an autonomy rung, or disabling a gate** | This is the act of transferring human judgement into code. An agent cannot approve its own promotion, and a rung that drifts upward through convenience defaults was never approved by anyone ([`agentic-failure-modes.md`](agentic-failure-modes.md) AGENT-08). |
| **Expanding the tool surface** — registering a new tool, or reclassifying one to a lower side-effect class | The agent's authority is the union of its tools. Adding an `external` or `irreversible` tool widens blast radius permanently, and it usually arrives as a small diff ([`agent-tool-design.md`](agent-tool-design.md)). |
| **Changing the model or a governed prompt** on a path that drives a governed action | Behaviour changes without any code change to the action itself. Silent model upgrades are non-conformant under [AUTH-37](auth-catalog.md#auth-37--model-selection-authority); prompt versions are controlled artifacts under [AUTH-35](auth-catalog.md#auth-35--prompt-governance-authority). |
| **A run crossing a tenancy or trust-realm boundary** | Cross-tenant and cross-realm reach requires an explicit, audited mechanism ([AUTH-16](auth-catalog.md#auth-16--tenancy-authority)). Cite `HORI_REALM_BRIDGE` when attesting the crossing. |

---

## Relationship to AI agents

Agents accelerate preparation; they **do not replace** named approvers for governed actions. Maintain a crisp line between **suggestion** (draft PR, proposed plan) and **execution** (merge to production, apply migration).

"Human in the loop" is not one setting, though — it is a position on a ladder, and most disagreements about an agent's safety are really disagreements about which rung it occupies.

---

## The autonomy ladder

Seven rungs from explanation to unattended action. State the rung a capability occupies; the phrase "human in the loop" on its own does not distinguish rung 2 from rung 6.

| Rung | The agent may | The human still | Minimum control required |
|------|---------------|-----------------|--------------------------|
| **1 — Inform** | Describe, explain, answer | Everything | None beyond ordinary data access |
| **2 — Suggest** | Recommend an action in prose | Decide and perform it | Nothing enforced; the human is the control |
| **3 — Draft** | Produce the artifact — patch, migration, message — without applying it | Review and apply | Artifact is inert by construction; no apply path exists |
| **4 — Execute per action** | Apply effects, one approval each | Approve every effect | Gate per effect; the proposed effect is legible enough to judge |
| **5 — Execute per plan** | Execute an approved plan's steps | Approve the plan once; monitor | Plan is bound and recorded; deviation from it halts the run |
| **6 — Act with reversal window** | Act unattended, notify after | Watch notifications; reverse | Every effect reversible; notification is reliable; reversal is tested |
| **7 — Act unattended** | Act within a declared scope | Review retrospectively, by sample | Full wrapper: budgets, per-call authorization, trust classification, run records, declared terminal states |

Two properties of the ladder matter more than the rung names.

**Reversibility is what makes rung 6 possible.** An agent permitted to act before a human sees it is an agent whose mistakes are cheap to undo. Where reversal does not exist, rung 6 is not available — the capability is at most rung 4, regardless of how routine it feels. This is why side-effect class is a required field on every tool ([`agent-tool-design.md`](agent-tool-design.md)).

**Rung 7 is not the absence of control; it is the maximum of it.** Each rung upward removes a human judgement from the path, and that judgement has to be re-implemented in deterministic code. The wrapper is largest exactly where the human is least present ([`agent-runtime-harness.md`](agent-runtime-harness.md) — *The inversion*).

Rungs are per **capability**, not per product. A system commonly runs rung 7 for read-only enrichment and rung 4 for anything that touches billing. One agent, two rungs, stated separately.

---

## Autonomy is earned by evidence

A rung is not assigned by ambition or by how well a demo went. It is earned by operating at the rung below and producing evidence that justifies the promotion.

Before raising a capability's rung, require:

1. **Run history at the current rung** — enough completed runs, with records, to characterize behaviour rather than anecdote it ([`agent-run-record.md`](agent-run-record.md)).
2. **An intervention rate** — how often the human gate rejected or corrected the agent, and a trend. A gate that never fires is either unnecessary or not being read; both are findings.
3. **A characterized failure profile** — what the failures actually were, and whether the new rung would have caught them. Promotion is unjustified if the removed gate is the one that caught the known failures.
4. **The controls the new rung requires, already built and tested** — not planned. Rung 7 without run records is not rung 7.
5. **A named approver for the promotion itself**, per the trigger table above.

Demotion follows the same discipline in reverse and should be routine, not an incident. If the intervention rate rises or the failure profile shifts, the rung comes down while the cause is understood.

The metric catalog that supplies this evidence is in [`agent-evaluation.md`](agent-evaluation.md). The relationship is directional: **evaluation is what makes autonomy defensible**, so a capability with no post-deployment measurement is capped at the rung its pre-deployment tests can justify.

---

## Not the same as the guidance–feedback severity ladder

Two ladders in this standard both end in a rung called *gate*. They are unrelated, and conflating them produces confused designs.

| | Autonomy ladder (this document) | Severity ladder ([`guidance-feedback-seam.md`](guidance-feedback-seam.md)) |
|---|---|---|
| **Question** | How much authority does automation hold? | How forcefully does the product tell a user something? |
| **Rungs** | Inform → … → Act unattended (7) | nudge → recommendation → guidance → gate (4) |
| **Subject** | An agent or automated capability | A human user of the product |
| **"Gate" means** | A human approval checkpoint the agent must pass | A UI state that blocks an invalid user action |
| **Owner** | Governance and the runtime harness | Logician band verdict producer; Unifier band renders |

An agent at rung 4 needs an approval gate. A user at severity *gate* needs blocking copy. Neither implies the other.

---

## Further reading

- [`agent-runtime-harness.md`](agent-runtime-harness.md) — the control the upper rungs require  
- [`agent-tool-design.md`](agent-tool-design.md) — side-effect class and reversibility  
- [`agent-evaluation.md`](agent-evaluation.md) — the evidence that earns a rung  
- [`agentic-failure-modes.md`](agentic-failure-modes.md) — AGENT-08 autonomy inflation  
- [`docs/auth-aware-delivery.md`](../docs/auth-aware-delivery.md)  
- [`templates/bis-lite.md`](../templates/bis-lite.md) §12 Human checkpoint  
