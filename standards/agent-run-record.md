# Agent run record — the AgentRun model

> Normative short for the **durable record of one agent execution**: what it was asked, what it proposed, what was allowed, and how it ended. Deliberately **not** called a trace. Stack-agnostic.

Satisfies [AUTH-29 (Audit Log)](auth-catalog.md#auth-29--audit-log-authority), [AUTH-30 (Traceability)](auth-catalog.md#auth-30--traceability-authority), and [AUTH-31 (Decision Logging)](auth-catalog.md#auth-31--decision-logging-authority) for agentic execution. Written by [`agent-runtime-harness.md`](agent-runtime-harness.md) step 6.

---

## Problem

An agent acts. Rows change, mail sends, tickets open, a pull request appears. Afterwards the evidence is the side effects and, if anyone kept it, a chat transcript.

That is not enough to do any of the things the organization will need to do. Incident response cannot reconstruct what happened. Review cannot judge whether the agent behaved well. Evaluation has no population to measure. And the case for raising the agent's autonomy cannot be made, because the argument is precisely the run history nobody kept.

The gap is usually not indifference. Teams log plenty — token counts, latencies, errors, sometimes the full transcript. What they lack is a record with a **subject**: one run, identified, with its authority, its decisions, and its ending. Logs are organized around the system; the run record is organized around the run.

---

## Why not call it a trace

The word is taken three times over in this standard, which is why the run record gets its own name.

| Existing sense | What it means |
|----------------|---------------|
| `TRACE-NN` | A contract-drift pattern class ([`trace-catalog.md`](trace-catalog.md)) |
| "Trace & bind evidence" | Sentinel stage 2 — a human-gated assurance activity ([`sentinel-program.md`](sentinel-program.md)) |
| [AUTH-30](auth-catalog.md#auth-30--traceability-authority) Traceability | Correlation ids reconstructing a request across services — distributed tracing in the ordinary sense |

Calling the agent execution record a "trace" would collide with all three, and the collisions are not harmless: a finding citing "the trace" would be ambiguous between a drift class, an audit activity, and a correlation chain. The record is an **AgentRun**. See [Glossary — the four senses of "trace"](../docs/glossary.md#the-four-senses-of-trace--do-not-conflate).

An AgentRun **carries** an AUTH-30 correlation id so it can be joined to the distributed trace. It does not replace it. The distributed trace answers *where did this request go*; the AgentRun answers *what did this agent decide, and was it allowed*.

---

## The AgentRun envelope

One record per run, written at start and closed at termination.

| Field | Purpose |
|-------|---------|
| `run_id` | Stable identity for the run. The thing everything else cites. |
| `correlation_id` | The AUTH-30 id joining this run to the surrounding request chain. |
| `parent_run_id` | Set when spawned by another run — makes the agent graph reconstructable ([`multi-agent-restraint.md`](multi-agent-restraint.md)). |
| `actor` | The authenticated human or service on whose behalf the run executes. |
| `authority_ref` | The credential or role the run acted under — the reference, never the secret. |
| `goal` | What was requested, as received. |
| `autonomy_rung` | The rung this capability holds, per [`human-in-the-loop.md`](human-in-the-loop.md). Recorded because it is the claim the run's behaviour will be judged against. |
| `harness_version` | Which control wrapper ran. Behaviour is a property of the wrapper, so the version is part of the record. |
| `model_ref`, `prompt_version` | [AUTH-37](auth-catalog.md#auth-37--model-selection-authority) and [AUTH-35](auth-catalog.md#auth-35--prompt-governance-authority). Without these, a behaviour change cannot be attributed. |
| `budgets_granted` | The bounds this run was given, not the defaults in force today. |
| `started_at`, `ended_at` | Wall-clock span. |
| `terminal_state` | Exactly one value from the enum below. |

The two fields most often missing are `harness_version` and `budgets_granted`. Both matter because the answer to "why did this run behave differently" is frequently that it ran under different control, and a record that omits its own governing configuration cannot show that.

---

## Events

Append-only, ordered, within a run. Each event carries the `run_id`, a sequence number, and a timestamp.

| Event | Records |
|-------|---------|
| `context_assembled` | What entered the window for this step, with trust tags — references, not necessarily full payloads |
| `model_turn` | The step the model proposed, and the token and cost accounting for it |
| `tool_proposed` | Tool name and resolved arguments, redacted per policy |
| `tool_authorized` / `tool_denied` | The authorization verdict and the reason. **Denials are recorded.** |
| `tool_result` | Outcome, status, and the trust tag applied to the result |
| `gate_requested` / `gate_decided` | The approval sought, the named approver, the decision, the basis |
| `budget_checked` / `budget_exceeded` | Which budget, the value, the limit |
| `memory_written` | The persisted fact and its provenance ([AUTH-47](auth-catalog.md#auth-47--agent-memory-integrity-authority)) |
| `subagent_spawned` | Child `run_id` and the authority delegated to it |
| `effect_applied` | The idempotency key and the effect, so replays are detectable |
| `error` | Failure, with whether it was recovered |
| `terminated` | The terminal state and what caused it |

**Denied and failed steps are the most diagnostic events and the first to be dropped.** A record containing only successful calls describes a run that never met a boundary, which is exactly the run nobody needs to investigate. An authorization denial is evidence that a control worked; omitting it discards the proof.

---

## Terminal states (normative enum)

Every run ends in exactly one. This enum is the answer to "how did it stop," and its absence is [`agentic-failure-modes.md`](agentic-failure-modes.md) AGENT-03.

| State | Meaning |
|-------|---------|
| `completed` | The goal was met and the agent declared it. |
| `incomplete` | The agent stopped without meeting the goal and without error — it ran out of useful moves. A legitimate outcome that must not be recorded as success. |
| `budget_exhausted` | A declared bound was reached. Which bound is on the event. |
| `gate_refused` | A human declined an approval the run required. |
| `cancelled` | An operator stopped the run in flight ([AUTH-28](auth-catalog.md#auth-28--async-job--long-running-task-authority)). |
| `failed` | An unrecoverable error ended the run. |

A run whose process died without writing a terminal state has no terminal state — the record is incomplete and should be visible as such rather than inferred. Reconciliation of runs with an open envelope and no `terminated` event is an operational obligation, not a reporting nicety.

`incomplete` earns its own value because collapsing it into `completed` corrupts every downstream metric. A capability that quietly gives up on a third of its runs looks healthy if giving up is filed as success.

---

## Redaction and retention

The run record is an audit artifact, so it inherits audit obligations rather than debug-log habits.

- **Redact at write time.** Arguments, results, and context references are redacted before persistence, not before display. A record that captured a secret or a regulated field has moved the exposure rather than removed it ([`ai-coder-hard-stops.md`](ai-coder-hard-stops.md) §2–§3).
- **Store references, not payloads, where volume demands it** — but the reference must resolve for the record's full retention period, or the record is decorative.
- **Retention is declared** and matches the purpose: incident response, evaluation windows, and any regulatory obligation over the actions taken.
- **The record is not agent-writable.** It sits in the forbidden zone of [`development-authority.md`](development-authority.md). An agent that can edit its own run record has no run record.

---

## What this is not

| Not | Because |
|-----|---------|
| A debug log | Logs are organized around the system and rotate on operational schedules. The run record is organized around the run and retained on audit terms. |
| A transcript | A transcript is unstructured, lossy under compaction, and holds no authorization verdicts, gate outcomes, or budget state. |
| A distributed trace | AUTH-30 spans answer where a request travelled. The AgentRun answers what was decided and permitted. They are joined by `correlation_id`. |
| Durable run state | State is what the *next step reads*; the record is what a *later reader reconstructs*. Overlapping fields, opposite consumers ([`agent-context-and-state.md`](agent-context-and-state.md)). |
| Evaluation | The record is the population. Metrics computed over it are [`agent-evaluation.md`](agent-evaluation.md). |

---

## UDALI placement

| Concern | Owner |
|---------|-------|
| Event emission from the loop | **Logician** band — **L15 (AI services)**, emitted at **L14 (events)** |
| Envelope and event schema | **Designer** band — **L5 (entity)** / **L7 (DTO)** |
| Redaction before write | **L17 (middleware)** |
| Durable storage and retention | **Adapter** band — **L10 (repository)** |
| Correlation id propagation | **L17**, joined to platform tracing |
| Surfacing run state to users | **Unifier** band — renders; see [`signal-stack-compliance.md`](signal-stack-compliance.md) |

---

## Forbidden patterns

- **Success-only records.** Denials, refusals, and failures omitted.
- **Transcript as record.** Chat history kept in place of structured events.
- **Missing governing configuration.** No `harness_version`, `model_ref`, `prompt_version`, or `budgets_granted`.
- **Inferred termination.** Terminal state derived from the absence of later events.
- **`incomplete` filed as `completed`.** Abandonment recorded as success.
- **Redaction at read time.** Sensitive values persisted and filtered on display.
- **Unresolvable references.** Pointers that expire before the record does.
- **Agent-writable records.** The subject of the audit can edit the audit.
- **Orphaned children.** Spawned runs with no `parent_run_id`, so the agent graph cannot be rebuilt.

---

## Evidence (what assurance expects)

| Claim | Evidence anchor |
|-------|-----------------|
| Every run produces a record | Test asserting a record exists for successful, denied, and failed runs |
| Denials are recorded | Test forcing an authorization denial and asserting the event |
| Terminal states are exhaustive | The enum, plus a test per state |
| Governing configuration is captured | Schema with non-nullable `harness_version`, `model_ref`, `prompt_version`, `budgets_granted` |
| Redaction happens at write | Test writing a secret-shaped value and asserting it is absent from storage |
| Records are immutable to the agent | Test proving the agent's credential cannot write the store |
| Correlation joins the platform trace | A run record joined to its distributed trace by `correlation_id` |
| Agent graph is reconstructable | Test spawning a subagent and rebuilding the parent-child chain |
| Retention is enforced | Retention policy plus evidence of enforcement |

Per [AUTH-24](auth-catalog.md#auth-24--test--verification-authority), the test is the proof.

---

## Delivery lanes

| Lane | Use |
|------|-----|
| **Navigator** | Design the record with the capability. It is the precondition for raising autonomy later, so deferring it caps the rung the capability can ever justify. |
| **Investigator** | The run record is the first evidence for any agent defect — it is the Path A artifact. An agent defect with no run record cannot leave Stage 1 honestly; the missing record is itself the finding (AGENT-07). |
| **Sentinel** | Attest that records exist, are complete, are immutable to the agent, and are retained. Cite `HORI_AUTH` for the immutability boundary. |

---

## Public checklist

- [ ] One record per run, with a stable `run_id`.
- [ ] `correlation_id` joins the run to the surrounding request chain.
- [ ] `harness_version`, `model_ref`, `prompt_version`, and `budgets_granted` are all captured.
- [ ] `autonomy_rung` is recorded.
- [ ] Denied, refused, and failed steps are all recorded.
- [ ] Terminal state is one declared value; `incomplete` is distinct from `completed`.
- [ ] Runs with no terminal state are reconciled, not ignored.
- [ ] Redaction occurs before persistence.
- [ ] The agent cannot write the record store.
- [ ] `parent_run_id` is set for spawned runs.
- [ ] Retention is declared and matches evaluation and incident needs.

---

## Related

- [`agent-runtime-harness.md`](agent-runtime-harness.md) — writes the record
- [`agent-context-and-state.md`](agent-context-and-state.md) — state versus record
- [`agent-evaluation.md`](agent-evaluation.md) — metrics computed over records
- [`human-in-the-loop.md`](human-in-the-loop.md) — the evidence that earns a rung
- [`development-authority.md`](development-authority.md) — the forbidden zone protecting the record
- [`evidence-model.md`](evidence-model.md) — citation namespaces and binding rules

These documents do **not** constitute legal or regulatory certification. Formal gates are defined by **your** policies, tests, and CI.
