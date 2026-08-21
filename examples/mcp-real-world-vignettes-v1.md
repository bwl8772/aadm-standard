# AADM MCP — real-world vignettes (May–June 2026)

**Purpose:** Public-site-ready stories from engineering work with the hosted **AADM MCP** (`user-aadm` in Cursor / Claude Code). App names are **fictional**; patterns (lanes, seams, attestation, evidence-first) reflect real agent sessions.

**Period covered:** roughly **2026-05-28 → 2026-06-27**.

**Hour estimates:** Illustrative **engineering + review time avoided or redirected** — not guaranteed SLAs. Method: compare typical path (meetings, wrong fixes, full CI, parallel UI) vs attested path documented in session. Ranges assume a senior engineer + agent pair.

**Related examples:** [evidence-first-defect-resolution.md](evidence-first-defect-resolution.md) · [navigator-readiness-hr-app.md](navigator-readiness-hr-app.md) · [ai-task-assistant/](ai-task-assistant/)

---

# Part I — Ten vignettes

---

## 1. StudioPublish — export sub-page sends users to the wrong parent

**App · industry:** StudioPublish · digital media / content ops

### The problem
Producers opened `/exports/assets?clipId=…` from three entry points (campaign overview, QC queue, asset library). One **Back** button always returned to the exports hub — so users who came from QC landed in the wrong place **~40% of sessions** (support tagged “lost navigation”). Engineering proposed adding a fourth route-specific button without a shared contract.

### How AADM MCP helped
| Tool | What it did |
|------|-------------|
| `ux_surface_pass` | Translated product ask into two explicit exit intents: **return to origin** vs **return to exports hub** — blocked a third mystery “home” button. |
| `user_flow` | Modeled drill-in → exit-origin → exit-hub as attested steps with state prerequisites (`requires_states` / `creates_state`). |
| `pressure_test` | Named Vitest-RTL as proof stack and flagged missing tests for header-left vs header-right affordances before merge. |

### Hours saved (estimate)
**12–18 hours** — avoided 1 design review cycle (~4h), 1 wrong implementation pass (~6h), and 2h of QA repro scripts for “where did Back go?”

**Tools:** `ux_surface_pass` · `user_flow` · `pressure_test`

---

## 2. Vertex Learning — four LMS integrations, zero gap inventory

**App · industry:** Vertex Learning · edtech / course delivery

### The problem
Product committed to dispatch → poll → store → preview → regenerate → annotate → edit source → re-dispatch across a slide API, voice API, video API, and an LMS. Engineering had **partial** async job code and **no** operator view of failures. PM had 14 scattered tickets; nobody could answer “what’s missing for GA?”

### How AADM MCP helped
| Tool | What it did |
|------|-------------|
| `ux_surface_pass` | Captured the **10-step author loop** in one surface narrative — forced async + version history + notes persistence to stay in scope. |
| `signal_stack_outline` | Mapped Integrator-band stack: entry → auth → dispatch → poll → asset store → sub-page — tagged **proven vs latent** per layer. |
| `journey_attest` | Recorded operator monitoring as **latent** (read-only list existed; mutate/retry gaps explicit) — prevented “we’re done” claim on half-wired monitoring. |

### Hours saved (estimate)
**20–30 hours** — replaced ~8h of cross-team workshops, ~10h of engineer-written gap spreadsheets, and ~6h of duplicate discovery when platform teams asked the same questions separately

**Tools:** `ux_surface_pass` · `signal_stack_outline` · `journey_attest`

---

## 3. Northstar Retail — 4,000-line merchant admin page

**App · industry:** Northstar Retail · retail / multi-tenant SaaS

### The problem
One React page (~4k LOC) held merchants, billing, and support tools with **shared `?merchant=` URL state** and **10 mutations invalidating each other’s caches**. A proposed “split into three files in one folder” would have duplicated hook logic and broken purge/delete auth boundaries.

