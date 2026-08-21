# Agent tool design — the tool contract

> Normative short for **what makes a capability safe to expose to an agent**. States the six properties every registered tool must hold, and the list of capabilities that must never be registered as a tool at all. Stack-agnostic.

Companion to [`agent-runtime-harness.md`](agent-runtime-harness.md) — the harness authorizes each call; this document governs what is available to be called. Primary obligation: [AUTH-44 (Tool Invocation Authority)](auth-catalog.md#auth-44--tool-invocation-authority).

---

## Problem

A tool is usually added because an agent needed it during a demo. It gets the shape that was convenient at that moment: broad parameters, a vague description, whatever the underlying function already returned. Nobody records whether calling it twice is safe, whether its effects can be undone, or who is accountable when it fires.

The agent then treats that tool as a *capability of the system*. Whatever the tool can reach, the agent can reach — and the blast radius of the agent is exactly the union of its tools, regardless of what the prompt says it is for. A tool surface assembled by convenience is a permission model assembled by accident.

---

## One-line principle

A tool is a **governed interface**, not a convenience wrapper: narrow, typed, side-effect classified, idempotent, reversible, and audited. The agent's authority is the union of its tools — so the tool surface **is** the permission model, and it is designed deliberately or not at all.

---

## The six properties (normative)

Every registered tool MUST hold all six. A capability that cannot hold all six is either redesigned until it can, or not registered.

### 1 — Narrow

One operation with one purpose. A tool that changes behaviour based on a `mode`, `action`, or `operation` parameter is several tools wearing one name, and it defeats authorization: the harness cannot decide whether *this* call is permitted when the parameter decides what the call does.

**Test.** Can you state the tool's effect in one sentence with no "or"? If not, split it.

### 2 — Typed

Declared input schema and declared output schema, both validated at the boundary. Inputs are parsed, never asserted ([HANDOFF-08](handoff-patterns.md)). Outputs conform on exit, per [AUTH-21](auth-catalog.md#auth-21--dto--contract-boundary-authority).

The **description is part of the contract**. It is the only specification the model reads, and a misleading description causes wrong calls as reliably as a wrong schema. Describe what the tool does, what it does *not* do, and when not to call it.

### 3 — Side-effect classified

Every tool carries a declared class. The class drives authorization, gating, and retry policy — so it is a required field, not documentation.

| Class | Meaning | Default posture |
|-------|---------|-----------------|
| **read** | Returns data, changes nothing | Lowest friction; still subject to tenancy and field-level access |
| **write** | Changes state the system owns, recoverably | Authorized per call; recorded |
| **external** | Causes an effect outside the system's control — sends mail, moves money, calls a partner | Treated as irreversible unless the provider offers a proven compensating action |
| **irreversible** | Cannot be undone by any available path | Requires a human gate ([AUTH-14](auth-catalog.md#auth-14--approval-authority)); never available to an unattended rung |

An unclassified tool defaults to **irreversible**. Guessing low is how destructive capabilities become ambient.

### 4 — Idempotent

Retryable tools are idempotent against a declared key, per [AUTH-25](auth-catalog.md#auth-25--execution-idempotency-authority) — or the tool declares in writing why idempotency does not apply.

This matters more for agents than for ordinary callers. An agent retries on its own initiative, having reasoned that the previous attempt "may not have worked." A non-idempotent `write` or `external` tool in an agent loop will eventually fire twice.

### 5 — Reversible

Every `write` tool declares its undo path: a compensating operation, a soft-delete window, a transaction boundary, or an explicit statement that reversal is impossible — which reclassifies it as **irreversible**.

Reversibility is what makes a lower autonomy rung safe. An agent permitted to act unattended is really an agent whose mistakes are recoverable at acceptable cost.

### 6 — Audited

Every invocation emits an audit entry with calling agent, run id, resolved arguments, authorization verdict, and outcome — per [AUTH-29](auth-catalog.md#auth-29--audit-log-authority) and [AUTH-30](auth-catalog.md#auth-30--traceability-authority). Denied calls are audited too; a denial is evidence.

Arguments are recorded after redaction, not before. A tool audit log that captures secrets or regulated fields has traded one exposure for another.

---

## Never register these as tools

These capabilities cannot be made safe by narrowing parameters, because the risk is the capability itself. They are reached — when genuinely required — through a human-operated, separately-credentialed path outside the agent's tool surface.

| Never a tool | Why |
|--------------|-----|
| **Arbitrary code or shell execution** without an allowlisted command set and a sandbox | Equivalent to registering every capability the host possesses, including ones added later |
| **Arbitrary query execution** against a production store | Bypasses every read model, tenancy check, and field-level access rule the application implements |
| **Secret or credential retrieval** | Puts a durable credential into a transcript, a context window, and a vendor's logs |
| **Permission grant or role escalation** | Lets the agent widen its own authority; the permission model stops being a bound |
| **Anything that modifies the wrapper's own controls** — budgets, gates, tool registry, trust classification | An agent that can edit its own limits has none |
| **Anything that modifies audit logs or run records** | Destroys the evidence the whole model depends on ([`ai-coder-hard-stops.md`](ai-coder-hard-stops.md) §8) |
| **Generic outbound HTTP** to a caller-supplied URL | An exfiltration channel and an SSRF surface; register specific integrations instead |
| **Bulk destructive operations** — truncate, drop, purge tenant, mass delete | Blast radius is unbounded by construction |
| **Direct production traffic, DNS, or routing control** | [`ai-coder-hard-stops.md`](ai-coder-hard-stops.md) §7 |

Everything on [`ai-coder-hard-stops.md`](ai-coder-hard-stops.md) is out of scope for a tool surface by definition. If a task appears to require one of these, the task is reshaped — narrower tool, staging environment, human runbook — not the boundary.

---

## Tool results are untrusted input

A tool result re-enters the model's context, which makes every tool an ingress point. Per [AUTH-46](auth-catalog.md#auth-46--context-trust-boundary-authority), results are **data**, tagged with provenance, and never merged into the instruction channel.

This holds even for tools reading the system's own database, because the rows may contain text a user wrote. "Internal source" is not the same as "trusted content" — the trust question is *who authored this string*, not *which service returned it*. See [`agent-context-and-state.md`](agent-context-and-state.md).

---

## UDALI placement

| Concern | Owner |
|---------|-------|
| Tool schema, description, and side-effect class | **Designer** band — **L7 (DTO)**; the tool contract is a DTO contract |
| Tool authorization at invocation | **Adapter** band — **L11 (auth)** |
| Tool implementation reaching a domain operation | **Logician** band — **L13 (service)**; a tool calls a service, it does not reimplement one |
| Tool registry and dispatch | **L15 (AI services)**, with normalization at [SEAM-04](seam-catalog.md#seam-04--identity-normalization-alias--canonical--case) / [SEAM-05](seam-catalog.md#seam-05--dispatch--orchestration-composition) |
| Tool contract tests | **Integrator** band — **L21 (QA / verification)** |

A tool MUST NOT contain business logic of its own. Duplicated rules in a tool are the agentic form of [HANDOFF-03](handoff-patterns.md) shadow types: the tool path and the ordinary application path drift, and the agent gets the stale one.

---

## Forbidden patterns

- **The multi-tool.** One registration whose effect depends on an `action` parameter.
- **Undeclared side effects.** A tool named or described as a read that writes.
- **Optimistic classification.** Calling an `external` effect a `write` because reversal "probably" works.
- **Domain logic in the tool.** Validation or business rules implemented in the tool rather than delegated to the service.
- **Tenancy at the caller.** Trusting a tenant id the model supplied instead of resolving it from the run's authority ([AUTH-16](auth-catalog.md#auth-16--tenancy-authority), [TRACE-06](trace-catalog.md)).
- **Over-returning.** Returning whole records where the agent needs three fields — a field-level access failure ([AUTH-33](auth-catalog.md#auth-33--field-level-access-authority)) and a prompt-injection surface at once.
- **Silent registry growth.** Tools added without review because adding one is a small diff.
- **Description drift.** Behaviour changed, description unchanged — the model is now working from a stale spec ([AUTH-10](auth-catalog.md#auth-10--spec-version-enforcement-authority)).

---

## Evidence (what assurance expects)

| Claim | Evidence anchor |
|-------|-----------------|
| Registry is complete and reviewed | The enumerable tool registry, plus the review record for its last change |
| Every tool has a side-effect class | Schema or registry field, non-nullable, with a test rejecting an unclassified tool |
| Inputs are parsed | Test feeding a malformed argument and asserting rejection before any effect |
| Retryable tools are idempotent | Test invoking twice with one key and asserting one effect |
| `write` tools declare reversal | Documented undo path per tool, with a test for the compensating action |
| Irreversible tools are gated | Test asserting the call is refused without a recorded approval |
| Tenancy is server-resolved | Test supplying a foreign tenant id in arguments and asserting denial |
| Results carry provenance | Sample context payload showing trust tags on tool output |
| Nothing on the never-register list is registered | Registry diffed against the list, in CI |

Per [AUTH-24](auth-catalog.md#auth-24--test--verification-authority), the test is the proof. This document is not.

---

## Delivery lanes

| Lane | Use |
|------|-----|
| **Navigator** | Design the tool surface as a permission model during scope. Enumerate side-effect classes before implementation; a new `irreversible` tool is an architectural decision, not a task. |
| **Investigator** | "The agent did something nobody authorized" is usually a tool classified too low, a tenancy id trusted from arguments, or a second path reaching the same effect. Read the tool audit entries first. |
| **Sentinel** | Attest the registry: every tool classified, authorized, idempotent where retryable, and audited. Declare `HORI_AUTH`, and `HORI_EXT` where tools reach third parties. |

---

## Public checklist

- [ ] Every tool's effect states in one sentence with no "or".
- [ ] Every tool has a validated input schema and output schema.
- [ ] Every tool carries a side-effect class; unclassified defaults to irreversible.
- [ ] Descriptions say what the tool does not do, and when not to call it.
- [ ] Retryable `write` and `external` tools are idempotent on a declared key.
- [ ] Every `write` tool declares its undo path.
- [ ] Every `irreversible` tool sits behind a human gate.
- [ ] Tenancy and actor identity come from the run's authority, never from model-supplied arguments.
- [ ] Tools return the minimum fields needed, redacted per policy.
- [ ] Tool results enter context as tagged data, never as instructions.
- [ ] The registry is diffed against the never-register list in CI.

---

## Related

- [`agent-runtime-harness.md`](agent-runtime-harness.md) — the wrapper that authorizes each call
- [`agent-context-and-state.md`](agent-context-and-state.md) — how results re-enter context
- [`agentic-failure-modes.md`](agentic-failure-modes.md) — named anti-patterns
- [`ai-coder-hard-stops.md`](ai-coder-hard-stops.md) — actions never to recommend or register
- [`module-interface-seam-vocabulary.md`](module-interface-seam-vocabulary.md) — Interface discipline this document applies

These documents do **not** constitute legal or regulatory certification. Formal gates are defined by **your** policies, tests, and CI.
