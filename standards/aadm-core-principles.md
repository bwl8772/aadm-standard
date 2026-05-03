# AADM core principles

These principles describe **delivery intent** for **agent-aware** programs. They are **educational** and **tooling-neutral**—adapt wording to your organization’s policies.

For layer vocabulary, see [`docs/udali-personas.md`](../docs/udali-personas.md) (five groupings) and the optional L1–L22 lattice in [`docs/udali-22-layer-model.md`](../docs/udali-22-layer-model.md). For deeper dives, see the linked standards files at the end of each section.

---

## 1. Architecture before code

**Definition**  
Stabilize boundaries, contracts, AUTH-sensitive areas, and verification thinking **before** spreading implementation across the codebase.

**Why it matters**  
Speed—especially with generated code—cements the wrong shapes early. Cheap demos become expensive cores.

**What good looks like**  
Thin spikes when needed; promoted designs have explicit contracts and layer placement before wide rollout.

**Common failure mode**  
“Ship the prototype” without reconciling contracts, ownership, or rollback—and patch forever.

**Public checklist**

- [ ] Outcome and scope written down (for example BIS-lite / PRD-lite).  
- [ ] Boundary sketches exist for APIs, events, or modules touched.  
- [ ] AUTH-sensitive operations named before bulk coding.  
- [ ] Spikes labeled disposable until reviewed.

Further reading: [`architecture-before-code.md`](architecture-before-code.md).

---

## 2. Human-in-the-loop checkpoints

**Definition**  
Named moments where **people** retain approval authority for irreversible, sensitive, or policy-bound actions—automation may prepare, not substitute.

**Why it matters**  
Legal, financial, and safety accountability stays with the organization—not with models or scripts.

**What good looks like**  
Clear triggers, recorded approvers, and reversible paths where feasible.

**Common failure mode**  
One-click actions that merge privilege escalation or bulk deletion without review.

**Public checklist**

- [ ] High-risk actions identified per feature.  
- [ ] Approval path documented (role + system).  
- [ ] Evidence of review attachable to releases or tickets.  
- [ ] Agents cannot bypass governance solely via tooling config.

Further reading: [`human-in-the-loop.md`](human-in-the-loop.md).

---

## 3. Agentic systems by design

**Definition**  
Where **agents or models** participate, their role is **explicit**: bounded inputs/outputs, declared surfaces, human oversight where required—not ambient automation everywhere.

**Why it matters**  
Undisciplined agent paths blur ownership and bypass the same reviews expected of human-written code.

**What good looks like**  
Agent steps appear on layer maps; outputs validated like any external input; failures degraded safely.

**Common failure mode**  
“Drop in an AI button” with cross-cutting database access and no schema or policy story.

**Public checklist**

- [ ] Agent capability tied to a named workflow or API.  
- [ ] Outputs validated against contracts/schemas.  
- [ ] Logging avoids leaking secrets or unsanitized PII.  
- [ ] Human checkpoint exists where policy demands it.

---

## 4. No monoliths (undifferentiated lumping)

**Definition**  
Avoid fusing **unrelated responsibilities** into one unmaintainable boundary without an explicit, reviewed decision to consolidate.

**Why it matters**  
Hidden coupling slows change and amplifies blast radius—worse when agents add code quickly.

**What good looks like**  
Bounded areas with stable interfaces; consolidation is deliberate and documented.

**Common failure mode**  
“God module” utilities absorbing auth, domain rules, and integrations without cohesion.

**Public checklist**

- [ ] New code has a named home aligned to concerns.  
- [ ] Shared packages have ownership and purpose statements.  
- [ ] Consolidation decisions recorded (why, what stays separated inside).

Further reading: [`no-monolith-rule.md`](no-monolith-rule.md).

---

## 5. No skipped layers

**Definition**  
For each change, **accountability groupings** (Unifier, Designer, Adapter, Logician, Integrator—or your equivalent) are considered; none are skipped silently when they materially apply.

**Why it matters**  
Skipping shows up later as production incidents: UI-only security, schema drift, missing audits.

**What good looks like**  
Layer map attached; **N/A** marked with a one-line rationale when a grouping truly does not apply.

**Common failure mode**  
Patching symptoms in the wrong tier (for example presentation-only fixes for domain authorization bugs).

**Public checklist**

- [ ] Layer map completed for meaningful changes.  
- [ ] Reviewers assigned by touched groupings when helpful.  
- [ ] Cross-cutting risks (AUTH, data) explicitly listed.

Further reading: [`udali-layer-boundaries.md`](udali-layer-boundaries.md).

---

## 6. Auth-aware delivery

**Definition**  
**Authentication** (who) and **authorization** (what they may do) are explicit design subjects—enforced where meaning is interpreted, not only on the outermost UI.

**Why it matters**  
Agents and APIs multiply paths that bypass a single screen—implicit AUTH fails open under automation.

**What good looks like**  
Consistent enforcement story; deny-by-default posture for sensitive operations.

**Common failure mode**  
Beautiful screens with porous APIs; “the model knows the user” without server checks.

**Public checklist**

- [ ] Sensitive verbs identified per resource.  
- [ ] Server-side enforcement present for mutating operations.  
- [ ] Secrets never embedded in examples or client bundles inappropriately.

Further reading: [`docs/auth-aware-delivery.md`](../docs/auth-aware-delivery.md).

---

## 7. Clear separation of concerns

**Definition**  
Distinct responsibilities—UX coherence, domain meaning, data access, orchestration, shipping—stay **addressable independently** behind intentional interfaces.

**Why it matters**  
Mixed concerns hide defects and make reviews ineffective: reviewers cannot see what changed in which dimension.

**What good looks like**  
Contracts between concerns; adapters at integration edges; tests scoped to responsibility.

**Common failure mode**  
Controllers embedding persistence and business rules with no service boundary.

**Public checklist**

- [ ] Each major component has one primary responsibility.  
- [ ] Cross-cutting aspects (logging, AUTH) applied consistently, not ad hoc copies.  
- [ ] Dependencies flow inward toward domain clarity—not spaghetti imports.

Further reading: [`udali-layer-boundaries.md`](udali-layer-boundaries.md).

---

## 8. Outcome-driven execution

**Definition**  
Work traces to **observable outcomes** and **success signals** agreed with product ownership—not activity for its own sake.

**Why it matters**  
Engineering throughput without outcomes ships churn: features exist but problems persist.

**What good looks like**  
Acceptance criteria tied to user or business signals; metrics or qualitative checks after release.

**Common failure mode**  
“Done” equals merged PR with no definition of user-visible success.

**Public checklist**

- [ ] Acceptance criteria written before calling the feature finished.  
- [ ] Post-release signal chosen (metric, support volume, qualitative review).  
- [ ] Scope changes update outcomes and criteria together.

Further reading: [`docs/build-intent-specification.md`](../docs/build-intent-specification.md), [`templates/prd-lite.md`](../templates/prd-lite.md).

---

## Using this document

Teams may paste sections into internal playbooks or cite them in architecture reviews. Nothing here replaces **legal counsel**, **regulated control frameworks**, or **your production enforcement stack**.

For **agent-facing** consistency, pair these ideas with an **AADM MCP server**—see [`docs/mcp-quickstart.md`](../docs/mcp-quickstart.md).