### How AADM MCP helped
| Tool | What it did |
|------|-------------|
| `route_lane` | Routed to **Navigator** (delivery), not Investigator — scoped refactor vs bug hunt. |
| `plan_delivery` | Sequenced **hook attribution audit before route split** (Pass 2 blocked until ~10 useState / ~8 useQuery ownership mapped). |
| `fetch_auth` | Pulled **AUTH-16** (tenancy) and **AUTH-21** (contract boundary) into the slice plan before purge/delete UI. |
| `architecture_lens` | Pressure-tested **purge vs archive** as single service brain — rejected parallel wipe scripts. |

### Hours saved (estimate)
**30–45 hours** — avoided ~2 days of silent cross-deck regressions, ~1 day of auth review rework, and ~4h of “why did cache invalidate wrong deck?” debugging post-merge

**Tools:** `route_lane` · `plan_delivery` · `fetch_auth` · `architecture_lens`

---

## 4. Pulse Analytics — dashboards green, finance numbers wrong

**App · industry:** Pulse Analytics · B2B analytics / usage metering

### The problem
Tenant delete/purge integration tests passed. Finance still disputed **unattributed usage %** on the operator console. Root issues: client list DTO **fabricated** `kind` and `user_count`; server query used wrong account-prefix; test mocks asserted `unattributed_pct` while API returned `unattributed_usage_pct` — **tests green, prod wrong**.

### How AADM MCP helped
| Tool | What it did |
|------|-------------|
| `prism_frame` | Split audit into Integrity (DTO fabrication), Measurement (mock field names), Persistence (purge scope) — stopped “fix the chart” reflex. |
| `sentinel_closure` | Required explicit pass/fail on **schema_contract** and **guidance_feedback** seams — surfaced false-green metrics **separate from** lifecycle delete proofs. |

### Hours saved (estimate)
**16–24 hours** — skipped ~8h of UI-only fixes, ~6h of re-running green CI, ~4h finance call repeating “numbers still wrong”

**Tools:** `prism_frame` · `sentinel_closure`

---

## 5. LedgerFlow — API credits double-count at month boundary

**App · industry:** LedgerFlow · fintech / usage-based billing

### The problem
Finance reported **~3–8% overage** vs contract on the last day of each billing cycle. Dashboard looked fine. Separate finding: one partner webhook path could **enqueue usage without balance check**. Team initially blamed “hosted Postgres latency.”

### How AADM MCP helped
| Tool | What it did |
|------|-------------|
| `route_lane` | Investigator lane on **mutation + projection** stack — blocked dashboard-only investigation. |
| `debug_defect` | Evidence-first: contract doc said cycle end **exclusive**; query used `lte` inclusive — classified HIGH with file anchors. |
| `prism_frame` | Second finding: integration **skip balance check** — separate severity from cycle math. |
| `journey_attest` | Mapped billing reconcile nodes (gateway stamp → async context → tenant reconcile) for fix verification checklist. |
| `sentinel_closure` | Auth seams **AUTH-16 / AUTH-24** bound to scope — no cross-tenant reconcile shortcuts. |

### Hours saved (estimate)
**24–40 hours** — avoided ~2 days of infra tuning, ~1 day of wrong UI proration fix, ~4h executive escalation on “database slow”

**Tools:** `route_lane` · `debug_defect` · `prism_frame` · `sentinel_closure` · `journey_attest`

---

## 6. Harbor Insurance — member portal works in tests, blank in Chrome

**App · industry:** Harbor Insurance · insurance / member self-service

### The problem
Policyholders saw **empty account settings** in production Chrome. Node integration tests passed. DevTools: `GET /api/me/benefits` blocked on CORS preflight — custom header `x-policy-scope` sent by client but **missing from** `Access-Control-Allow-Headers`.

### How AADM MCP helped
| Tool | What it did |
|------|-------------|
| `debug_defect` | **append_evidence** chain: DevTools network line → `apiHeaders.ts` setter → CORS middleware allow-list → required preflight contract test — halted “increase fetch timeout” suggestions. |

