# Guidance–feedback seam

Normative short for **system-status feedback** in guided, multi-stage workflows: nudges, recommendations, coaching copy, readiness/stale signals, and enforcement gates. Companion: [Architecture principles](../docs/architecture-principles.md) §4–§5, [AUTH-aware delivery](../docs/auth-aware-delivery.md), [Sentinel audit programs](../docs/sentinel-audit-programs.md).

---

## Problem

Teams often rebuild “what state is the workflow in?” and “what should the user do next?” **per screen**—duplicate copy maps, client-derived severity, modals on transient errors, and advisory banners that contradict the real gate. That fragments trust and makes assurance impossible.

---

## One-line principle

In a guided multi-stage workflow, system-status feedback is **one server-authoritative seam** — **verdict → resolver → one affordance** — tone-graded from nudge to gate; presentation never derives severity, and the same affordance is reused on every surface.

Use these terms in reviews: **verdict**, **resolver**, **affordance**, **class**, **tone** — not interchangeable “banner,” “toast,” or “message” when naming the design.

---

## UDALI placement

| Concern | Owner |
|---------|--------|
| **Verdict** (severity, readiness, conflict) | **Logician** band — server truth, business/orchestration logic |
| **Presentation** (colour, tooltip, modal shell) | **Unifier** band — dumb renderer only |
| **Assurance** | **Sentinel** — declare horizontal seam **`guidance_feedback`** (finding id **`HORI_GUIDANCE_FEEDBACK`**) when this cross-cutting concern is in scope |

Optional finer slice: many organizations map verdict production to **L13** in the [22-layer model](../docs/udali-22-layer-model.md).

---

## Contract (normative)

1. **Verdict is server truth.** Severity, readiness, and conflict are produced by the server. The client **must not** compute or infer them.
2. **Exactly one resolver.** One mapping `verdict → { class, tone, tooltip, modal, cta }`. Legacy per-surface maps are **wrapped**, not duplicated. The **deletion test** must pass: removing a legacy map must not break callers.
3. **One affordance, every surface.** A single renderer: colour from severity, hover shows one server line, click opens one modal. No per-screen inline coaching that grows layout.
4. **`class` drives behaviour:**
   - **corrective** — modal + deep-link to the owning surface.
   - **refresh** — reload in place; **never** deep-link away from unsaved work.
   - **transient** — disable + `Retry-After`; **never** a modal (including rate limits / `429`).
5. **Gate owns the headline.** The enforcement gate owns the ready / not-ready headline; advisory tones **must not** contradict it on the same screen.

---

## Severity ladder (one system, four tones)

| Tone | Meaning | Must not |
|------|---------|----------|
| **nudge** | Low-stakes hint | Block, modal, or grow layout |
| **recommendation** | Suggested next action | Contradict the gate on the same screen |
| **guidance / coaching** | Explains how to become ready | Be re-implemented per surface |
| **gate / enforcement** | Blocks the invalid action | Be derived client-side |

---

## Architecture review questions

1. Where is the verdict produced — single server source, or is any surface re-deriving severity / readiness?
2. Is there exactly one resolver, and does the **deletion test** pass?
3. Is the affordance identical on every surface, or look-alike re-implementations?
4. Does `class` split corrective / refresh / transient correctly? Any modal for transient/`429`? Any deep-link that abandons unsaved work on refresh?
5. Can an advisory tone contradict the enforcement gate on the same screen?
6. Does presentation stay free of business logic (verdict producer vs. dumb renderer)?

---

## Forbidden patterns

- Parallel per-surface copy or severity maps  
- Client-side severity or readiness inference  
- Modal for a transient / `429` state  
- Deep-linking away from unsaved work on a **refresh** class  
- Advisory tone contradicting the gate on the same screen  

---

## Evidence (what assurance expects)

Before attesting readiness, bind proof in **your application repository** (not documentation alone):

| Claim | Evidence anchor |
|-------|-----------------|
| Single verdict producer | Path + test showing one server source; no surface re-derivation |
| One resolver | Inventory of legacy maps; deletion test result |
| One affordance | Shared component(s) per surface vs. look-alikes |
| Transient handling | `Retry-After` / disable behaviour — not a modal |
| AUTH on producer | Identity, authorization, validation, and audit posture on the **verdict producer** — presentation is not the enforcement home |

Sentinel passes should record **`HORI_GUIDANCE_FEEDBACK`** in the horizontal seam matrix when this concern is in scope. See [Sentinel audit programs](../docs/sentinel-audit-programs.md).

---

## Delivery lanes

| Lane | Use |
|------|-----|
| **Navigator** | Define the verdict → resolver → affordance contract **before** building per-screen UX ([Architecture before code](architecture-before-code.md)). |
| **Investigator** | Symptoms such as disagreeing screens, duplicate copy maps, or retry modals on `429` often indicate a broken seam—not a single UI bug. |
| **Sentinel** | Attest the horizontal seam with explicit proof or deferred risk. |

---

## Optional: hosted MCP

Teams using the **hosted AADM MCP** ([www.aadm.io](https://www.aadm.io), endpoint `https://mcp.aadm.io/mcp`) can load the same doctrine and assurance hints via tools documented in [Using the AADM MCP](../docs/mcp-quickstart.md). The **standard in this repository** remains authoritative for humans and policy; the MCP does not scan your repo or issue compliance scores.

---

## Public checklist

- [ ] Verdict producer named; presentation explicitly a renderer only.  
- [ ] One resolver; deletion test documented.  
- [ ] One affordance reused on every in-scope surface.  
- [ ] `class` rules respected (corrective / refresh / transient).  
- [ ] Gate vs. advisory contradiction ruled out for each screen.  
- [ ] Sentinel horizontal seam declared when assuring existing systems (`guidance_feedback` / `HORI_GUIDANCE_FEEDBACK`).
