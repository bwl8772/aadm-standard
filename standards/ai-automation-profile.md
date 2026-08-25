# AI Automation Profile v1 — AR-01..AR-30

**Status:** CANDIDATE profile. Normative authority stays with the AUTH catalog and the doctrine it cites; this profile adds no obligations of its own. AUTH-44..47 and AUTH-48..55 are CANDIDATE controls pending Architect / AI Auditor ratification.

**What this is.** A reusable adoption profile for business-critical AI automation: thirty implementation requirements (AR-01..AR-30), each mapped to the AADM control that owns it, the UDALI placement, the delivery-template field where it is declared, the evidence assurance expects, and the kind of test that proves it. The architecture is:

```
AADM Standard → AUTH / SEAM / UDALI normative controls
             → AI Automation Profile (this document)
             → AR-01..AR-30 implementation requirements
             → BIS → Acceptance criteria → tests / evals / evidence
```

The profile **references** the catalog; it does not extend it. Where a requirement had no owning control, a CANDIDATE control was added to the catalog first (AUTH-48..55) and the profile row cites it — the base catalog is never contaminated with application-specific requirements, and the numbered catalogs stay stable and append-only.

**HITL is an authority mechanism, not a workflow step.** Everywhere this profile routes to a human, the obligation is [AUTH-14](auth-catalog.md): an accountable, named person approving, rejecting, or attesting *before* the irreversible or high-risk effect, with a reconstructable decision record. A gate the automation can satisfy by asserting it was satisfied is not a gate (AR-19, AR-22); a rung of autonomy is earned by evaluation evidence, never assumed ([`agent-evaluation.md`](agent-evaluation.md)).

---

## How to read a row

- **Class** — *Covered*: the pre-existing catalog owns it. *Covered (new)*: owned by a CANDIDATE control added for this profile (AUTH-48..55). *Shared*: split between a pre-existing owner and a new control. Nothing in this profile is a Gap: every gap found during mapping became a control.
- **Primary control** — the citation for a finding. Cite the control, never the AR id alone; `AR-NN` locates the requirement in this profile, `AUTH-NN` carries the obligation.
- **Template field** — where the requirement is declared during delivery: the BIS ([`build-intent-specification.md`](../docs/build-intent-specification.md)) and the Stage-3 control-selection sweep (domains 19 and 20).
- **Test/eval type** — *deterministic test* (assertion-based, CI), *adversarial test* (drive the boundary and assert the stop), *eval* (criteria-based judgement over sampled or live outputs), *replay* (regression corpus re-execution), *registry/CI check* (build-time diff or policy assertion).

---

## The map