### Hours saved (estimate)
**8–14 hours** — typical wrong path: ~4h staging redeploys, ~4h “works on my machine,” ~2h support scripts; attested path went straight to allow-list + contract test

**Tools:** `debug_defect`

---

## 7. BridgeWorks — handoff preview 500 for one new work package

**App · industry:** BridgeWorks · construction / project scheduling

### The problem
`/portfolio/handoff-preview` returned **500** for project ID with a work package created yesterday. Spine API listed the package; materialized read-model table had **no row** — list-by-course returned partial set and skipped cold-start materialization for the new ID. On-call assumed DB timeout and proposed **doubling query timeout**.

### How AADM MCP helped
| Tool | What it did |
|------|-------------|
| `triage_bug` | Classified as **projection materialization gap**, not infrastructure — ruled out timeout padding as first fix. |
| `digfind` | Pointed search at projection list + cold-start policy — not generic “500 handler” grep across repo. |

### Hours saved (estimate)
**10–16 hours** — avoided ~6h of timeout/config changes, ~4h of false “DB incident” comms, ~2h hotfix rollback

**Tools:** `triage_bug` · `digfind`

---

## 8. Atlas Compliance — 258 “critical” alerts, zero customer impact

**App · industry:** Atlas Compliance · regtech / audit monitoring

### The problem
Pager fired **258 critical/error events** in one UTC day — all one event type: `schema_backlog_detected`. Live stale audit rows = **0**. Duplicate emit from worker matched known double-write; message text said “backlog converging.” VP wanted **rollback**.

### How AADM MCP helped
| Tool | What it did |
|------|-------------|
| `triage_bug` | Forced classification: incident vs **observability noise** vs author-facing guidance violation. |
| `classify_bug` | Downgraded to telemetry hygiene — duplicate worker log, not customer data corruption. |

### Hours saved (estimate)
**12–20 hours** — prevented ~4h midnight rollback + ~8h war room + ~4h customer comms draft for non-incident

**Tools:** `triage_bug` · `classify_bug`

---

## 9. FleetDispatch — org slug written where depot UUID required

**App · industry:** FleetDispatch · logistics / fleet ops

### The problem
Mid-migration: routes used org slug `/w/acme-logistics`, middleware set depot UUID, shipment tables FK’d to `depots.id`. Six export routes still passed **slug as depot id** — writes would **FK fail** on next migration. Client used `depotId ?? orgSlug` in twelve files.

### How AADM MCP helped
| Tool | What it did |
|------|-------------|
| `prism_frame` | Documented **partially shipped** three-ID model as fact pattern for assurance pass. |
| `fetch_seam` (**SEAM-06**) | Named **contract drift** seam — not “refactor when we have time.” |
| `map_feature` | Listed four **product decisions** blocking engineering (org-wide fleet vs per-depot catalog, scope columns, assistant rail, agent API). |
| `route_lane` | Assurance lane — migration correctness before feature velocity. |

### Hours saved (estimate)
**25–35 hours** — avoided ~1 week of silent FK failures in staging, ~8h of emergency hotfix, ~6h of ambiguous fallback spread in PR reviews

**Tools:** `prism_frame` · `route_lane` · `fetch_seam` · `map_feature`

---

## 10. Meridian Health — CI runs tests that no longer exist

**App · industry:** Meridian Health · healthcare / compliance workflows

### The problem
Proof-pack npm script referenced **deleted** 1,300-line integration monolith. Six smaller specs existed on disk. Invariants register and contract spine still cited ghost filenames — auditors assumed CI covered paths that **did not run**.

### How AADM MCP helped
| Tool | What it did |
|------|-------------|
| `fix_pack` | Required **evidence anchors**: `test ! -f …` proof absent, `wc -l` on survivors, grep spine lines — **halt before fix** until confirmed. |
| `route_lane` | Investigator hygiene — registry truth before new features. |

