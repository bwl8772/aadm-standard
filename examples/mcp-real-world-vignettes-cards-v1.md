# AADM MCP — website hero cards (one paragraph each)

Copy-paste cards for landing pages, carousels, or social. Pairs with the full vignettes in [mcp-real-world-vignettes-v1.md](mcp-real-world-vignettes-v1.md). App names are fictional; hour ranges are illustrative engineering-time avoided.

---

## 1 · StudioPublish · Media ops
**Wrong Back button · 12–18 hrs saved**

Producers drilling into an export sub-page kept landing in the wrong parent—campaign, QC queue, or library—because a single Back control ignored where they came from. AADM MCP’s `ux_surface_pass`, `user_flow`, and `pressure_test` modeled two intentional exits (return to origin vs return to hub) and named component tests before any header tweak shipped, saving an estimated **12–18 hours** of design rework and QA repro cycles.

---

## 2 · Vertex Learning · Edtech
**Four integrations, no gap map · 20–30 hrs saved**

Product committed to a full publish loop across slide, voice, video, and LMS vendors, but engineering only had partial async jobs and fourteen scattered tickets. `ux_surface_pass` captured the ten-step author journey; `signal_stack_outline` and `journey_attest` tagged what was proven vs latent on operator monitoring—producing one attested requirements brief instead of repeated cross-team workshops (**~20–30 hours** saved).

---

## 3 · Northstar Retail · Retail SaaS
**4,000-line admin monolith · 30–45 hrs saved**

One merchant-admin screen (~4k LOC) shared URL state and cross-deck cache invalidation across three in-page decks—a split-without-plan would have broken purge auth. `route_lane` and `plan_delivery` sequenced hook attribution first; `fetch_auth` pulled tenancy controls in; `architecture_lens` pressure-tested purge vs archive. Named slices shipped instead of delete-and-pray (**~30–45 hours** of regressions avoided).

---

## 4 · Pulse Analytics · B2B analytics
**Green tests, wrong meters · 16–24 hrs saved**

Tenant lifecycle tests passed while finance disputed usage percentages—client DTOs fabricated fields and mocks used the wrong metric name (`unattributed_pct` vs `unattributed_usage_pct`). `prism_frame` split integrity vs measurement; `sentinel_closure` forced explicit pass/fail on schema seams—surfacing false-green dashboards separate from delete proofs (**~16–24 hours** of UI-only fixes and repeat finance calls avoided).

---

## 5 · LedgerFlow · Fintech
**Billing cycle double-count · 24–40 hrs saved**

Month-end API credits overshot finance expectations because the usage query treated cycle end as inclusive when the contract said exclusive—and one webhook path skipped balance checks. `debug_defect` and `prism_frame` kept the thread evidence-first; `journey_attest` mapped reconcile nodes. Ledger fixes landed without dashboard band-aids or “upgrade the database” detours (**~24–40 hours** saved).

---

## 6 · Harbor Insurance · Insurance
**Works in Node, fails in Chrome · 8–14 hrs saved**

Members saw empty account settings in production while integration tests passed—a custom scope header was blocked on CORS preflight. `debug_defect` chained DevTools proof → header setter → allow-list → contract test, halting timeout-padding guesses. Fix shipped in one attested path (**~8–14 hours** vs redeploy-and-hope loops).

---

## 7 · BridgeWorks · Construction
**Handoff preview 500 · 10–16 hrs saved**

A new work package appeared in the spine but handoff preview returned 500—partial projection lists skipped cold-start materialization. On-call proposed doubling query timeouts. `triage_bug` ruled out infra; `digfind` targeted materialization policy. Scoped fix, no rollback (**~10–16 hours** saved).

---

## 8 · Atlas Compliance · Regtech
**258 critical alerts, zero impact · 12–20 hrs saved**

Monitoring fired 258 critical events in one day—all one self-healing backlog tick with duplicate worker emits and zero stale rows. Leadership wanted rollback. `triage_bug` and `classify_bug` downgraded to telemetry hygiene, not customer incident (**~12–20 hours** of midnight war room avoided).

---

## 9 · FleetDispatch · Logistics
**Three IDs, silent FK failures · 25–35 hrs saved**

Mid-migration, org slug, depot UUID, and middleware scope coexisted while export routes still wrote slug into UUID FK columns. `prism_frame` documented partial ship; `fetch_seam` (SEAM-06) named contract drift; `map_feature` listed four product blockers. Cutover continued with a decision log—not `depotId ?? orgSlug` spread (**~25–35 hours** of staging firefighting avoided).

---

## 10 · Meridian Health · Healthcare
**CI cites ghost tests · 14–22 hrs saved**

Proof-pack scripts referenced deleted 1,300-line monoliths while invariants register still claimed they ran—auditors assumed coverage that didn’t exist. `fix_pack` required file-absence evidence before cutover; `route_lane` kept it Investigator hygiene. Registry matched disk (**~14–22 hours** of false-green CI and audit prep saved).

---

## 11 · StudioPublish · Media ops
**Dashboard 500 on stale JSONB · 40–60 hrs saved**

Publish dashboard returned 500 when stored queue schema lagged reader expectations; Round 1 fixed the throw but Round 2 found taxonomy saves not invalidating projections—UI would lie “ready” on stale verdicts. `debug_defect` plus `journey_attest` closed both classes in one train (**~40–60 hours** of per-tenant firefighting and a second incident avoided).

---

## 12 · Atlas Compliance · Regtech
**Stuck on “computing” · 32–48 hrs saved**

Audit summaries hung for 20+ minutes—ten concurrent recompiles stampeded advisory locks at 8s pooler timeout while SQL ran in 5ms. Team wanted global timeout increases. `debug_defect` classified missing in-flight dedupe and listed forbidden fixes (**~32–48 hours** of false infra tuning avoided).

