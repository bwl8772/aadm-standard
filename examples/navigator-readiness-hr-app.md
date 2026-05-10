# Example: Navigator-shaped readiness, then implementation (HR App)

This document is a **true story of one engineering thread**, retold for the **Examples** section of the AADM Standard. The product is called **HR App** throughout—it is a **pseudonym** for the real single-repo app where the work happened (authoring / Creator orchestration). Paths are slightly generalized (`client/`, `shared/contracts/`) but match how HR App is laid out.

**Audience:** Engineers who want to see **standard framing** (delivery lane, layers, contracts, tests) versus **what only the application repo can prove**.

---

## Goal

The HR App team needed **one coherent story** for “what should the Creator hub do next?” without two implementations drifting apart:

- **Spec / orchestration readiness** — Prep state, structure compose, and topic projections must agree on gates before POSTs.  
- **Block readiness** — Multiple failure modes were all surfacing as the same opaque `blocked` action; consoles could not tell **scope unresolved** from **missing topic** from **structure gates** without reading prose.  
- **Release / integration confidence** — Contract tests and hook guards should lock precedence rules (e.g. **generate before complete**, **apply before patch**).

The thread mixed **AADM-style framing** (layers, validation discipline) with **concrete HR App changes** (contracts, decider, hook wiring, tests).

---

## Phase 1 — Readiness framing (Navigator lane)

There is **no single template named “readiness.”** In practice the team:

1. **Aligned on delivery posture** — Treated the thread as **Navigator** (planned change): scope, verification plan, release readiness—not a single defect thread (**Investigator**) and not a broad assurance audit (**Sentinel**). See [Glossary — delivery lanes](../docs/glossary.md).  
2. **Mapped the feature** — Attached a **layer map** fragment to the initiative: where UI stops, where contracts live, where orchestration decisions belong ([UDALI](../docs/udali-personas.md)).  
3. **Recorded build intent** — BIS-lite sections captured **Interfaces** (Creator next-action contract) and **Seams** between prep, structure-compose, and projections.  
4. **Planned verification** — Listed **contract tests** and aggregate CI scripts the repo actually exposes (local names—not assumed from other projects).

**Takeaway:** Standard materials are **heuristic and governance-oriented**. They do **not** lint HR App’s tree or flip CI green. This phase aligned **people and tickets** before code converged.

---

## Phase 2 — Making it real in HR App

Framing converged on a **design risk**: one pure function (`decide…NextAuthoringAction`) existed and was **only exercised from tests**, while **live navigation** still flowed from **prep-built next action** and **resolve unified next step**. Same gates “in principle,” **different code paths** → **drift**.

The thread implemented alignment work (names generalized):

1. **Shared contract** — Extended the **Creator next authoring action** contract (version bump): optional **`blocked_gate_code`** and **`blocked_context_codes`** so many “blocked” branches stay distinguishable; documented **apply wins over patch staleness** as explicit precedence; added **`course_authoring_lane_complete`** input so **hub-only** mode can return **`complete`** after generate is ruled out—not only topic-scoped aggregate completion.  
2. **Decider** — Populated gate codes on every blocked outcome; ordered tail so **generate** precedes **complete**; **missing target interaction** → **blocked** with a dedicated gate code.  
3. **Input builder** — New helper assembling inputs from **orchestration prep**, **structure-compose-control**, **authoring-status projection**, and **scope guard** alignment with generate POSTs.  
4. **Hook** — Main Creator orchestration hook now calls **structure compose** query, builds input, runs the decider, and exposes **`creatorNextAuthoringAction`**. **Topic aggregate complete** is passed **only** when the **effective generate target** matches the **Creator-scoped topic** used for detail/read models.  
5. **Tests** — Table-driven decider tests updated for gate codes and a **hub complete** case; **boundary guard** updated so the hook is **allowed** to import the decider.

This is the **executable** half: templates suggested *what kind of rigor*; HR App added *contracts + tests + wiring*.

---

## Phase 3 — Defect-shaped elements (design defects, not one typo)

No single production incident was pasted—but the thread treated **ambiguous contracts and dual paths** as **defect class**:

| Symptom | What was wrong |
|--------|----------------|
| **`complete` vs `generate`** | Tail precedence could imply “done” when generate should still run, or when **target id** was missing. |
| **Hub never `complete`** | `complete` was effectively **topic-scoped only**; course-level lane completion from prep was not wired into the same decision function. |
| **Everything is `blocked`** | Multiple causes collapsed to one label **without a machine reason**. |
| **Drift** | Tests used one brain; runtime navigation used another. |

**Fix patterns shipped:**

- **Single precedence chain** in one place for POST/orchestration ordering; document **apply before patch** in the contract and lock it with tests.  
- **Strict gate typing** — `blocked_gate_code` + optional context echoes from prep/structure.  
- **Scope alignment** for aggregate completion vs generate target.  
- **Production wiring** — Hook subscribes to **structure-compose-control** so staleness is **server-backed**.

---

## Lessons learned

- **Framing first** → layers, AUTH themes, and *classes* of validation—not repo-specific pass/fail.  
- **Repo second** → script names and tiers are **local**; map suggestions to **`go` / `ci` / contract tests** that actually exist.  
- **Dual code paths** are a **bug farm**; converge or document until one wins.  
- **Collapsed enums** (`blocked` without codes) hurt operators and assistants; extend contracts **before** UX debt spreads.  
- **Explicit precedence** belongs in **shared contracts + tests**, not only in chat.

---

## Try it yourself — minimal checklist

1. Label the initiative **Navigator** vs **Investigator** vs **Sentinel** using [Glossary](../docs/glossary.md).  
2. Complete **BIS-lite** + **layer map** for the change.  
3. Map validation intent to **your** `package.json` scripts and contract tests—do not assume template script names exist.  
4. Run aggregate **`go`/`ci`** or targeted **`vitest`** (or equivalent) on the contracts and guards you touched.

For **[UDALI](../docs/udali-personas.md)** and optional **[L1–L22](../docs/udali-22-layer-model.md)**, see the standard docs index.
