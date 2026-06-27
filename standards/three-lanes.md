# The three lanes — Navigator, Investigator, Sentinel

> Normative description of the three AADM delivery lanes. Each lane has three stages with a human gate per stage. Lanes do not collapse into each other; they hand off explicitly. Stack-agnostic.

## Why three lanes

Engineering work answers one of three primary questions:

| Primary question | Lane | Workflow id |
|------------------|------|-------------|
| What planned change are we making, and are we gated to build it? | **Navigator** | `delivery` |
| What broke versus expected, and what is the evidence-gated fix scope? | **Investigator** | `defect` |
| Does the system already match AUTH, UDALI, gates, and evidence — vertically and horizontally — beyond one ticket? | **Sentinel** | `assurance` |

Lanes are **equal weight**. None is "the real work" and the others "support work." Mixing lanes silently produces low-quality outcomes — open-ended hypothesizing on broken-vs-expected, attesting features that haven't shipped, building features that fix a defect that was never classified.

Lanes sit **above** the UDALI band roles (Unifier, Designer, Adapter, Logician, Integrator). The same person can wear a UDALI band hat and operate in any lane. The lane labels the *posture*; the band labels the *placement*.

---

## Lane separation rules

These are normative, not stylistic:

1. **First match wins.** When intent is ambiguous, the order of evaluation is: Investigator (broken-vs-expected with anchors) → Navigator (planned change) → Sentinel (assurance pass at program scope).
2. **No silent lane switches.** When a Navigator scope contains a defect-shaped narrative, the work splits: Investigator runs first on the defect, Navigator resumes on the planned change after bounded remediation or an explicit waiver.
3. **No collapsing.** Sentinel does not become "a bigger bug ticket." Investigator does not become "open-ended help." Navigator does not skip Verify.
4. **Hand off, do not redefine.** A Sentinel pass that surfaces a concrete defect opens an Investigator thread with the anchors carried over. An Investigator pass that needs new contracts or UX opens a Navigator slice after classification.

---

## Navigator — planned delivery

> Persona: the agent or person guiding planned change through to ship-ready.

### What Navigator covers

| Shape | Examples | Notes |
|-------|----------|-------|
| **Net new capability** | New surface, new flow, greenfield API or UI | Map AUTH controls and UDALI bands during Scope. |
| **Material improvement** | Extends an existing feature (fields, behavior, performance, UX); meaningful refactor | Same gates; stress contracts, migrations, compatibility, horizontal touch. |

### Stages

| Stage | Name | Intent |
|-------|------|--------|
| 1 | **Scope** | Intent, constraints, stakeholders, rough UDALI / AUTH touchpoints. Explicit non-goals. **Not** approval to build. |
| 2 | **Verify** | Acceptance criteria before implementation: contracts, tests, security/readiness, ownership — tied back to Scope. |
| 3 | **Readiness** | Human sign-off; implementation authorized subject to repo CI. |

Each stage carries a **human gate** — the stage is not satisfied until a human signs off. Automation can prepare and propose; it cannot self-approve.

### Stage 1 — Scope

**Outputs required:**

- Outcome statement (user or system) in one sentence.
- Definition of done in checkable language.
- Explicit **non-goals** (what is intentionally out of scope).
- Rough UDALI band touchpoints — which bands materially apply.
- Rough AUTH controls in scope — at minimum the obligations that could be violated by the change.
- Named owner.

**Common failure mode.** "Implement now, define later." Scope without non-goals is not scope.

### Stage 2 — Verify

**Outputs required:**

- Acceptance criteria, written as falsifiable statements.
- Contract sketches for any new or changed interfaces (DTOs, event payloads, tool schemas).
- AUTH obligations mapped to specific tests or runtime checks.
- Readiness signals named (rollout plan, feature flag if any, observability, rollback).
- Layer map — for each UDALI band that applies, what changes and where.

**Common failure mode.** Acceptance criteria too vague to fail.

### Stage 3 — Readiness

**Outputs required:**

- Verify outputs are filled in.
- Named approver (or approvers) for Readiness.
- Recorded approval and timestamp (per AUTH-14).
- Test plan exists; CI is green or has a documented exception.
- Rollback plan exists where the change is consequential.

**Common failure mode.** Approval inferred from "no one objected."

### When Navigator hands off