### Hours saved (estimate)
**14–22 hours** — ~6h per full CI run × 2 false-green runs avoided, ~4h audit prep, ~4h spine/doc reconciliation in one gated pass

**Tools:** `fix_pack` · `route_lane`

---

## Bonus — ClearPath Talent — skills map that proved nothing

**App · industry:** ClearPath Talent · HR tech / workforce planning

### The problem
Capabilities tab “Map” was three static chip rows (skills → capabilities → frameworks). Executives called it a **demo widget**. Eng proposed client-side D3 with invented edge weights — would duplicate server constellation API already shipping.

### How AADM MCP helped
| Tool | What it did |
|------|-------------|
| `ux_surface_pass` | Scoped Expand affordance + filters against **existing** gravity-field / constellation read models — no new graph authority on client. |
| `map_feature` | Mapped UI ambition to query contracts and flagged **client-derived graph** as deletion-test failure. |
| `user_flow` | Expand → full viewer → filter → drill-detail as attested journey before wireframes. |

### Hours saved (estimate)
**18–28 hours** — avoided ~2 weeks of parallel graph logic (~12h build + ~8h reconciliation when server truth disagreed)

**Tools:** `ux_surface_pass` · `map_feature` · `user_flow`

---

# Part II — Ten more vignettes (mutually exclusive with Part I)

---

## 11. StudioPublish — publish dashboard 500 on stale JSONB rows

**App · industry:** StudioPublish · digital media / content ops

### The problem
`GET …/publish-dashboard` returned **500** for ~12% of active tenants. Stored JSONB queue schema was **1.6.0**; reader expected **1.7.0** + new required array — strict parse on read. Round 1 fix stopped the 500; Round 2 found taxonomy-link saves **did not invalidate** downstream projections — UI showed “ready” on stale compliance verdicts.

### How AADM MCP helped
| Tool | What it did |
|------|-------------|
| `debug_defect` | Round 1: classified stored-payload Zod drift; prescribed parent version bump + safeParse self-heal pattern. |
| `journey_attest` | Round 2: cascade invalidation gap — **would have shipped false “fixed”** without attestation. |
| `prism_frame` | Structured sweep across six projections + guardrail requirements in one train narrative. |

### Hours saved (estimate)
**40–60 hours** — per-tenant firefighting (~4h × 8 tenants) + ~16h second incident from stale verdicts avoided by Round 2 attestation

**Tools:** `debug_defect` · `journey_attest` · `prism_frame`

---

## 12. Atlas Compliance — audit summary stuck on “computing” forever

**App · industry:** Atlas Compliance · regtech / audit monitoring

### The problem
`/compliance/summary` returned `{"status":"computing"}` for 20+ minutes. Ten concurrent background recompiles on same portfolio; advisory-lock waiters hit **8s pooler timeout** (Postgres 57014). `EXPLAIN` on drift query: **5ms** — not slow SQL. Team proposed raising timeouts globally.

### How AADM MCP helped
| Tool | What it did |
|------|-------------|
| `debug_defect` | **CLASSIFIED** root cause: missing in-flight dedupe — evidence score 7; listed **forbidden fixes** (timeout pad, remove lock). |
| `fetch_seam` | SEAM-08 async fan-out parity — recompile must not stampede on poll. |
| `route_lane` | Assurance on projection stack — not “upgrade database tier.” |

### Hours saved (estimate)
**32–48 hours** — ~3 days of false infra tuning + ~8h of padded test timeouts that would hide recurrence

**Tools:** `debug_defect` · `route_lane` · `fetch_seam`

---

## 13. Vertex Learning — lesson plan empty after “Saved successfully”

**App · industry:** Vertex Learning · edtech / course authoring

### The problem
Authors saved lesson rows (200 + toast). Lesson-plan panel showed **empty template** because UI gated on nullable `compiledHint` while course projection returned **500 on cold cache miss**. Support volume: “save broken” — actually **read path** and **wrong client gate**.

