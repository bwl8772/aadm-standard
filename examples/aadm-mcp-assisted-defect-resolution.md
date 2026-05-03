# Example: AADM MCP assisting a multi-layer “save blocked” defect

**Sanitized case study.** Names, modules, and identifiers are invented; the **pattern** (degraded read path + client omitting normalization + strict validation before normalization on write) is grounded in a real class of production incidents. Timeline language (“hours vs weeks”) is **illustrative**, not a benchmark promise.

---

## Minimal product context (only what matters)

Imagine a **web authoring product**: users fill in a **structured blueprint** for a deliverable, save it, then move through downstream **build** and **publish** stages. If saving the blueprint fails, the whole pipeline stalls—nothing durable to build from, flags stay red, shipping stops.

---

## Presenting symptom

- Users see a generic **“Save couldn’t complete”** message.
- The API returns **HTTP 400** with a body-validation error on **PATCH** (update blueprint).
- Browser devtools and server logs eventually show the validator rejecting **legacy literal values** in fields that the **current** contract defines as a **closed enum** (e.g. old lowercase tokens no longer in the allowed set).

Surface symptom: **strict validation rejects the payload**. Deeper question: **why are those literals still in flight?**

---

## What was actually broken (three reinforcing layers)

Three separate issues stacked; **any one** could have caused pain; **all three** made “save” reliably fail.

| Layer | Generic failure mode |
|--------|----------------------|
| **Read path (server)** | A **read-model** endpoint assembles a large DTO. Inner services may **normalize** a nested blueprint object. If **outer** response validation fails and the stack is configured to **degrade with warnings** instead of failing closed, the handler can return the **raw** assembled object. Legacy literals in nested fields can then reach the browser even though the “happy path” would have stripped them. |
| **Client** | A **newer** authoring shell seeds editable state from that payload. On save it emits **whatever is in memory** without running the same **normalize → parse** guard that an older form had. Assumption: “the server always sends canonical data”—true until degradation bypasses stripping. |
| **Write path (server)** | The **PATCH** handler runs **schema validation first**. The persistence layer might normalize legacy tokens later—but **validation fails earlier**, so normalization never runs. |

---

## How AADM MCP helped

The team had the **AADM Standard Enforcement MCP** available in the IDE. Below is how it **contributed**, stated plainly—without pretending the MCP edited files or ran tests.

### 1. Evidence discipline (Path A → Path B)

Early calls to the troubleshooter surface (**`debug_defect`** on current servers; older deployments used other names) stayed in **recon-first** posture until anchors existed: **route + status**, **handler location**, **validator issue paths**, **sample failing payload**. That pushed the session away from “try random coercion in the UI” toward **instrumentation and trace**.

When logs finally showed **exact enum mismatch paths**, the tool could move to **structured diagnosis** (Path B–style): contract choke point vs client state vs read-model degradation—not three guesses, but a **labeled stack**.

### 2. Layer and ownership hints ([UDALI](../../docs/udali-personas.md)-shaped; optional [L1–L22](../../docs/udali-22-layer-model.md))

The MCP does not replace architecture reviews, but its outputs **name bands/layers** (e.g. presentation coherence vs application boundary vs contract validation ordering). That reduced the risk of a **single mega-change** that rewires read models, clients, and gateways in one undifferentiated patch. Even when fixes landed in close succession, the **narrative** stayed: “boundary normalization,” “client inference guard,” “transport/schema ordering.”

### 3. AUTH context where approvals and readiness matter

When **UI copy** and **server readiness flags** disagreed (e.g. “reviewed” vs “not ready to build”), pulling **`fetch_auth`** for relevant controls (e.g. approval / readiness authority patterns) helped frame the bug as **cross-layer invariant drift**, not only a cosmetic label bug.

### 4. `haltBeforeFix` / HOLD as orchestration, not a compiler

On strong evidence, the troubleshooter may return **guardrails** such as **hold before implementation**—meaning: **pause** until **ownership and sequencing** are explicit (who fixes degraded read-model policy vs API vs client). In fast sessions, humans or assistants sometimes **implement anyway** when the technical closure is obvious; the value of HOLD is **reducing silent scope creep**, not physically blocking an editor. In this pattern, the important clarification is: **AADM shaped sequencing intent** even when keystrokes didn’t wait for a formal sign-off inside the tool.

### 5. Continuations across a long thread

Using **`append_evidence`**, **`completed_path_a_steps`**, and periodic **`orchestrator_anchor`** / **`standard_brief`** reminders (see [MCP quickstart](../docs/mcp-quickstart.md)) keeps **structured defect state** from evaporating when chat context compacts—so Path B doesn’t “forget” earlier proofs.

---

## What the team shipped (still generic)

No proprietary snippets—only the **shape** of the remediation:

- **Server write path:** **Normalize or coerce legacy blueprint fields before strict validation** on PATCH, with explicit policy (e.g. upgrade silently at the boundary vs reject). Keeps strict enums without abandoning users on old tabs.
- **Client:** **Normalize on save** (same pipeline as the legacy form): if the object is unsalvageable, **do not send** a doomed request.
- **Observability:** Promote **silent warning-only** failures in critical projections to **structured errors** so “blank UI” becomes diagnosable.
- **Automated journey:** Update **stable selectors** when authoring components rename affordances—otherwise end-to-end flows stall without an obvious assertion failure.

---

## Honest boundary

- **AADM MCP did not** apply patches, migrate data, or prove CI green.
- **It did** reinforce **evidence before diagnosis**, **layer-aware scoping**, and **governance prompts** (AUTH, guardrails) that match how senior engineers already think—especially under time pressure.

---

## Where to go next

- Navigator / readiness framing: [aadm-mcp-in-action.md](aadm-mcp-in-action.md)
- Connecting a client: [MCP quickstart](../docs/mcp-quickstart.md)
- Example chat prompts → tools: same quickstart, section **Example asks**
