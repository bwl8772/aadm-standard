# Agent context and state

> Normative short for **what an agent knows**. Covers decision-scoped context assembly, context drift as a named failure, and the durable state a running agent must hold outside its conversation. Stack-agnostic.

Companion to [`agent-runtime-harness.md`](agent-runtime-harness.md) step 2. Primary obligations: [AUTH-46 (Context Trust Boundary)](auth-catalog.md#auth-46--context-trust-boundary-authority), [AUTH-47 (Agent Memory Integrity)](auth-catalog.md#auth-47--agent-memory-integrity-authority), [AUTH-11 (Staleness)](auth-catalog.md#auth-11--staleness-authority).

---

## Problem

Context is usually built by accumulation. The conversation grows, tool results append, retrieved documents pile up, and the whole window is passed forward on every turn because that is the path of least resistance and because more information feels safer.

Two distinct failures follow, and they get treated as one.

The first is **trust**: everything in the window arrives in the same channel, so a sentence written by an attacker in a retrieved document has the same standing as an instruction written by the engineering team.

The second is **relevance**: the window fills with material that mattered earlier in the run. Superseded facts sit next to current ones with nothing marking which won, and the agent reasons over a picture that no longer describes the task.

Neither is fixed by a longer window. A larger context makes both worse, because it postpones the moment the team is forced to decide what belongs.

---

## One-line principle

Context is **assembled for a decision, not accumulated for a session**: every item is selected because this step needs it, tagged with the trust it carries, and superseded rather than appended. Durable facts the run depends on live in **explicit state outside the conversation**.

---

## Decision-scoped assembly

The unit of context is one decision, not one run. Before each step, the harness asks what *this* step needs — and that question has a different answer at step 2 and step 40.

| Assemble by accumulation | Assemble by decision |
|--------------------------|----------------------|
| Everything so far, forward | What this step requires |
| Items never leave | Items are dropped or superseded when no longer current |
| Relevance decays over the run | Relevance is re-established each step |
| Growth is unbounded | Size is a function of the decision, not the run length |
| "Which fact is current?" is the model's problem | Currency is resolved before the model sees it |

Three rules make this enforceable.

1. **Select, do not append.** Each item enters because something decided it was needed. "It was already there" is not a reason.
2. **Supersede explicitly.** When a fact is refreshed, the old value is replaced, not accompanied. Two versions of one fact in a window is an unresolved [AUTH-11](auth-catalog.md#auth-11--staleness-authority) staleness question handed to a model.
3. **Summarize with provenance.** Compaction is legitimate and often necessary, but a summary is a derived artifact: it records what it was derived from, so a wrong summary can be traced to its source rather than merely regenerated.

---

## Trust classification (normative)

Every context item carries a trust tag at assembly time. The tag travels with the item; it is not inferred later from which service returned it.

| Trust | Sources | May it instruct? |
|-------|---------|------------------|
| **System** | Prompts and policies the organization authored, versioned, and reviewed ([AUTH-35](auth-catalog.md#auth-35--prompt-governance-authority)) | Yes — this is the instruction channel |
| **Operator** | The authenticated requesting human, within their authority | Only within declared bounds; never to widen the agent's own authority |
| **Application** | Data the system owns and produced | No. Data only |
| **Untrusted** | Tool results, retrieved documents, web content, upstream agent output, any user-authored free text | No. Data only, always |

The distinction that gets missed: **"our own database" is not a trust level.** A row the application owns may contain a string a user wrote. The trust question is *who authored this text*, not *which service returned it*. A record's provenance follows the text, so user-authored content read from an internal store is **untrusted** — same as if it had come from the open web.

**The rule.** Untrusted content is never concatenated into the instruction channel. It is delivered as a labelled block whose framing states it is data, and the harness — not the model — enforces the separation.

---

## Context drift as a named failure

Drift is catalogued as [`agentic-failure-modes.md`](agentic-failure-modes.md) **AGENT-05** because it needs to be nameable in a finding. Left unnamed it presents as flakiness: failures that appear late in long runs, are hard to reproduce, and look model-shaped. Teams respond by swapping models or rewriting prompts, and the accumulation continues.

**Symptoms.** Quality degrades with run length. The agent acts on a value that was corrected earlier. It re-does completed work, or contradicts a decision it already made. Short runs are fine.

**Why it is not a token-count problem.** A small window holding one stale fact drifts. A large window whose every item is current and scoped does not. The variable is currency and relevance, not size — which is why context-window upgrades do not fix it.

**Where to look.** The assembly function. If it takes the prior window and adds to it, drift is structural rather than incidental.

---

## Durable state outside the conversation

A conversation is a poor database. It has no schema, no invariants, no query path, and it is lossy under compaction — yet running agents routinely depend on facts that exist nowhere else.

State the run depends on MUST live in explicit storage, addressable independently of the transcript:

| State | Why it cannot live in the transcript |
|-------|--------------------------------------|
| **Run identity and authority** | The credential and actor a step is authorized against must not be re-derived from text the model can influence |
| **Budget counters** | Iterations, tool calls, spend, wall-clock — the enforcement point reads these, and the model must not be able to edit them ([AUTH-45](auth-catalog.md#auth-45--agent-loop--budget-authority)) |
| **Plan and progress** | Which steps are complete, which remain. Survives compaction; makes resumption possible |
| **Gate outcomes** | What a human approved, when, and on what basis ([AUTH-14](auth-catalog.md#auth-14--approval-authority)) |
| **Effects already applied** | The idempotency ledger. Without it a retrying agent repeats side effects ([AUTH-25](auth-catalog.md#auth-25--execution-idempotency-authority)) |
| **Terminal state** | How the run ended, recorded once |

The test is simple: **truncate the transcript to its last turn.** Every fact the next step needs in order to be correct and safe must still be available. Anything that is not is a fact the system was storing in a text buffer.

Compaction, resumption, and handoff between agents are all the same operation seen from different angles — and all three are safe only if state was never in the transcript to begin with.

---

## Persistent memory across runs

Memory spanning runs is state with a longer life and a larger consequence, so it carries the same rules plus provenance.

Every persisted fact records **what wrote it, when, from what source, and under what trust level** ([AUTH-47](auth-catalog.md#auth-47--agent-memory-integrity-authority)). Without those fields there is no way to invalidate everything derived from one bad source, so the only recovery is discarding all memory — see [`agentic-failure-modes.md`](agentic-failure-modes.md) AGENT-06.

Two constraints follow:

- **Untrusted content does not become memory unaltered.** A fact learned from an untrusted source is stored with that provenance, and it never gains authority by being read back later. Laundering trust through persistence is the most common poisoning path.
- **Recovery is a tested procedure.** "Invalidate everything sourced from X between times T1 and T2" must be something the system can actually perform, with a test proving it.

---

## UDALI placement

| Concern | Owner |
|---------|-------|
| Context assembly and trust tagging | **Logician** band — **L15 (AI services)** |
| Trust classification on ingress | **Adapter** band — **L17 (middleware)** |
| Durable run state persistence | **L10 (repository)**, with schema owned at **L5 (entity)** |
| Memory provenance schema | **Designer** band — **L5** / **L7 (DTO)** |
| Staleness and supersession rules | **L13 (service)** |
| Drift and injection tests | **Integrator** band — **L21 (QA / verification)** |

Context assembly is a named module with an interface, not a string-building helper beside the prompt.

---

## Forbidden patterns

- **Append-only windows.** Prior context carried forward because it was there.
- **Untagged items.** Any context item with no trust classification — it defaults to trusted by omission.
- **Trust by source service.** Treating internal-store content as trusted when the text was user-authored.
- **Two live versions of one fact.** Staleness delegated to the model.
- **State in the transcript.** Budgets, plan progress, gate outcomes, or applied effects recoverable only by reading conversation text.
- **Model-writable counters.** Budget or progress state the model can alter by emitting text.
- **Provenance-free memory.** Persisted facts with no source, writer, or timestamp.
- **Laundered trust.** Untrusted content that becomes trusted by being written down and read back.
- **Summaries without lineage.** Compaction that loses what it was derived from.

---

## Evidence (what assurance expects)

| Claim | Evidence anchor |
|-------|-----------------|
| Assembly is decision-scoped | The assembly function, plus a test showing an item is dropped when no longer needed |
| Every item is tagged | Schema making the trust field non-nullable; test rejecting an untagged item |
| Untrusted content cannot instruct | Injection test: instruction-shaped text through a tool result and through an application record, asserting no behaviour change |
| User-authored internal data is untrusted | Test proving a user-written field read from the system's own store is tagged untrusted |
| Supersession works | Test where a corrected value replaces the original and the agent acts on the correction |
| Truncation is survivable | Test truncating the transcript and asserting the next step is still correct and bounded |
| Budgets are not model-writable | Test asserting emitted text cannot change a counter |
| Memory carries provenance | Schema with source, writer, timestamp, trust; test rejecting a write without them |
| Poisoning is recoverable | Test invalidating all memory from one source across a time range |

Per [AUTH-24](auth-catalog.md#auth-24--test--verification-authority), the test is the proof.

---

## Delivery lanes

| Lane | Use |
|------|-----|
| **Navigator** | Decide the assembly strategy and the state schema during scope. Retrofitting durable state after a transcript-dependent agent is working is a rewrite of the loop. |
| **Investigator** | Quality that degrades with run length, an agent acting on a corrected value, or work repeated after compaction are AGENT-05 signals — read the assembled context for the failing step before theorizing about the model. |
| **Sentinel** | Attest trust classification and state durability. Declare `HORI_AUTH` for the trust boundary and `HORI_REALM_BRIDGE` where context crosses an authority realm. |

---

## Public checklist

- [ ] Context is assembled per decision, not carried forward.
- [ ] Every item carries a trust tag; untagged is rejected, not defaulted.
- [ ] User-authored text is untrusted regardless of which store returned it.
- [ ] Untrusted content never enters the instruction channel.
- [ ] Superseded facts are replaced, not accompanied.
- [ ] Summaries record what they were derived from.
- [ ] Run identity, budgets, plan progress, gate outcomes, and applied effects live outside the transcript.
- [ ] The truncation test passes.
- [ ] Counters are not writable by model output.
- [ ] Persisted memory carries provenance, and invalidate-by-source is tested.

---

## Related

- [`agent-runtime-harness.md`](agent-runtime-harness.md) — the loop this feeds
- [`agent-tool-design.md`](agent-tool-design.md) — tool results as untrusted ingress
- [`agent-run-record.md`](agent-run-record.md) — the record, distinct from the state
- [`agentic-failure-modes.md`](agentic-failure-modes.md) — AGENT-04, AGENT-05, AGENT-06
- [`multi-agent-restraint.md`](multi-agent-restraint.md) — handoff between agents as a trust boundary

These documents do **not** constitute legal or regulatory certification. Formal gates are defined by **your** policies, tests, and CI.