| AR | Requirement | Class | Primary control(s) | Supporting | UDALI | Template field | Required evidence | Test/eval type |
|----|-------------|-------|--------------------|------------|-------|----------------|-------------------|----------------|
| AR-01 | Model independence — rules, standards, permissions, workflow controls, acceptance criteria live outside the model | Covered | [AUTH-55](auth-catalog.md), [AUTH-37](auth-catalog.md) | [`agent-runtime-harness`](agent-runtime-harness.md) *reasons vs governs* | L13, L15 | BIS Scope boundaries; Stage-3 D20 | Rules and criteria in versioned code/config, not prompt text; model swap changes no rule | Deterministic test (swap `model_ref`, assert controls unchanged) |
| AR-02 | Independent output evaluation | Covered (new) | [AUTH-48](auth-catalog.md) | [AUTH-24](auth-catalog.md) | L21, L15 | Stage-3 D20; BIS Acceptance criteria | Evaluator distinct from producer; verdict recorded per artifact | Eval + deterministic test (producer ≠ evaluator identity) |
| AR-03 | Deterministic evaluation of objective requirements | Covered | [AUTH-48](auth-catalog.md) pass criteria, [AUTH-24](auth-catalog.md), [AUTH-21](auth-catalog.md) | — | L21, L7 | BIS Acceptance criteria | Each rule-expressible criterion has a deterministic check | Deterministic test |
| AR-04 | AI evaluator for semantic judgement, against explicit criteria | Covered (new) | [AUTH-48](auth-catalog.md) | [AUTH-35](auth-catalog.md), [AUTH-52](auth-catalog.md) | L15, L21 | Stage-3 D20 | Evaluator prompt + criteria versioned and recorded on each verdict | Eval, qualified per AR-26 |
| AR-05 | Machine-readable evaluation results | Covered (new) | [AUTH-48](auth-catalog.md) | [AUTH-21](auth-catalog.md) | L7, L14 | Stage-3 D20 | Verdict schema: outcome, per-criterion result, reason, evidence | Deterministic test (schema conformance) |
| AR-06 | Bounded generate–evaluate–correct loop with escalation | Covered (new) | [AUTH-48](auth-catalog.md), [AUTH-45](auth-catalog.md) | [AUTH-14](auth-catalog.md) | L15, L13 | BIS Scope; Stage-3 D19+D20 | Correction budget declared; exhaustion escalates to a named human | Adversarial test (exhaust budget, assert escalation) |
| AR-07 | Structured output contracts on every handoff | Covered | [AUTH-21](auth-catalog.md) | [AUTH-09](auth-catalog.md), [AUTH-10](auth-catalog.md), [SEAM-03](seam-catalog.md) | L7 | BIS Contract spine | Declared contract per handoff, versioned | Deterministic test |
| AR-08 | Deterministic schema/contract validation before acceptance | Covered | [AUTH-21](auth-catalog.md), [AUTH-36](auth-catalog.md) | [SEAM-02](seam-catalog.md) | L7, L17 | BIS Contract spine | Validation at the boundary, before any effect | Deterministic test (malformed payload rejected) |
| AR-09 | Required evidence on claims, recommendations, diagnoses, decisions | Covered (new) | [AUTH-49](auth-catalog.md) | [AUTH-46](auth-catalog.md) | L15, L7 | BIS Outcome; Stage-3 D20 | Resolvable citations per claim; unsupported claims marked | Eval (grounding) + deterministic test (references resolve) |
| AR-10 | Invalid handoffs fail explicitly, never inferred around | Covered | [AUTH-15](auth-catalog.md), [AUTH-21](auth-catalog.md) | [HANDOFF-08](handoff-patterns.md) | L7, L13 | BIS Contract spine | Missing required field → explicit failure, not downstream inference | Deterministic test |
| AR-11 | Semantic validation — structural validity is not proof of correctness | Covered (new) | [AUTH-48](auth-catalog.md) fail criteria | [AUTH-24](auth-catalog.md) | L21 | Stage-3 D20 rationale | Acceptance path shows evaluation *after* validation | Eval (valid-but-wrong fixture must fail) |
| AR-12 | Least-privilege tool/data access per agent | Shared | [AUTH-53](auth-catalog.md) | [AUTH-16](auth-catalog.md), [AUTH-33](auth-catalog.md) | L11, L15 | BIS User roles; Stage-3 D19 | Envelope per agent; task-scoped credential; deny-by-default proven | Adversarial test (out-of-envelope call denied) |
| AR-13 | Purpose-built actions, not raw infrastructure access | Covered (new) | [AUTH-54](auth-catalog.md) | [`agent-tool-design`](agent-tool-design.md) never-register list | L7, L13 | Stage-3 D19 | Registry diffed against never-register list in CI | Registry/CI check |
| AR-14 | Deterministic pre-execution authorization | Covered | [AUTH-44](auth-catalog.md) | [AUTH-34](auth-catalog.md), [AUTH-14](auth-catalog.md) | L11, L15 | Stage-3 D19 | Per-call verdict recorded, including denials | Adversarial test (unauthorized call denied while registered) |
| AR-15 | Prohibited actions technically unavailable, not prompt-discouraged | Covered | [AUTH-53](auth-catalog.md), [AUTH-44](auth-catalog.md) | [AGENT-01](agentic-failure-modes.md), [AUTH-32](auth-catalog.md) | L11 | Stage-3 D19 | Denial enforced in code; prompt text carries no load-bearing rule | Adversarial test |
| AR-16 | Human approval before high-risk / irreversible effects | Covered | [AUTH-14](auth-catalog.md) | [AUTH-55](auth-catalog.md), [`human-in-the-loop`](human-in-the-loop.md) | L13, L11 | BIS Approval triggers; Stage-3 D6 | Named approver, recorded decision, before the effect | Adversarial test (effect blocked without approval) |
| AR-17 | Explicit workflow states for business-critical automation | Covered (new) | [AUTH-55](auth-catalog.md) | [AUTH-06](auth-catalog.md) | L13, L6 | BIS Scope; Stage-3 D20 | Declared state model, versioned, outside the model's reach | Deterministic test |
| AR-18 | Software decides permitted transitions, not the AI | Covered (new) | [AUTH-55](auth-catalog.md) | [AUTH-06](auth-catalog.md) | L13 | Stage-3 D20 | Transition map in code; agent proposal ≠ transition | Adversarial test (invalid transition refused) |
| AR-19 | Mandatory gates satisfied before protected boundaries | Covered (new) | [AUTH-55](auth-catalog.md) | [AUTH-20](auth-catalog.md), [AUTH-43](auth-catalog.md), [AUTH-14](auth-catalog.md) | L13, L21 | Stage-3 D20 | Gate results recorded by owning control; no bypass path | Adversarial test (advance without gate refused) |
| AR-20 | AI judgement bounded to its authorized state | Covered (new) | [AUTH-55](auth-catalog.md), [AUTH-53](auth-catalog.md) | [AUTH-45](auth-catalog.md) | L13, L11 | Stage-3 D19+D20 | Scope expansion requires governed change, not runtime decision | Adversarial test |
| AR-21 | Defined failure routing (retry / correct / evidence-request / escalate / rollback / terminate) | Covered | [AUTH-26](auth-catalog.md), [AUTH-55](auth-catalog.md) | [AUTH-15](auth-catalog.md), terminal states in [`agent-run-record`](agent-run-record.md) | L13, L14 | BIS Failure handling | Declared route per failure class; exhaustion escalates | Adversarial test per route |
| AR-22 | Completion verified against system state, not agent assertion | Covered (new) | [AUTH-50](auth-catalog.md) | [AUTH-25](auth-catalog.md), [AUTH-24](auth-catalog.md) | L21, L10 | BIS Acceptance criteria; Stage-3 D20 | Observable completion condition; verification reads state of record | Deterministic test (asserted-done vs observed-not-done → `incomplete`) |
| AR-23 | Failures captured as reusable learning / regression cases | Covered (new) | [AUTH-51](auth-catalog.md) | [AUTH-29](auth-catalog.md), [AUTH-31](auth-catalog.md) | L21, L10 | Stage-3 D20 | Case with inputs, context, configuration, expected + actual | Registry/CI check (incident closure requires a case) |
| AR-24 | Regression replay across model / prompt / agent / eval / tool / workflow changes | Covered (new) | [AUTH-51](auth-catalog.md) | [AUTH-24](auth-catalog.md) | L22, L21 | Stage-3 D20 | Replay results attributable to the exact configuration | Replay |
| AR-25 | Model qualification before production authority | Covered (new) | [AUTH-52](auth-catalog.md) | [AUTH-37](auth-catalog.md), [AUTH-43](auth-catalog.md) | L22, L15 | Stage-3 D20 | Thresholds declared pre-result; provider drift detected | Replay + eval at declared thresholds |
| AR-26 | Evaluator changes keep prior failures detectable | Covered (new) | [AUTH-52](auth-catalog.md), [AUTH-51](auth-catalog.md) | [AUTH-48](auth-catalog.md) | L22, L21 | Stage-3 D20 | Detectability replay over previously-caught failures | Replay |
| AR-27 | Production failures produce durable control improvements | Covered (new) | [AUTH-51](auth-catalog.md) pass criteria | [AUTH-14](auth-catalog.md) | L21, L22 | Stage-3 D20 | Each material failure linked to a governing-mechanism change | Registry/CI check |
| AR-28 | Explicit authority envelope per agent | Covered (new) | [AUTH-53](auth-catalog.md) | [AUTH-16](auth-catalog.md), [`development-authority`](development-authority.md) for the repo axis | L11 | BIS User roles; Stage-3 D19 | Machine-readable envelope: read/create/modify/execute/request/approve/never | Adversarial test + registry/CI check |
| AR-29 | Authorization decided and enforced outside the model | Covered | [AUTH-44](auth-catalog.md), [AUTH-32](auth-catalog.md) | [AGENT-01](agentic-failure-modes.md), [`agent-runtime-harness`](agent-runtime-harness.md) | L11, L15 | Stage-3 D19 | Deterministic verdict path; model proposes, wrapper decides | Adversarial test |
| AR-30 | Auditability — reconstruct actor, config, decisions, gates, transitions, outcome | Covered | [AUTH-29](auth-catalog.md), [AUTH-30](auth-catalog.md), [AUTH-31](auth-catalog.md) | [`agent-run-record`](agent-run-record.md) AgentRun envelope | L14, L10 | Stage-3 D14 | Complete run record incl. denials, gates, terminal state | Deterministic test (record retrievable + complete) |

