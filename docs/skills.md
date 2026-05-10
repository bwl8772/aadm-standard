# Creating and aligning skills with the AADM standard

This guide is for teams that use **agent skills**—packaged instructions your tooling loads when a task matches (often a `SKILL.md` or equivalent)—and want those skills to **speak the same language** as AADM: intent, layers, AUTH, evidence, and delivery lanes.

**What this is not:** a tutorial for any single vendor’s skill file format, folder layout, or marketplace. Follow **your** product’s documentation for syntax; use this page for **alignment** with the standard.

---

## 1. What we mean by “skills”

In AADM materials, **skills** are **durable, reusable playbooks** for agents:

- They describe **when** the skill applies (triggers, keywords, ticket types).  
- They describe **what** the agent should produce (artifacts, sections, checks).  
- They describe **boundaries**—what is out of scope or requires human approval.

Skills sit **next to** chats and one-off prompts: they reduce drift when many people automate similar work.

---

## 2. Why align skills with AADM

Without a shared frame, skills tend to:

- Collapse **delivery**, **defects**, and **assurance** into one generic “help me code” path.  
- Skip **build intent** and **layer placement**, so reviews cannot tell what broke or why.  
- Treat **AUTH** as optional prose instead of named enforcement concerns.

Aligning skills with AADM means:

- Every skill names a **delivery lane** ([Glossary — Navigator, Investigator, Sentinel](glossary.md#navigator-investigator-and-sentinel-delivery-lanes)) where possible.  
- Outputs reference **templates** from this repo ([`templates/`](../templates/)) when humans need something ticket-ready.  
- Strong claims still point to **tests and CI**—skills document **how to work**, not **proof** ([Architecture principles](architecture-principles.md), [Repository scope](repository-scope.md)).

---

## 3. Start with a delivery lane

Before writing steps, decide which lane fits the **primary question**:

| Lane | Primary question | Skill should emphasize |
|------|------------------|-------------------------|
| **Navigator** | What are we building or materially improving, and how do we verify readiness? | Scope, interfaces, acceptance signals, phased validation—not premature defect closure. |
| **Investigator** | What is broken versus expected, and what **evidence** supports that? | Reproduction anchors, minimal traces, one hypothesis at a time—avoid broad refactors before root cause is grounded. |
| **Sentinel** | Does **existing** posture align with declared standards across seams? | Scope vs ontology, evidence binding, attestations—not the same narrow scope as a single bug thread. |

If a skill mixes lanes (for example, “small bug fix” that also needs a layer map), **split into two skills** or **sequence**: Investigator first for the defect, Navigator for the follow-up design change.

---

## 4. Common skill intents and standard anchors

Use this table when naming skills or writing “when to use” sections. Map **your** skill names to these **intents**; keep wording stable inside your org.

| Skill-style intent | Typical lane | Point people and agents at |
|--------------------|--------------|----------------------------|
| **Triage / classify** | Often pre-lane | [Glossary](glossary.md), [UDALI personas](udali-personas.md) — label concern and suggest lane. |
| **Shape intent / plan** | Navigator | [Build intent specification](build-intent-specification.md), [BIS-lite](../templates/bis-lite.md), [layer map](../templates/layer-map.md). |
| **Spec / PRD bridge** | Navigator | [PRD-lite](../templates/prd-lite.md), [use case](../templates/use-case.md). |
| **Investigate defect** | Investigator | Evidence-first patterns in [examples/evidence-first-defect-resolution.md](../examples/evidence-first-defect-resolution.md), [AUTH-aware delivery](auth-aware-delivery.md) for privilege themes. |
| **Assurance / audit** | Sentinel | [Sentinel audit programs](sentinel-audit-programs.md), [Compliance checklist](compliance-checklist.md). |
| **Architecture / boundaries** | Often Navigator (sometimes Sentinel) | [Architecture principles](architecture-principles.md), [Glossary — Module, Interface, Seam](glossary.md). |
| **Test discipline / acceptance** | Navigator (closure) | [Acceptance criteria](../templates/acceptance-criteria.md), QA section of [Compliance checklist](compliance-checklist.md). |

---

## 5. What to put inside a skill file

A clear skill file usually includes:

1. **Title and one-line purpose** — What problem this skill solves.  
2. **Lane** — Navigator, Investigator, or Sentinel (or “triage → lane”).  
3. **Triggers** — Ticket labels, user phrases, file paths, or systems that should activate the skill.  
4. **Inputs** — What the human must provide (scope, links, failing test name, environment).  
5. **Steps** — Ordered behavior aligned to the lane; cite standard sections by **title and link** where helpful.  
6. **Outputs** — Concrete artifacts (e.g. “completed BIS-lite sections 1–7,” “vertical trace table,” “list of seams with PASS/FAIL/DEFERRED”).  
7. **Stop conditions** — When to escalate to a human ([Human in the loop](../standards/human-in-the-loop.md)), when AUTH review is mandatory ([AUTH-aware delivery](auth-aware-delivery.md)).  
8. **Non-goals** — What the skill must **not** do (e.g. raise production privileges, swap AUTH stacks, “fix” without evidence).

Keep skills **short enough to maintain**; deep narrative stays in this repository.

---

## 6. Checklist for skill authors

- [ ] Skill declares a **primary lane** or a **triage → lane** rule.  
- [ ] Skill references **Module / Interface / Seam** when discussing boundaries ([Glossary](glossary.md)).  
- [ ] Skill requires **build intent** or BIS-lite for non-trivial Navigator work ([Build intent specification](build-intent-specification.md)).  
- [ ] Investigator skills require **observable evidence** before architecture-heavy conclusions ([Examples](../examples/evidence-first-defect-resolution.md)).  
- [ ] Sentinel-style skills respect **ontology vs scope** ([Sentinel audit programs](sentinel-audit-programs.md)).  
- [ ] Skill states that **pass/fail** lives in **application tests and CI**, not in the skill text ([Repository scope](repository-scope.md)).  
- [ ] Skill respects **human checkpoints** for irreversible or policy-bound actions ([Human in the loop](../standards/human-in-the-loop.md)).

---

## 7. Integrating skills with your program

**Governance**

- Publish an internal **skill catalog** with owners and review cadence.  
- Version skills when lane rules or templates change.  
- Avoid duplicating long standard text—**link** to this repo or your mirrored copy.

**Quality**

- Peer-review new skills like code: wrong lane routing or missing AUTH gates spreads fast.  
- Add a short **“test plan”** for each skill: what automated check or review proves the skill’s outputs are usable.

**Adoption**

- Run the [Adoption guide](../community/adoption-guide.md) workshop once; then map **each skill** to the templates and glossary entries it depends on.

---

## Related reading

- [What is AADM?](what-is-aadm.md)  
- [Glossary](glossary.md)  
- [Using the AADM MCP](mcp-quickstart.md) — **[www.aadm.io](https://www.aadm.io)** · **`https://mcp.aadm.io/mcp`**  
- [AUTH-aware delivery](auth-aware-delivery.md)  
- [Compliance checklist](compliance-checklist.md)  
- [Repository scope](repository-scope.md)  
