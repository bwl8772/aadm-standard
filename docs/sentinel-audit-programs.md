# Sentinel audit programs — UDALI vs AADM (requirements)

**Persona:** **Sentinel** (`assurance` lane) — prove alignment with declared standards for **existing** posture, not a single feature brief and not a single defect thread.

**Purpose of this document:** Scope and requirements for two **launchable audit programs** hosts may expose as distinct Sentinel passes:

1. **UDALI audit** — placement, boundaries, and collaboration-map discipline (see [UDALI coding personas](udali-personas.md); optional [L1–L22](udali-22-layer-model.md)).  
2. **AADM audit** — full delivery-model posture, including UDALI **plus** AUTH, enforcement, evidence, testing, and agent-participation governance.

This **standard repo** defines **what each program means** and **what evidence must exist** before calling an audit “complete.” How your organization **runs** an audit (checklists, portals, scripts) is implementation-specific.

---

## 1. Shared foundations (both programs)

### 1.1 Authority of verdict

- **Documentation frames** the audit; **the application repository** (code, tests, CI, operational controls) **proves** claims.  
- Sentinel outputs are **attestations of scope and evidence binding**, not a substitute for organizational sign-off or regulated certification unless your governance layer says otherwise.

### 1.2 Ontology vs scope (non-negotiable)

Every pass MUST declare:

| Concept | Requirement |
|--------|-------------|
| **Ontology** | Full vertical model and dependencies remain valid; skipping hops **moves risk**, it does not erase failure modes. |
| **Scope of proof** | What is **proven now** (required seams), what is **recommended to widen** (additional), what is **explicitly out of scope** (latent / non-goals). |

### 1.3 Three stages (both programs)

| Stage | Name | Exit criterion |
|-------|------|----------------|
| 1 | **Scope & frame** | Audit program selected (**UDALI** or **AADM**); boundaries, systems, and timebox recorded; stakeholders named. |
| 2 | **Trace & bind evidence** | Each claim in that program’s checklist has **anchors** (repo paths, test names, CI jobs, tickets, runbooks) **or** an explicit **gap/risk** with owner. |
| 3 | **Attest readiness** | Human sign-off: proven / assumed / deferred; follow-ups routed to **Navigator** (change program) or **Investigator** (concrete defect) when appropriate. |

### 1.4 Observability and agents

- Sentinel does **not** require production log ingestion inside a documentation portal; evidence MAY include pasted excerpts or links **your** observability owns.  
- Where auto-coding agents participate, each seam SHOULD note expectations for correlation, boundary errors, and async visibility—as **expectations** tied to **your** systems, not as substitute observability.

---

## 2. Program A — Full **UDALI audit**

### 2.1 Definition

A **UDALI audit** attests that work **fits the collaboration map**: concerns sit in the right **U / D / A / L / Integrator** stance (and finer slices where your organization uses them), **handoffs** are explicit, and **cross-layer leakage** is bounded or explicitly accepted risk.

It is **architecture-and-process placement**, not full AUTH catalog conformance unless you explicitly widen scope.

### 2.2 In scope (minimum)

| Area | Requirement |
|------|-------------|
| **Layer / role map** | Artifact ties features or systems to UDALI groupings (and optional layer IDs if you use a 22-slice map). |
| **Boundary discipline** | Named interfaces between presentation, application/API, domain, data access, and delivery closure (Integrator); no silent “god path” without documented exception. |
| **Handoffs** | Designer ↔ Adapter ↔ Logician ↔ Integrator (and Unifier where UI/client involved) have **clear ownership** for the scoped flows. |
| **Evidence** | Spot-check: representative paths have **tests or reviews** at declared boundaries (or documented gaps). |

### 2.3 Out of scope (unless explicitly added)

- Exhaustive **AUTH-XX** control-by-control proof.  
- Full **CI pipeline / evidence model** audit (those belong under **AADM audit** unless narrowly framed).  
- **Investigator** root-cause work on a single bug (different lane).

### 2.4 Launch inputs (minimum)

- Target: service, repo, or bounded initiative name.  
- **Vertical claim** for this pass (how deep UI→metal is asserted).  
- **Horizontal seams** in play (schema, integration boundary, auth trust, external dependency, async/event, **guidance–feedback** when nudges/recommendations/readiness UX are in scope, or none declared).  
- Depth: `narrow` | `standard` | `expansive` (or your org’s equivalent).

### 2.5 Deliverables (minimum)

- **Scope statement** (ontology vs proof burden).  
- **Vertical trace table** (coarse hops: presentation → app → domain → data → platform as applicable).  
- **Horizontal seam matrix** with proof or gap per cell (include **`HORI_GUIDANCE_FEEDBACK`** when system-status / nudge / recommendation UX is attested — see [Guidance–feedback seam](../standards/guidance-feedback-seam.md)).  
- **Residual risk register** (latent seams, deferred proof).  
- **Sign-off** record (role, date, system of record).

### 2.6 Supporting artifacts (optional)

Teams often pair this pass with internal checklists derived from [layer map](../templates/layer-map.md), [architecture principles](architecture-principles.md), and AUTH placement summaries—**heuristic** guides, not substitutes for repo proof.

---

## 3. Program B — Full **AADM audit**

### 3.1 Definition

