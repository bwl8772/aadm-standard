# Example: Evidence-first resolution of a multi-layer “save blocked” defect

**Sanitized case study.** Names, modules, and identifiers are invented; the **pattern** (degraded read path + client omitting normalization + strict validation before normalization on write) is grounded in a real class of production incidents. Timeline language (“hours vs weeks”) is **illustrative**, not a benchmark promise.

---

## Minimal product context (only what matters)

Imagine a **web authoring product**: users fill in a **structured blueprint** for a deliverable, save it, then move through downstream **build** and **publish** stages. If saving the blueprint fails, the whole pipeline stalls.

---

## Presenting symptom

- Users see a generic **“Save couldn’t complete”** message.  
- The API returns **HTTP 400** with a body-validation error on **PATCH** (update blueprint).  
- Logs show the validator rejecting **legacy literal values** in fields that the **current** contract defines as a **closed enum**.

Surface symptom: **strict validation rejects the payload**. Deeper question: **why are those literals still in flight?**

---

## What was actually broken (three reinforcing layers)

| Layer | Generic failure mode |
|--------|---------------------|
| **Read path (server)** | A **read-model** endpoint assembles a large DTO. Inner paths may **normalize** nested data. If **outer** validation fails and the stack **degrades with warnings**, the handler can return **raw** assembled state—legacy literals reach the browser. |
| **Client** | A **newer** authoring shell seeds editable state from that payload. On save it emits **memory** without the same **normalize → parse** guard an older form had. |
| **Write path (server)** | The **PATCH** handler runs **schema validation first**. Normalization might exist later—but **validation fails earlier**. |

---

## How AADM-shaped discipline helped

The team did **not** treat this as a single-line UI fix. Below is how **standard vocabulary and Investigator-style sequencing** contributed—without pretending documentation edited files or ran tests.

### 1. Evidence before architecture guesses (**Investigator** lane)

The thread stayed in **recon-first** posture until anchors existed: **route + status**, **handler location**, **validator issue paths**, **sample failing payload**. That pushed work away from “try random coercion in the UI” toward **instrumentation and trace**.

When logs showed **exact enum mismatch paths**, diagnosis stayed scoped: contract choke point vs client state vs read-model degradation—not three undifferentiated guesses.

### 2. Layer and ownership hints ([UDALI](../../docs/udali-personas.md))

Naming **presentation**, **application boundary**, and **validation ordering** reduced the risk of a **single mega-change** that rewires read models, clients, and gateways at once. Fixes could land close together while the **narrative** stayed: “boundary normalization,” “client inference guard,” “transport/schema ordering.”

### 3. AUTH context where approvals and readiness matter

When **UI copy** and **server readiness flags** disagreed, framing the bug as **cross-layer invariant drift** (not only cosmetic copy) matched [AUTH-aware delivery](../../docs/auth-aware-delivery.md) themes.

### 4. Hold points as sequencing, not magic

Strong evidence sometimes warrants an explicit **pause** until ownership is clear (who fixes read-model policy vs API vs client). The value is **reducing silent scope creep**, not deferring obvious fixes forever.

### 5. Long threads need written state

For multi-session investigations, maintain a **short evidence log** in the ticket—routes, payloads, test names—so deep diagnosis does not “forget” earlier proofs when discussion contexts reset.

---

## What the team shipped (still generic)

- **Server write path:** Normalize or coerce legacy fields **before** strict validation on PATCH, with explicit policy.  
- **Client:** Normalize on save; do not send doomed requests.  
- **Observability:** Promote **warning-only** failures in critical projections to **structured errors**.  
- **Automated journeys:** Update **stable selectors** when components rename affordances.

---

## Honest boundary

- **Standard docs did not** apply patches or prove CI green.  
- **They did** reinforce **evidence before diagnosis**, **layer-aware scoping**, and **AUTH-aware framing**.

---

## Where to go next

- Navigator-shaped readiness thread: [navigator-readiness-hr-app.md](navigator-readiness-hr-app.md)  
- [Glossary — Investigator lane](../../docs/glossary.md)  
- [Repository scope](../../docs/repository-scope.md)  