### How AADM MCP helped
| Tool | What it did |
|------|-------------|
| `route_lane` | Assurance + **guidance-feedback** stack — separate transient computing from corrective empty-after-save. |
| `fetch_seam` | SEAM-07 read shaping, SEAM-08 async parity, SEAM-13 guidance–feedback — rejected warm-all-first (60–90s hang) and client-only fallback. |
| `fetch_auth` | AUTH-19 UI authority — client must not infer template from nullable hint. |
| `architecture_lens` | Single resolver for display state — deletion test on parallel gates. |

### Hours saved (estimate)
**35–50 hours** — policy locked in **one session** (~4h) vs ~3 slices discovered mid-sprint (~12h each) + ~8h support playbook wrong fixes

**Tools:** `route_lane` · `fetch_seam` · `fetch_auth` · `architecture_lens`

---

## 14. BridgeWorks — “Can’t save brief” on first edit (phantom conflict)

**App · industry:** BridgeWorks · construction / field apps

### The problem
Field brief PATCH returned **409 `version_mismatch`** on **first save** after open — no concurrent editors. Cause: `updated_at` OCC token lost Postgres µs through JS `Date`. Recovery UI said “re-import your plan” — wrong workflow for a save conflict.

### How AADM MCP helped
| Tool | What it did |
|------|-------------|
| `route_lane` | Mutation stack — not “disable optimistic locking.” |
| `coding_principles` | Integer **`revision` counter** replaces timestamp token — data-model decision before patch routes. |
| `plan_delivery` | Four **lane-specific conflict codes** + registry stale entries in one attested cutover plan. |

### Hours saved (estimate)
**28–40 hours** — ~10h/support week on unreproducible tickets + ~20h avoided debating four separate one-off fixes across lanes

**Tools:** `route_lane` · `coding_principles` · `plan_delivery`

---

## 15. Harbor Insurance — release review “black box” gates

**App · industry:** Harbor Insurance · insurance / policy administration

### The problem
Underwriters saw **pass/fail gates** on `/release` with no artifact transparency. New requirement: seven-tile “what we’re shipping” strip + drilldown + schema cascade across three read models — high merge risk without attestation.

### How AADM MCP helped
| Tool | What it did |
|------|-------------|
| `journey_attest` | **21 nodes**, 0 blocking, **`ready_with_residual`** — latent E2E explicitly listed, not hidden. |
| `sentinel_closure` | Vertical **through_domain_and_data** + horizontal schema/guidance seams signed off. |
| `architecture_lens` | Path B: extend shipped artifact vs dormant schema — avoided greenfield duplicate model. |

### Hours saved (estimate)
**24–36 hours** — ~3 × 2h stakeholder “is it safe?” meetings + ~12h of undocumented residual debt discovered post-merge

**Tools:** `journey_attest` · `sentinel_closure` · `architecture_lens`

---

## 16. ClearPath Talent — four AI assistant allowlists drifting apart

**App · industry:** ClearPath Talent · HR tech / AI-assisted workflows

### The problem
Recruiting, onboarding, learning, and analytics each hardcoded **different** assistant action allowlists. MCP agent + internal assignment console incoming — would have added **fifth list**. SA console preview would show actions production rails hid.

### How AADM MCP helped
| Tool | What it did |
|------|-------------|
| `map_feature` | Scoped single resolver + DB override table + drop-reason codes before UI. |
| `plan_delivery` | Slices: seed registry → migration → resolver → SA console → guardrail in CI. |
| `grill_questions` | Six canonical questions — deletion test: remove resolver → all four rails fail visibly. |

### Hours saved (estimate)
**45–70 hours** — ~2 weeks rebuilding console after drift (~30h) + ~20h debugging “button shows in preview not prod” + ~8h duplicate normalization hunts

**Tools:** `map_feature` · `plan_delivery` · `grill_questions`