An **AADM audit** attests that the **program** (product + engineering posture) aligns with the **Agentic Authority Delivery Model** pillars: not only **where** work sits (UDALI) but **how** intent, controls, enforcement, testing, evidence, and agent participation are governed.

**UDALI audit is a proper subset of concerns** inside AADM audit; AADM audit **always** includes a UDALI placement review **or** incorporates a completed UDALI audit artifact by reference.

### 3.2 In scope (minimum — additive to UDALI)

| Pillar | Requirement |
|--------|-------------|
| **Intent & artifacts** | Build intent / BIS-lite or equivalent exists for material work; traceability from intent to shipped boundary. |
| **AUTH posture** | Applicable **AUTH** themes identified; controls mapped to ownership; enforcement points named (gateway, service, job)—not “documentation only.” |
| **Enforcement design** | Where controls must hold in code/config; known gaps explicit. |
| **Testing & proof** | CI/tests tied to claims; critical paths have falsifiable checks or accepted residual risk. |
| **Evidence & auditability** | How compliance claims are supported (logs, tickets, reviews)—aligned with your evidence model. |
| **Agent discipline** | Where agents are used: scope limits, credential hygiene, no substitution of assistant tools for production authorization. |

### 3.3 Out of scope (unless contract expands)

- Penetration test or formal certification packaging.  
- Organization-wide HR or procurement policy.  
- Vendor SOC reports (may be **inputs**, not outputs of this pass).

### 3.4 Launch inputs (minimum)

Everything in **§2.4**, **plus**:

- AUTH catalog scope (e.g. rule families or IDs in play).  
- Pipeline stage map (when gates run).  
- Agent usage boundary statement (yes/no/where).  
- Link or attachment to **completed UDALI audit** OR explicit instruction to perform UDALI slice inside this engagement.

### 3.5 Deliverables (minimum)

Everything in **§2.5**, **plus**:

- **AUTH mapping matrix** (control → enforcement locus → proof artifact).  
- **Pipeline / gate summary** (what blocks merge/release).  
- **Evidence pack index** (where auditors look).  
- **Agent governance summary** (allowed scopes, prohibited shortcuts).  
- **Sign-off** with explicit statement that **AADM program-level** claims are **proven / assumed / deferred**.

### 3.6 Optional — Contract boundary trace (**TRACE**)

When the dominant risk is **data contract drift** (not only “was AUTH cited?”), teams MAY add a **contract trace** slice inside an **AADM audit** (or a narrower Sentinel pass):

- **Slice-bound** scope; **3–5 critical fields** whose drift would hurt the business.  
- Per field × **material handoff** (e.g. wire ↔ validated shape ↔ domain ↔ persistence ↔ async payload): **PASS**, **FAIL**, or **DEFERRED** with owner — each claim anchored in the **application repository** or explicitly gapped.  
- Drift classified with stable **`TRACE-NN`** pattern ids **where your organization maintains a catalog**.

**Boundary:** This **public repo** states the **practice** and evidence rule (repo proves; documentation does not scan consumers). Machine-stable pattern tables are **organizational** assets versioned outside this repository if you use them.

---

## 4. Relationship summary

| Aspect | UDALI audit | AADM audit |
|--------|-------------|------------|
| Primary question | “Are concerns in the right UDALI places with clear boundaries?” | “Does the program meet AADM pillars (UDALI + AUTH + enforcement + proof + agents)?” |
| AUTH depth | Thematic / placement only unless widened | Systematic applicability and enforcement binding |
| Typical duration | Shorter; bounded architecture pass | Longer; cross-functional |
| Prerequisite | None | SHOULD incorporate UDALI audit findings or repeat UDALI slice |
| Sentinel depth | Often `narrow`–`standard` | Often `standard`–`expansive` |

---

## 5. Host implementation notes (generic)

These are **requirements on hosts** that want a literal **“Launch UDALI audit”** / **“Launch AADM audit”** control—not mandatory for reading this standard.

| ID | Requirement |
|----|-------------|
| **R1** | Host MUST persist **program selection** (UDALI vs AADM), **stage**, and **scope inputs** for reproducibility. |
| **R2** | Host MUST NOT imply documentation or portals alone perform repo scanning; copy MUST state **consumer repo proves** claims. |
| **R3** | **UDALI** launch MUST pre-load or link **layer-map / UDALI model** resources; **AADM** launch MUST pre-load AUTH + build-intent pointers. |
| **R4** | Outputs MUST be exportable (markdown/PDF/JSON) with a **version pin** when closure rules come from a versioned internal catalog. |
| **R5** | Follow-on routing SHOULD expose distinct stable ids (e.g. `sentinel_audit_udali`, `sentinel_audit_aadm`) so orchestrators branch deterministically. |

---

## 6. Non-goals

- Replacing **Investigator** defect investigation for a concrete bug thread.  
- Replacing **Navigator** authorization for net-new delivery.  
- Emitting **compliance pass/fail** without human attestation stage 3.

---

## Related reading

- [What is AADM?](what-is-aadm.md)  
- [Glossary — TRACE, VERT\_\*, HORI\_\*](glossary.md)  
- [UDALI coding personas](udali-personas.md)  
- [UDALI 22-layer model](udali-22-layer-model.md)  
- [AUTH-aware delivery](auth-aware-delivery.md)  
- [Build intent specification](build-intent-specification.md)  
- [Repository scope](repository-scope.md)  
- [Compliance checklist](compliance-checklist.md)  
