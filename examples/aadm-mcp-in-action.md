# Example: AADM MCP in action — readiness framing, then bug fixing

This document is a **true story of one engineering thread**, retold for the **Examples** section of the AADM Standard. The product is called **HR App** throughout—it is a **pseudonym** for the real single-repo app where the work happened (authoring / Creator orchestration). Paths are slightly generalized (`client/`, `shared/contracts/`) but match how HR App is laid out.

**Audience:** Engineers and leads new to AADM MCP who need to see **what the MCP frames** versus **what only the application repo can prove**.

---

## Goal

The HR App team needed **one coherent story** for “what should the Creator hub do next?” without two implementations drifting apart:

- **Spec / orchestration readiness** — Prep state, structure compose, and topic projections must agree on gates before POSTs.
- **Block readiness** — Multiple failure modes were all surfacing as the same opaque `blocked` action; agents and consoles could not tell **scope unresolved** from **missing topic** from **structure gates** without reading prose.
- **Release / integration confidence** — Contract tests and hook guards should lock precedence rules (e.g. **generate before complete**, **apply before patch**).

The thread mixed **AADM-style framing** (layers, suggested validation) with **concrete HR App changes** (contracts, decider, hook wiring, tests).

---

## What AADM MCP is (one paragraph)

The AADM MCP provides **portable standard context and tools**—workflow framing (**Navigator** / **Investigator** / **Sentinel**), layer maps, delivery routing hints, audit scaffolds, and evidence-gated defect tooling—not a substitute for cloning CI into chat. It does **not** replace **HR App’s** tests or scripts. **Pass/fail** stays in the repo: Vitest contract suites, tiered `npm` scripts, and architecture guard tests. The MCP answers “what layer and what AUTH vocabulary apply?” and “what kind of evidence should we collect?”; HR App answers “did we ship correct behavior?”

---

## Phase 1 — Readiness framing via MCP