---

## 13 · Vertex Learning · Edtech
**Empty form after “Saved” · 35–50 hrs saved**

Lesson rows persisted with a success toast, but the plan panel stayed empty—the UI gated on a nullable hint while projections 500’d on cold miss. `route_lane` and `fetch_seam` rejected warm-all-first (60–90s hangs) and client-only fallback before multi-slice work (**~35–50 hours** of wrong support playbooks and mid-sprint rework saved).

---

## 14 · BridgeWorks · Construction
**Phantom save on first edit · 28–40 hrs saved**

First brief save returned version mismatch—timestamp OCC tokens lost microsecond precision through JavaScript Date—and recovery copy sent users to re-import workflows. `route_lane`, `coding_principles`, and `plan_delivery` drove integer revision counters and lane-specific conflict codes in one attested cutover (**~28–40 hours** of unreproducible tickets and four debated one-offs avoided).

---

## 15 · Harbor Insurance · Insurance
**Release black box · 24–36 hrs saved**

Underwriters saw pass/fail gates with no artifact transparency; a seven-tile “what we’re shipping” strip risked merge without proof. `journey_attest` returned ready_with_residual (21 nodes, 0 blocking); `sentinel_closure` signed horizontal seams. Shipped with residuals explicit (**~24–36 hours** of “is it safe?” meetings saved).

---

## 16 · ClearPath Talent · HR tech
**Four AI allowlists drifting · 45–70 hrs saved**

Recruiting, onboarding, learning, and analytics each hardcoded different assistant actions—MCP and an assignment console would have added a fifth brain. `map_feature`, `plan_delivery`, and `grill_questions` locked one resolver, drop-reason codes, and CI guardrails before UI sprawl (**~45–70 hours** of console rewrite and preview-vs-prod debugging avoided).

---

## 17 · StudioPublish · Media ops
**Duplicate Regenerate menu · 30–45 hrs saved**

A spec still required a four-verb dropdown while a per-slot rerun strip already shipped the same backend—building the menu would introduce a second client resolver with zero new capability. `grill_questions` and `architecture_lens` failed the menu on the deletion test; doc cleanup only (**~30–45 hours**, ~1–1.5 sprints, saved).

---

## 18 · Vertex Learning · Edtech
**Course builder without structure brain · 16–24 hrs saved**

A headless cron worker was ready to wire Stage 2 outline generation beside the orchestrator—risk of divergent module plans and mismatched recipes. `grill_questions` audit_mode CONFIRM GO blocked Stage 2 until a pedagogy resolver landed (**~16–24 hours** of architecture review wait and rework avoided).

---

## 19 · Atlas Compliance · Regtech
**688 tests red · 50–80 hrs saved**

Remediation touched 50+ spec files; each guess triggered ~90-minute full CI. `journey_attest` smoke marked proven vs latent nodes; `fix_pack` anchored evidence per cluster. Recovery ran targeted bundles instead of shotgun CI (**~50–80 hours** saved).

---

## 20 · FleetDispatch · Logistics
**Migration blocked on product · 20–30 hrs saved**

Engineering was ready for org→depot cutover but product hadn’t decided catalog scope, scope columns, assistant rail, or agent API safety—and coders started assuming answers. `slice_issues`, `grill_questions`, and `map_feature` produced a numbered decision menu in one agent session (**~20–30 hours** of throwaway migration code and meeting cycles saved).

---

## Bonus · ClearPath Talent · HR tech
**Skills map demo widget · 18–28 hrs saved**

A static three-row chip map proved nothing; eng proposed client-side D3 with invented weights while server constellation APIs already existed. `ux_surface_pass` and `map_feature` tied Expand, filters, and clustering to authoritative read models before wireframes (**~18–28 hours** of parallel graph logic avoided).

---

## Card metadata (optional UI fields)

| # | Title (≤8 words) | Industry tag | Hours | Primary tools |
|---|------------------|--------------|-------|---------------|
| 1 | Two exits, not one Back | Media | 12–18 | ux_surface_pass |
| 2 | Integration gap inventory | Edtech | 20–30 | journey_attest |
| 3 | Slice the 4k monolith | Retail | 30–45 | plan_delivery |
| 4 | False-green dashboards | Analytics | 16–24 | sentinel_closure |
| 5 | Billing cycle math | Fintech | 24–40 | debug_defect |
| 6 | CORS preflight trap | Insurance | 8–14 | debug_defect |
| 7 | Missing projection row | Construction | 10–16 | digfind |
| 8 | Alert noise, not incident | Regtech | 12–20 | classify_bug |
| 9 | Three-ID migration | Logistics | 25–35 | fetch_seam |
| 10 | Ghost CI paths | Healthcare | 14–22 | fix_pack |
| 11 | JSONB drift 500 | Media | 40–60 | journey_attest |
| 12 | Computing forever | Regtech | 32–48 | debug_defect |
| 13 | Empty after save | Edtech | 35–50 | fetch_seam |
| 14 | Phantom 409 | Construction | 28–40 | plan_delivery |
| 15 | Release transparency | Insurance | 24–36 | journey_attest |
| 16 | One assistant brain | HR tech | 45–70 | map_feature |
| 17 | Deletion test wins | Media | 30–45 | grill_questions |
| 18 | Structure brain first | Edtech | 16–24 | grill_questions |
| 19 | Targeted test recovery | Regtech | 50–80 | fix_pack |
| 20 | Product decision menu | Logistics | 20–30 | slice_issues |

**Portfolio band (all 20):** ~**490–770 engineer-hours** redirected from wrong fixes and meeting loops—not raw codegen speed.