- **Broken-vs-expected becomes dominant** → Investigator (`debug_defect` with `bug_context`).
- **The ask is "prove the standard is met" across more than this slice** → Sentinel.

---

## Investigator — evidence-gated defect resolution

> Persona: the agent or person resolving a defect with evidence discipline, not speculation.

### What Investigator covers

Anything framed as **observed versus expected**: bugs, errors, flaky tests, incidents, "it broke after the deploy," "this used to work."

Investigator runs whether the user says "bug" or not — the discriminator is the *shape* of the narrative.

### Stages

| Stage | Name | Intent |
|-------|------|--------|
| 1 | **Investigate & evidence** | One atomic recon action per turn. Build the evidence needed to classify. Status: `UNCLASSIFIED`. No remediation, no architectural guesses. |
| 2 | **Diagnose & classify** | Once qualifying evidence is available, name the root cause and the bounded scope of fix. Status moves to `CLASSIFIED`. |
| 3 | **Scoped remediation** | Apply the controlled fix in declared scope. Repo CI is the verdict. |

Each stage carries a **human gate**.

### Stage 1 — Investigate & evidence (Path A)

**Posture.** "Path A" means recon-only.

**Minimum to qualify for Stage 2:**

- Symptom — what was observed.
- Expectation — what was supposed to happen.
- At least one **anchor** — a path:line, a failing test name, an error string, a route, a stack frame, a screenshot of state.
- Reproduction or, if not reproducible, the conditions under which it appeared.

**Forbidden in Stage 1:**

- Editing code beyond a no-op recon.
- Recommending a fix.
- Open-ended hypothesizing without anchors.

**Common failure mode.** Skipping to Stage 2 because the situation "feels obvious." Defect narratives that feel obvious are exactly where premature fixes corrupt data or hide root causes.

### Stage 2 — Diagnose & classify (Path B)

**Outputs required:**

- Named root cause anchored to code or contract.
- Classification — which AUTH obligation, SEAM crossing, or TRACE pattern best names the defect class.
- Bounded fix scope — what files, modules, or behaviors are in scope; what is out.
- Status: `CLASSIFIED`.

**Common failure mode.** Classifying without anchors. If Stage 1 didn't produce evidence, Stage 2 cannot happen.

### Stage 3 — Scoped remediation

**Outputs required:**

- The fix lives inside the scope declared in Stage 2. Expanding scope opens a separate Navigator slice.
- Tests added or updated per AUTH-24.
- Regression check named and ideally added to CI.
- Human approval recorded.

**Common failure mode.** "While I was in there…" — drive-by refactors that turn a one-line fix into a 50-file change. Investigator scope is tight by design.

### When Investigator hands off

- **Classification reveals missing capability or contracts** → Navigator slice after classification (not before; Investigator does not skip stages retroactively).
- **Wider "are we aligned?" question** → Sentinel pass.

---

## Sentinel — assurance

> Persona: the agent or person proving the system already matches the standard.

### What Sentinel covers

Sentinel proves alignment with the standard for **existing** posture. It is **not** scoped to a single feature brief (that's Navigator Readiness) nor a single defect thread (that's Investigator Stage 2 classification).

Sentinel has two axes — both required, equal weight:

- **Vertical.** Trace scoped concerns UI → metal through UDALI bands. Bind ownership, enforcement, evidence per hop.
- **Horizontal.** Across DTO/schema edges, service-to-service calls, feature/domain seams, role handoffs, async/event sequencing, payloads. Show no silent drops and no unowned malformed crossing.

### Ontology vs scope of attestation

The full vertical model and implicit dependencies stay valid even when a team only touches part of the stack — skipping a hop **moves risk**, it does not erase failure modes.

Each Sentinel pass must say what it **proves now** (required seams), what it **recommends widening** (additional seams), and what is **out of scope** (latent seams / explicit non-goals). That is **not** a mandate to implement every UDALI band on every ticket.

### Stages

| Stage | Name | Intent |
|-------|------|--------|
| 1 | **Scope & frame** | Which slices, seams, AUTH themes, domains are in scope for this assurance pass. Attestation depth declared. |
| 2 | **Trace & bind evidence** | Vertical traces and horizontal matrix; each claim has anchors or explicit gap/risk with owner. |
| 3 | **Attest readiness** | Human sign-off: proven / assumed / deferred; follow-ups (Navigator or Investigator as needed). |