---

## 17. StudioPublish — duplicate “Regenerate” menu vs shipped strip

**App · industry:** StudioPublish · digital media / content ops

### The problem
Spec still required four-verb dropdown (Approve / Regenerate / Refine / Edit by hand). **Slot rerun strip already shipped** — same backend, second client resolver. Would rebuild menu + sub-menu + tests for zero new capability.

### How AADM MCP helped
| Tool | What it did |
|------|-------------|
| `grill_questions` | Canonical six — strip passes **one resolver / one affordance**; menu fails deletion test. |
| `architecture_lens` | Guidance–feedback seam — parallel resolver = drift factory. |

### Hours saved (estimate)
**30–45 hours** (~1–1.5 sprint-equivalents) — menu UI (~16h), duplicate tests (~8h), doc reconciliation (~6h)

**Tools:** `grill_questions` · `architecture_lens`

---

## 18. Vertex Learning — headless course builder before structure brain exists

**App · industry:** Vertex Learning · edtech / generative authoring

### The problem
Cron worker stages (brief → outline → lessons → blocks) ready to wire. Risk: **second structure brain** beside orchestrator — Stage 2 would invent module count/arcs ad hoc; Stage 3 recipes wouldn’t match.

### How AADM MCP helped
| Tool | What it did |
|------|-------------|
| `grill_questions` | **audit_mode CONFIRM GO** — pedagogy resolver P0, 409 on plan mismatch, advisory frameworks deferred to P4. |
| `route_lane` | Navigator delivery — blocked Stage 2 wire until resolver lands. |
| `architecture_lens` | Orchestrator stays execution-only — no parallel structure planner. |

### Hours saved (estimate)
**16–24 hours** — ~1 scheduled architecture review (~3h) + ~12–18h rework when Stage 2/3 diverged without resolver

**Tools:** `grill_questions` · `route_lane` · `architecture_lens`

---

## 19. Atlas Compliance — 688 tests red after remediation sweep

**App · industry:** Atlas Compliance · regtech / remediation workflows

### The problem
Compliance hub remediation touched **50+ spec files** — registry Zod drift, adapter guards, lane-specific conflict proofs, six guardrails. Team faced **full CI** (~90 min) per guess; 688-test alignment bundle failing.

### How AADM MCP helped
| Tool | What it did |
|------|-------------|
| `journey_attest` | Smoke attestation: proven nodes (schema, resolver, hook, adapter) vs **latent** Playwright — stopped “run everything until green.” |
| `fix_pack` | Evidence anchors per cluster — file list + command output before each commit. |
| `pressure_test` | Hook + read-model surfaces — async/cache flags set before recovery PRs. |

### Hours saved (estimate)
**50–80 hours** — ~6 full CI runs (~9h each) reduced to ~2 targeted runs + ~8h cluster commits with attested smoke journey

**Tools:** `journey_attest` · `fix_pack` · `pressure_test`

---

## 20. FleetDispatch — depot cutover blocked on four product answers

**App · industry:** FleetDispatch · logistics / multi-depot SaaS

### The problem
Engineering ready for org→depot migration; product unresolved: org-wide route catalog vs per-depot, which tables get `depot_id`, assistant scope per org or depot, agent API safety when user switches depot mid-session. Team started coding **assumptions**.

### How AADM MCP helped
| Tool | What it did |
|------|-------------|
| `slice_issues` | Turned vague blockers into **numbered slice_issues** with owner (product vs eng). |
| `grill_questions` | Six questions — each decision tied to AUTH/seam consequence. |
| `map_feature` | Feature map: /main hub, course list, metrics, Signal docs — flagged columns missing. |
| `prism_frame` | Partial-ship fact pattern — what’s done vs dangerous fallback spread. |

### Hours saved (estimate)
**20–30 hours** — ~2 × 2h cross-functional meetings + ~12–18h throwaway migration code on wrong product assumption

