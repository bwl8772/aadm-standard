# Business logic placement — frontend vs backend

> Where decisions and rules live (browser vs server) — consistent with UDALI ownership and topology (SPA, SSR, BFF, mobile + web). Stack-agnostic.

## Default under UDALI

- **Domain meaning** (what entities are, invariants, lifecycle meaning) — **Designer band (L4–L8)**, especially domain meaning in L6.
- **Application business decisions and orchestration** ("what we do next," policies that allocate outcomes) — **Logician band (L13–L16)** — services decide; repositories do not.
- **Presentation correctness, UX state, UI-safe guards** — **Unifier band (L1–L3)** — show/hide, loading/error affordances, client-side validation that mirrors server rules for UX only.

**Rule of thumb.** If two clients (web, mobile, partner API) must agree on an outcome, or authority depends on it, the backend (through the API and domain/service layers) must own the rule. The frontend may preview or derive display — not define truth.

---

## Prefer backend (or shared domain/service layer) when any are true

| Signal | Why |
|--------|-----|
| **Authorization or tenancy** | Who may act is never defined only in the browser. |
| **Money, inventory, commitments, legal timing** | Correctness and audit trail live server-side. |
| **Single source of truth across channels** | Web + mobile + jobs + integrations must not diverge. |
| **Concurrency or races** | "Last write wins" in local state is not a policy engine. |
| **Compliance, audit, or dispute** | Server-enforced, logged outcomes are required. |
| **Same rule currently duplicated** in several UI entry points | Extract to API + domain/service. See [`shared-backend-separate-pages.md`](shared-backend-separate-pages.md). |
| **Sensitive data minimization** | Business rules should not require shipping secrets or full graphs to the client. |

---

## Frontend logic is appropriate when all are safely true

These are not substitutes for server authority — they optimize experience and latency within a contract the server already defines.

| Kind | Examples |
|------|----------|
| **Pure presentation** | Formatting, sorting for display, animations, layout branching. |
| **UX derivation from server facts** | Enabled/disabled buttons from flags returned by API; stepper state from explicit server-provided state machine fields. |
| **Optimistic UI** | Assume success only if you reconcile or roll back when server rejects — server wins. |
| **Client-only flows** | Drafts that never imply commitment until submit; wizards that collect input then POST once. |
| **Performance** | Debounced search against your backend API — not "business policy" invented client-side. |
| **Offline / flaky networks** | Queue actions, replay through server validation; treat client store as tentative. |

**Guardrail.** Client validation duplicates server rules for faster feedback — it does not replace server enforcement.

---

## Map to topology

| Topology | Frontend-heavy logic risk | Backend emphasis |
|----------|----------------------------|------------------|
| **SPA + REST/GraphQL API** | Rules creep into hooks/stores → drift vs mobile. | Policies and invariants on server; DTOs/contracts explicit. |
| **SSR / full-stack framework** | "Easy" to put decisions in route handlers or server components — still separate UI from domain/service per UDALI. | Keep decisions in service/domain modules; UI renders outcomes. |
| **BFF (backend-for-frontend)** | BFF aggregates — still not the ultimate authority if core domain lives deeper. | BFF shapes payloads; domain rules stay in core services. |
| **Mobile + Web** | Duplicate "business" in Swift/Kotlin and TS. | Shared API contract + server-owned rules. |
| **Edge / serverless handlers** | Thin wrappers still call domain-owned modules — avoid mega handlers. | Extract testable domain/service units. |

---

## UDALI placement cheat sheet

| Concern | Typical band |
|---------|--------------|
| UI routing, visibility, component state | **U** L1–L3 |
| Schema, entity, DTO shape, domain meaning | **D** L4–L8 |
| Persistence, auth enforcement at data boundary | **A** L9–L12 |
| Orchestration, policies, domain workflows | **L** L13–L16 |
| HTTP routes, middleware wiring | **I** L17–L22 |

If "business logic" appears in U or I because it was convenient, **hand off** to L or D.

---

## Decision checklist

Answer before recommending "do it in the frontend":

1. **Would another client disagree if only the browser enforced this?** → Backend.
2. **Does security or money depend on it?** → Backend.
3. **Is it only how we show server-returned facts?** → Frontend OK.
4. **Are we duplicating the third copy of the same rule?** → Consolidate backend (+ thin UI mirror).
5. **After submit, could two users see incompatible truths?** → Backend coordination.

---

## Closing

Architecture-specific does not mean UDALI-optional. Your topology changes latency and packaging, not who owns authority and truth. **Server owns decisions; client owns presentation.** Duplicate rules in the UI only for UX, never only for correctness.

---

## Related

- [`shared-backend-separate-pages.md`](shared-backend-separate-pages.md) — multiple screens, one workflow spine.
- [`operational-design-heuristics.md`](operational-design-heuristics.md) — repeated bugs and rule duplication.
- [`udali-auth-enforcement-mapping.md`](udali-auth-enforcement-mapping.md) — where each AUTH lives.
- [UDALI personas](../docs/udali-personas.md) — five collaboration bands.
