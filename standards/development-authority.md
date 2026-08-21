# Development authority

> Normative short for the **fourth authority**: what an actor may change about the system itself. Authentication, authorization, and auditing govern behaviour at runtime. Development authority governs change to the thing that runs. Stack-agnostic.

Companion: [AUTH-aware delivery](../docs/auth-aware-delivery.md), where the four authorities are separated. Anchored by [AUTH-00 (System Authority Model)](auth-catalog.md#auth-00--system-authority-model) — development authority is the part of AUTH-00 that applies to the repository rather than to the data.

---

## Problem

AUTH has always answered three questions: who is this caller, what may they do to which resources, and who did what. All three concern **runtime** behaviour on data.

Agent-assisted delivery introduced a fourth actor that none of them describe. A coding agent does not primarily read and write rows; it reads and writes the code that decides how rows are read and written. Its output is deferred — nothing happens when it makes the change, and everything happens later, on every subsequent request.

Teams govern this by habit rather than declaration: pull request review, plus an assumption that someone would notice something alarming. That assumption fails in the specific case it most needs to hold, because the changes that matter least to a reviewer's eye — a nullability tweak, a moved authorization check, a widened schema column — are exactly the ones whose runtime consequences are largest.

---

## One-line principle

**Development authority** is the declared answer to *what may this actor change about the system itself* — stated per repository area, before an agent is pointed at the codebase, and enforced at merge rather than inferred from review attention.

---

## Why this is a fourth authority, not a case of authorization

| | Authorization | Development authority |
|---|---|---|
| **Subject** | A data resource | The system's own definition — code, schema, configuration, controls |
| **When the effect occurs** | At the moment of the call | At merge or deploy, then on every run afterwards |
| **Blast radius** | The records in scope | Every future execution, including for actors the change never considered |
| **Who is protected** | The record's owner or tenant | Everyone downstream, including the controls themselves |
| **Reversal** | Usually a compensating write | A revert plus whatever ran in the interval |

The decisive difference is **recursion**: an actor with unbounded development authority can change the code that enforces authorization. Runtime authority is bounded by the rules; development authority can rewrite the rules. It has to be governed separately, or it silently supersedes the other three.

---

## The four required declarations

An adopter allowing agents into a codebase MUST declare all four, in writing, before the agent has write access. Adopters commonly encode these in a repository configuration file so tooling can enforce them; the obligation is the declaration, not any particular format.

### 1 — Which repository areas may be changed

Every area falls in exactly one zone. Unclassified areas default to **restricted**.

| Zone | Contents | Posture |
|------|----------|---------|
| **Open** | Tests, fixtures, documentation, comments, local scripts | Agent may change freely; ordinary review |
| **Reviewed** | Ordinary feature code with no governed obligation attached | Agent may change; human review required before merge |
| **Restricted** | Schema and migrations, AUTH enforcement code, the agent runtime harness and tool registry, CI and release configuration, dependency manifests, retention and audit configuration | Agent may **propose**; a named human with authority over that area approves |
| **Forbidden** | Audit records and run records, secrets and credential stores, legal and compliance artifacts, the controls that bound the agent itself | Agent may not change, in any lane, under any instruction |

The forbidden zone is not a review threshold — it is a refusal. An agent that can edit its own budgets, gates, or audit trail has none, per [`agentic-failure-modes.md`](agentic-failure-modes.md) AGENT-01 and AGENT-07.

### 2 — Whether schema may change

Schema changes deserve their own declaration because their cost is asymmetric: a migration is trivial to write, expensive to reverse, and its damage is often silent. Declare one of:

- **No.** Agent may not author migrations. Schema change is a human task with its own review path.
- **Propose only.** Agent may author a migration as an inert artifact; a named owner reviews and applies it.
- **Yes, within named constraints** — for example additive-only, no destructive operations, no changes to columns carrying tenancy or identity.

Whichever is chosen, migrations remain gated by [AUTH-14](auth-catalog.md#auth-14--approval-authority), and destructive migrations are on the [`ai-coder-hard-stops.md`](ai-coder-hard-stops.md) list regardless of declaration.

### 3 — Whether AUTH enforcement code may change

The code implementing an AUTH obligation is the code that proves the system is governed. Declare whether an agent may modify it, and if so, what additional proof is required.

The default posture is **propose only, with the paired test required in the same change**. Per [AUTH-24](auth-catalog.md#auth-24--test--verification-authority) every obligation has a paired test, so a change to enforcement that does not touch its test is either untested or a weakening — and both are reviewable facts rather than judgement calls.

Watch specifically for **relocation**: moving an authorization check from the domain toward the edge preserves the check while destroying the guarantee ([AUTH-32](auth-catalog.md#auth-32--policy-enforcement-authority)). A diff that reads as a refactor can be an authority change.

### 4 — What validation is required before merge

Name the gate. Development authority that is declared but unenforced is a preference.

At minimum, for any change touching a restricted area:

- The paired tests for every AUTH obligation in the diff pass.
- Schema changes carry a reviewed migration and a stated reversal path.
- No net reduction in enforcement — removals are called out explicitly, not left for a reader to notice.
- A named human with authority over the area has approved, per [AUTH-43](auth-catalog.md#auth-43--release-gate-authority).
- The change is attributable to a specific agent run ([`agent-run-record.md`](agent-run-record.md)) so the reasoning is recoverable later.

---

## UDALI placement

| Concern | Owner |
|---------|-------|
| Zone declaration and its enforcement | **Integrator** band — **L22 (system integration / release)**; the gate lives with release authority |
| Schema-change constraints | **Designer** band — **L5 (entity)** and the migration path |
| AUTH-code change rules | **Adapter** band — **L11 (auth)** |
| Validation gate in CI | **L21 (QA / verification)** |

Development authority is a **release-time** control. Placing it only in a contributing guide leaves it to memory; placing it only in an agent's prompt leaves it to the model ([`agentic-failure-modes.md`](agentic-failure-modes.md) AGENT-01).

---

## Forbidden patterns

- **Undeclared write access.** An agent with repository credentials and no zone declaration — everything is implicitly open.
- **Recursive authority.** The agent can modify its own harness, budgets, gates, or tool registry.
- **Review as the only control.** Restricted areas protected solely by a reviewer noticing.
- **Enforcement change without test change.** A diff touching AUTH code whose paired tests are untouched.
- **Silent relocation.** An authorization check moved outward and described as a refactor.
- **Schema by convenience.** A migration authored to make a failing test pass.
- **Green by weakening.** Validation, assertions, or error handling loosened to satisfy the merge gate — [`ai-coder-hard-stops.md`](ai-coder-hard-stops.md) §10.
- **Unattributed change.** Merged agent work with no run record, so the reasoning cannot be reconstructed during an incident.

---

## Evidence (what assurance expects)

| Claim | Evidence anchor |
|-------|-----------------|
| Zones are declared | The written declaration, covering every path in the repository |
| Restricted areas are enforced | CI job that fails a change touching a restricted path without the required approval |
| Forbidden areas are unreachable | Test or policy proving the agent's credential cannot write those paths |
| Schema posture holds | Migration review records; test rejecting a destructive migration |
| AUTH changes carry tests | CI rule requiring paired-test changes alongside enforcement changes |
| No net enforcement loss | Diff review record, or a control-coverage comparison across the change |
| Changes are attributable | Merged change linked to an agent run id |

---

## Delivery lanes

| Lane | Use |
|------|-----|
| **Navigator** | Declare the four before granting write access. This is a scope-stage artifact; discovering it at review time means the agent has already been operating undeclared. |
| **Investigator** | When a control turns out not to have been enforced, ask when it stopped being enforced and in which change. Development-authority failures present as absent controls that everyone remembers building. |
| **Sentinel** | Attest the zones and the merge gate. Declare `HORI_AUTH` for enforcement-code rules and `HORI_SCHEMA` for the schema posture. |

---

## Public checklist

- [ ] Every repository area is assigned a zone; unclassified defaults to restricted.
- [ ] The forbidden zone includes audit records, run records, secrets, and the agent's own controls.
- [ ] Schema posture is declared: no, propose only, or yes within named constraints.
- [ ] AUTH-code posture is declared, with paired-test requirement.
- [ ] The merge gate is named, automated where possible, and fails closed.
- [ ] The agent's credential cannot write forbidden paths — proven, not assumed.
- [ ] Enforcement removals must be declared explicitly in a change.
- [ ] Every merged agent change is attributable to a run record.

---

## Related

- [AUTH-aware delivery](../docs/auth-aware-delivery.md) — the four authorities separated
- [`agent-runtime-harness.md`](agent-runtime-harness.md) — the controls the forbidden zone protects
- [`human-in-the-loop.md`](human-in-the-loop.md) — approval triggers, including tool-surface expansion
- [`ai-coder-hard-stops.md`](ai-coder-hard-stops.md) — actions no declaration can permit
- [`architecture-before-code.md`](architecture-before-code.md) — why the declaration precedes the code

These documents do **not** constitute legal or regulatory certification. Formal gates are defined by **your** policies, tests, and CI.