---

## Classification summary

| Class | ARs |
|-------|-----|
| **Covered by the pre-existing catalog** | AR-01, 03, 07, 08, 10, 14, 15, 16, 21, 29, 30 |
| **Covered by a CANDIDATE control added for this profile** | AR-02, 04, 05, 06, 09, 11, 13, 17, 18, 19, 20, 22, 23, 24, 25, 26, 27, 28 |
| **Shared (pre-existing owner + new control)** | AR-12 |
| **Gap / Duplicate / Application-specific** | none — every gap became a control; no AR duplicates an existing control's obligation verbatim; application-specific detail stays in the adopter's BIS |

The eight CANDIDATE controls behind this profile: [AUTH-48](auth-catalog.md) Output Evaluation · [AUTH-49](auth-catalog.md) Claim Grounding · [AUTH-50](auth-catalog.md) Completion Verification · [AUTH-51](auth-catalog.md) Regression Corpus · [AUTH-52](auth-catalog.md) AI Change Qualification · [AUTH-53](auth-catalog.md) Agent Scope · [AUTH-54](auth-catalog.md) Tool Contract · [AUTH-55](auth-catalog.md) Automation Workflow Gate.

---

## Adoption sequence (bottom-up)

1. **Declare** — BIS names the automation, its autonomy rung, its agents' envelopes (AR-28), its workflow states (AR-17), and its acceptance criteria (AR-02/03).
2. **Select** — Stage-3 sweep of domains 19 and 20; every AR row above resolves to selected-or-rejected with a reason, per the reconciliation rule.
3. **Build** — the harness ([`agent-runtime-harness`](agent-runtime-harness.md)) enforces domains 19; the evaluation and workflow surfaces enforce domain 20.
4. **Prove** — the test/eval column is the evidence plan; bind proof in the application repo per [AUTH-24](auth-catalog.md) and [`repo evidence binding`](evidence-model.md).
5. **Operate** — post-deployment metrics per [`agent-evaluation`](agent-evaluation.md); failures feed the corpus (AR-23); changes re-qualify (AR-25/26); autonomy moves only on evidence.

---

## Non-goals

- This profile does not add obligations; the cited control text is the obligation.
- It does not assign compliance scores; pass/fail lives in the adopter's tests and CI.
- It is not a certification. Formal gates are defined by **your** policies, tests, and CI.

## Related

- [`auth-catalog.md`](auth-catalog.md) — the controls this profile cites (domains 19 and 20 are candidate)
- [`agent-runtime-harness.md`](agent-runtime-harness.md) · [`agent-tool-design.md`](agent-tool-design.md) · [`agent-run-record.md`](agent-run-record.md) · [`agent-evaluation.md`](agent-evaluation.md) · [`agentic-failure-modes.md`](agentic-failure-modes.md)
- [`human-in-the-loop.md`](human-in-the-loop.md) — the authority mechanism every gate routes to
- [`../docs/build-intent-specification.md`](../docs/build-intent-specification.md) — where the profile's template fields are declared
- [`../CONFORMANCE.md`](../CONFORMANCE.md) — implementations mirroring this profile declare it like any catalog
