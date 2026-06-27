# Engineering doctrine — top ten (priority order)

> A single ranked list (1 = apply first) so agents and humans factor the same spine into planning, architecture, and reviews. **Priority is not UDALI layer numbers** — it is damage-prevention order. Stack-agnostic.

**How to use.** Skim 1–4 before large work; lean on 5–7 during design; use 8–10 for hardening and assurance.

**Plain-language spine.** [`universal-engineering-truths.md`](universal-engineering-truths.md) — twenty truths that apply regardless of stack or tenure; use alongside this ranked list.

---

## 1 — Evidence before refactor or fix

**Doctrine.** Do not expand code, tests, timeouts, or scope until defect-shaped work is evidence-backed and properly classified. Recon comes before diagnosis; execution-control rules apply.

**Why first.** Wrong fixes and duplicate "platforms" usually start here — symptom-driven edits without proof.

**Where to look.** [`three-lanes.md`](three-lanes.md) Investigator stages, [`ai-coder-hard-stops.md`](ai-coder-hard-stops.md).

---

## 2 — Correctness lives in the application repo

**Doctrine.** Pass or fail for behavior is owned by your automated tests and CI — not by AADM narrative, checklists, or keyword mappers.

**Why.** Prevents "compliance theater" and arguing from documents instead of runnable proof.

**Where to look.** [AUTH-24 (Verification)](auth-catalog.md#auth-24--test--verification-authority), [`GOVERNANCE.md`](../GOVERNANCE.md), [`CONFORMANCE.md`](../CONFORMANCE.md).

---

## 3 — Intent routing (delivery vs defect vs assurance)

**Doctrine.** Classify the primary question before picking tools: planned delivery, broken-vs-expected, or prove-alignment assurance. Do not use Navigator to skip Investigator when the narrative is defect-shaped.

**Why.** Wrong lane wastes time and violates gates (open-ended help instead of evidence gathering).

**Where to look.** [`three-lanes.md`](three-lanes.md).

---

## 4 — Scope and human gates

**Doctrine.** Explicit in-scope / out-of-scope, checkpoints before implementation balloons, proportional depth — especially for Navigator (Scope → Verify → Readiness) and Sentinel (Scope & frame → Trace & bind → Attest).

**Why.** Scope creep is the default failure mode of multi-agent and long chats.

**Where to look.** [`three-lanes.md`](three-lanes.md), [`universal-coding-principles.md`](universal-coding-principles.md).

---

## 5 — One spine, many surfaces (shared backend, separate pages)

**Doctrine.** When several UIs are different user jobs but one business process, merge logic underneath — shared services, workflow state, permissions — do not duplicate per-page mini-platforms.

**Why.** "If I change the rule on page A, must I change page B?" — when yes, you owe a shared spine.

**Where to look.** [`shared-backend-separate-pages.md`](shared-backend-separate-pages.md).

---

## 6 — Boundary ownership (UDALI)

**Doctrine.** Each band owns specific concerns; hand off across boundaries instead of smuggling UI logic into services or DB rules into controllers. Load minimal slices per task.

**Why.** Layer leakage is expensive to unwind and breaks reviewability.

**Where to look.** [UDALI personas](../docs/udali-personas.md), [UDALI 22-layer model](../docs/udali-22-layer-model.md), [`udali-auth-enforcement-mapping.md`](udali-auth-enforcement-mapping.md).

---

## 7 — Contracts, meaning, and traceability

**Doctrine.** Data meaning and boundaries are explicit (schemas, DTOs, invariants, compatibility). Drift across seams is tracked with discipline — not tribal knowledge.

**Why.** Silent contract breakage outruns feature velocity.

**Where to look.** [AUTH-21 (DTO/Contract Boundary)](auth-catalog.md#auth-21--dto--contract-boundary-authority), [`trace-catalog.md`](trace-catalog.md), [`seam-catalog.md`](seam-catalog.md).

---

## 8 — Security and authority posture by design

**Doctrine.** Authorization, tenancy, secrets, and sensitive flows are inputs to design — not glue added after merge.

**Why.** Retrofitted security is brittle and expensive.

**Where to look.** [`auth-catalog.md`](auth-catalog.md) (full catalog), Security & Tenancy Authority (AUTH-16), Security & Policy Authority (AUTH-32..34).

---

## 9 — Observability and failure UX

**Doctrine.** Failures must be visible, attributable, and actionable across seams — agents and humans need enough signal to continue without guessing.

**Why.** Underspecified errors recreate the same defect threads forever.

**Where to look.** [AUTH-29 (Audit Log)](auth-catalog.md#auth-29--audit-log-authority), [AUTH-30 (Traceability)](auth-catalog.md#auth-30--traceability-authority), [AUTH-31 (Decision Logging)](auth-catalog.md#auth-31--decision-logging-authority).

---

## 10 — Assurance vs delivery (proof packs, not generic scores)

**Doctrine.** Sentinel proves alignment with the standard using scoped evidence — not generic "AI compliance scores" from a server. Delivery ships features; Investigator fixes defects; assurance binds proof to seams.

**Why.** Prevents audits that produce slides but no binding checks.

**Where to look.** [`sentinel-program.md`](sentinel-program.md), [`three-lanes.md`](three-lanes.md).

---

## Quick matrix

| # | Doctrine | Primary discipline |
|---|----------|--------------------|
| 1 | Evidence before fix | Investigator Stage 1 (anchors before classification) |
| 2 | Repo owns correctness | AUTH-24; tests and CI |
| 3 | Route intent | Three-lane separation |
| 4 | Scope and gates | Navigator/Sentinel stage gates |
| 5 | Shared spine, separate UI | Shared backend, separate pages |
| 6 | UDALI placement | Layer ↔ AUTH enforcement mapping |
| 7 | Contracts / trace | AUTH-21, TRACE, SEAM |
| 8 | Security by design | AUTH catalog |
| 9 | Observable failures | Audit/Trace/Decision logging |
| 10 | Assurance discipline | Sentinel program, evidence model |

---

## Related

- [`universal-engineering-truths.md`](universal-engineering-truths.md) — concise coaching truths (twenty).
- [`ai-coder-hard-stops.md`](ai-coder-hard-stops.md) — actions that never appear on this list because they should never be taken.
- [`module-interface-seam-vocabulary.md`](module-interface-seam-vocabulary.md) — architecture-review language.
- [`operational-design-heuristics.md`](operational-design-heuristics.md) — singletons and repetition signals.
- [`business-logic-placement.md`](business-logic-placement.md) — where decisions belong.

---

## Closing

When team standards conflict with this index, written team standards win — **after you name the delta.** This document is a compass, not a substitute for repository proof.