There is **no tool literally named “readiness.”** In this session, framing started like this (names reflect **reference** AADM MCP tool ids—see [MCP quickstart — Reference tool ids](../docs/mcp-quickstart.md#reference-aadm-enforcement-mcp-tool-ids); your operator may alias them):

1. **`standard_brief`** — Load the capability snapshot (three workflows: **Navigator** / **Investigator** / **Sentinel**, orchestrator hints, and structured follow-ons where published).
2. **`map_feature`** — Map the Creator/orchestration concern to delivery layers (where UI stops and contracts/services begin).
3. **`plan_delivery`** — Classify multi-role delivery work and boundary hints for the change (Navigator-shaped routing, not a defect troubleshooter).
4. **`audit_outline`** (as appropriate) — Scaffold readiness checks against bundled domains; optional **`fetch_auth`** / **`role_guide`** when AUTH or doctrine slices matter.
5. **Suggested validation commands** from the standard were **not** copy-pasted script names from HR App—the remind list mentioned generic checks (imports, validate tiers). On HR App the team mapped that intent to **whatever the repo actually exposes** (e.g. a **`go`** or **`ci`** aggregate script), instead of assuming a script name from another template exists locally.

**Takeaway:** MCP output was **heuristic and governance-oriented**. It did **not** lint HR App’s tree or flip CI green. This phase aligned with **Navigator** (delivery); **Sentinel** would apply if the primary ask had been “prove posture across seams,” and **Investigator** if the thread had been defect-first.

---

## Phase 2 — Making it real in HR App (this thread’s implementation)

Framing converged on a **design risk**: one pure function (`decide…NextAuthoringAction`) existed and was **only exercised from tests**, while **live navigation** still flowed from **prep-built next action** and **resolve unified next step**. Same gates “in principle,” **different code paths** → **drift**.

The thread implemented alignment work (names generalized):

1. **Shared contract** — Extended the **Creator next authoring action** contract (version bump): optional **`blocked_gate_code`** and **`blocked_context_codes`** so many “blocked” branches stay distinguishable for agents/UI; documented **apply wins over patch staleness** as explicit precedence; added **`course_authoring_lane_complete`** input so **hub-only** mode can return **`complete`** after generate is ruled out—not only topic-scoped aggregate completion.
2. **Decider** — Populated gate codes on every blocked outcome; ordered tail so **generate** precedes **complete**; **missing target interaction** → **blocked** with a dedicated gate code (not masked as complete).
3. **Input builder** — New helper assembling inputs from **orchestration prep**, **structure-compose-control** (preview staleness, blocking reasons, step key merge), **authoring-status projection** fetch state, and **scope guard** alignment with generate POSTs.
4. **Hook** — Main Creator orchestration hook now calls **structure compose** query, builds input, runs the decider, and exposes **`creatorNextAuthoringAction`** alongside existing prep-driven UI. **Topic aggregate complete** is passed **only** when the **effective generate target** matches the **Creator-scoped topic** used for detail/read models—so “complete” does not lie when scope and projection disagree.
5. **Tests** — Table-driven decider tests updated for gate codes and a **hub complete** case; **boundary guard** updated so the hook is **allowed** to import the decider (Forge cross-layer pages still must not).

This is the **executable** half: MCP suggested *what kind of rigor*; HR App added *contracts + tests + wiring*.

---

## Phase 3 — “Bug fixing” in this story (design defects, not one typo)

No single production incident was pasted—but the thread treated **ambiguous contracts and dual paths** as **defect class**:

| Symptom | What was wrong |
|--------|----------------|
| **`complete` vs `generate`** | Tail precedence could imply “done” when generate should still run, or when **target id** was missing—wrong user mental model. |
| **Hub never `complete`** | `complete` was effectively **topic-scoped only**; course-level lane completion from prep was not wired into the same decision function—easy to forget when server adds aggregate flags. |
| **Everything is `blocked`** | Scope, hierarchy, authoring-step blocked, missing target—all collapsed to one action label **without a machine reason**—bad for agents and for consoles that need stable routing. |
| **Drift** | Tests used one brain; runtime navigation used another—**logic bugs by omission** (two truths). |

**Fix patterns (what HR App shipped conceptually):**

- **Single precedence chain** in one place for **POST/orchestration** ordering; document **apply before patch** in the contract and lock it with tests.
- **Strict gate typing** — `blocked_gate_code` + optional context echoes from prep/structure.
- **Scope alignment** for aggregate completion vs generate target.
- **Production wiring** — Hook subscribes to **structure-compose-control** so staleness is **server-backed**, not guessed.

**`debug_defect`** (Investigator track) in a similar defect-shaped session would carry **boundary hints** (orchestration versus Forge human surfaces)—it **does not** edit files; fixes stay in HR App.

---

> **WHAT THIS MEANS**  
> When troubleshooter-style tools emit a **human-readable closing section**, treat it as **routing translated for leads** (“UI vs contract vs service”). **Structured payloads** (layers, AUTH, suggested commands) remain the machine-facing truth. Same split applies inside HR App: **`creatorNextAuthoringAction`** is for **orchestration/agent precedence**; human checklist strings from prep may still differ until product **explicitly merges** those narratives.

---

## Lessons learned (from this thread)

- **MCP first** → layers, AUTH, delivery routing hints, and *classes* of validation—not repo-specific pass/fail.
- **Repo second** → script names and tiers are **local**; map suggestions to **`go` / `ci` / contract tests** that actually exist.
- **Dual code paths** are a **bug farm**; converge or document “navigation vs orchestration decision” until one wins.
- **Collapsed enums** (`blocked` without codes) hurt **agents**; extend contracts **before** UX debt spreads.
- **Explicit precedence** (**apply vs patch**, **generate vs complete**) belongs in **shared contracts + tests**, not only in chat.

---

## Try it yourself — minimal checklist

1. Enable **AADM MCP**, call **`standard_brief`**, then **`map_feature`** / **`plan_delivery`** for Navigator-shaped delivery work—or pick **`route_lane`** if your orchestrator already fixed **`intent`** (`delivery` / `defect` / `assurance`).
2. Map suggested checks to **your** `package.json` scripts and **Vitest** contract paths—do not assume template script names exist.
3. If behavior spans **UI + prep + compose + projections** and the narrative is **defect-shaped**, paste **path:line** or a **failing test name** into **`debug_defect`** for evidence-gated troubleshooting hints—not **`Sentinel`**, which is for assurance posture across seams.
4. In **HR App**, run your aggregate **`go`/`ci`** or targeted **`vitest`** on the contracts and hook guards you touched.

For **[UDALI](../docs/udali-personas.md) personas**, the optional **[L1–L22 slice](../docs/udali-22-layer-model.md)**, and **AUTH** definitions, see the **AADM Standard** repository (canonical URL in that repo’s root README).