**Tools:** `slice_issues` · `grill_questions` · `map_feature` · `prism_frame`

---

## Summary — estimated hours saved (all vignettes)

| # | App | Estimate (hours) |
|---|-----|------------------|
| 1 | StudioPublish | 12–18 |
| 2 | Vertex Learning | 20–30 |
| 3 | Northstar Retail | 30–45 |
| 4 | Pulse Analytics | 16–24 |
| 5 | LedgerFlow | 24–40 |
| 6 | Harbor Insurance | 8–14 |
| 7 | BridgeWorks | 10–16 |
| 8 | Atlas Compliance | 12–20 |
| 9 | FleetDispatch | 25–35 |
| 10 | Meridian Health | 14–22 |
| B | ClearPath Talent | 18–28 |
| 11 | StudioPublish | 40–60 |
| 12 | Atlas Compliance | 32–48 |
| 13 | Vertex Learning | 35–50 |
| 14 | BridgeWorks | 28–40 |
| 15 | Harbor Insurance | 24–36 |
| 16 | ClearPath Talent | 45–70 |
| 17 | StudioPublish | 30–45 |
| 18 | Vertex Learning | 16–24 |
| 19 | Atlas Compliance | 50–80 |
| 20 | FleetDispatch | 20–30 |

**Portfolio band (if these 20 patterns applied in one quarter):** roughly **490–770 engineer-hours** redirected from wrong fixes, duplicate UI, false CI, and meeting loops — **not** raw “AI coding speed.”

---

## Part I ↔ Part II exclusivity map

| Part I topic | Part II does **not** repeat |
|--------------|----------------------------|
| Export back-nav UX | §11 dashboard 500 / JSONB |
| External platform gap map | §11 cascade / §15 release strip |
| Operator monolith refactor | §4 false-green / §20 cutover decisions |
| Usage billing cycle | §14 phantom 409 |
| CORS browser vs Node | §12 poll-storm / §13 cold projection |
| Read-model cold-start | §11 cascade / §13 author trust |
| 258 monitoring noise | §12 computing stuck / §19 test recovery |
| Org→depot FK cutover | §20 product decision menu |
| Integration monolith CI | §19 drift hub recovery |
| Skills map expandable | §16 assistant rail / §17 regenerate strip |

---

## App roster

| Fictional app | Industry | Vignettes |
|---------------|----------|-----------|
| **StudioPublish** | Digital media | 1, 2, 11, 17, Bonus |
| **Vertex Learning** | Edtech | 2, 13, 18 |
| **Northstar Retail** | Retail SaaS | 3 |
| **Pulse Analytics** | B2B analytics | 4 |
| **LedgerFlow** | Fintech | 5 |
| **Harbor Insurance** | Insurance | 6, 15 |
| **BridgeWorks** | Construction | 7, 14 |
| **Atlas Compliance** | Regtech | 8, 12, 19 |
| **FleetDispatch** | Logistics | 9, 20 |
| **Meridian Health** | Healthcare | 10 |
| **ClearPath Talent** | HR tech | Bonus, 16 |

---

## Public-site copy rules

| Do | Don’t |
|----|--------|
| “**12–18 hours** avoided wrong Back button implementation + review cycle” | “AADM saved 90% of development” |
| “`debug_defect` classified root cause in one session; forbade timeout padding” | “AADM fixed production” |
| “Round 2 `journey_attest` caught stale verdicts before ship” | “Zero bugs” |

**Honest boundary:** AADM MCP frames problems, seams, and proof obligations. **Your repo’s tests and evidence** still decide pass/fail.

For longer baselines: [evidence-first-defect-resolution.md](evidence-first-defect-resolution.md) · [MCP quickstart](../docs/mcp-quickstart.md)

**Website hero cards:** one-paragraph copy for all 20 vignettes → [mcp-real-world-vignettes-cards-v1.md](mcp-real-world-vignettes-cards-v1.md)