Each stage carries a **human gate**.

### Stage 1 — Scope & frame

**Outputs required:**

- The slice (routes, UX entry, services, surfaces).
- Attestation depth — `narrow`, `standard`, or `expansive`.
- Vertical claim — the deepest hop this pass claims (`presentation_only`, `through_application_boundary`, `through_domain_and_data`, `full_vertical_ui_to_metal`).
- Horizontal seam kinds in scope (`schema_contract`, `service_boundary`, `auth_trust_boundary`, `external_integration`, `async_or_event`, `guidance_feedback`).
- Optional: SEAM-NN ids and AUTH-NN ids picked from the catalogs.

**Common failure mode.** Scope creeping during Stage 2.

### Stage 2 — Trace & bind evidence

**Outputs required:**

- Vertical trace per critical concern from UI to the deepest declared hop.
- Horizontal matrix per declared seam kind.
- Each cell carries an anchor or a deferred-gap with a named owner. **No blank cells.**
- Findings cite `AUTH-NN`, `SEAM-NN`, `TRACE-NN`, `VERT_*`, `HORI_*`, `L*` as applicable.
- Where attestation closure is used, the closure required/additional/latent tiers are observed.

**Common failure mode.** Documentation prose treated as evidence. The evidence anchor must be in the application repository (path, test, CI link) — or be an explicit deferred-gap.

### Stage 3 — Attest readiness

**Outputs required:**

- Each required seam row is one of: **proven** (anchor), **assumed** (with stated justification), or **deferred** (with named owner and follow-up).
- Named human attester.
- Recorded approval timestamp.
- Follow-up issues opened (Navigator for change programs, Investigator for concrete defects, Sentinel for wider scope).

**Common failure mode.** Auto-issuing an "attestation" without human sign-off. The Sentinel gate is human, by definition.

### When Sentinel hands off

- **Concrete defect surfaced** → open Investigator with the anchors carried over.
- **Missing capability or change program** → open Navigator.

---

## Edge cases and their resolutions

| Situation | Resolution |
|-----------|------------|
| Same message mixes "ship feature X" and "Y is broken" | Split threads. If delivery is blocked by broken behavior, Investigator first on Y; Navigator resumes after bounded remediation. If independent, run two parallel sequences. |
| Production incident | Incident stabilization is operational, not an AADM lane. Once framed as observed vs expected with evidence, Investigator applies. |
| Tiny one-line fix | If framed broken-vs-expected, Investigator (still three stages, proportionate depth). If framed as planned polish with no defect narrative, Navigator (still three gates in principle). |
| PR / diff review only | Sentinel if proving alignment with standard across touched seams; Navigator Verify/Readiness if the PR *is* the scoped delivery artifact. Pick by whether the ask is "prove posture" vs "authorize change." |
| Sentinel finds a concrete defect | Open Investigator with anchors carried from the assurance matrix. |
| Investigator needs new contracts or UX | After classification, open Navigator for the additive delivery slice. Do not skip Investigator stages retroactively. |
| Ambiguous intent (no clear planned change, bug, or assurance ask) | Do not guess. Return structured clarification — present the three primary questions and let the user pick. |
| Assurance vs single feature brief | Sentinel is for program-level or system-level proof. A single feature ticket without horizontal/vertical program framing defaults to Navigator. |

---

## Quick-pick reference

| You hear | Lane | First move |
|----------|------|------------|
| "Feature X / improve Y / greenfield Z" | **Navigator** | Scope statement with explicit non-goals; layer map; AUTH list. |
| "Broken / wrong / 500 / flaky / expected vs actual" | **Investigator** | Path A: gather symptom + expectation + at least one anchor. |
| "Prove we match standard / audit / vertical + horizontal seams" | **Sentinel** | Stage 1: declare slice + attestation depth + seams in scope. |

---

## Related

- [`auth-catalog.md`](auth-catalog.md) — obligations cited in any lane
- [`seam-catalog.md`](seam-catalog.md) — crossings to scope in Sentinel
- [`trace-catalog.md`](trace-catalog.md) — drift classes used in Investigator classification and Sentinel findings
- [`sentinel-program.md`](sentinel-program.md) — assurance attestation closure and audit programs
- [`ai-coder-hard-stops.md`](ai-coder-hard-stops.md) — actions that no lane authorizes
