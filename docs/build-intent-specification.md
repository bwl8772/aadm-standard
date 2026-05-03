# Build intent specification

A **build intent specification** is the **bridge** between **product intent**, **architecture guardrails**, and **engineering execution**.

It answers—early and in one place—**what is actually being built**, **under what constraints**, and **what “done” means**, so implementation (including agent-assisted coding) does not collapse into guesswork.

---

## Three anchors

| Anchor | What it does |
|--------|----------------|
| **PRD (or PRD-lite)** | **Authorizes work** — ties initiative to outcomes and stakeholder agreement to spend effort here. |
| **Architecture** | **Constrains work** — patterns, non-negotiables, risk boundaries, and compatibility expectations engineering must respect. |
| **BIS (build intent specification)** | **Tells engineering what is actually being built** — concrete capability scope, acceptance signals, AUTH/data assumptions, layer placement, and checkpoints—aligned to PRD and architecture. |

None of these replaces the others. **Product** decides *whether* and *why*; **architecture** decides *what must always stay true*; **engineering** delivers *what* within that shared frame.

---

## Ownership split (who owns what)

### Product owns

- **Outcomes** — the change should produce observable value or risk reduction.  
- **Scope boundaries** — what is in and explicitly out (prevents silent creep).  
- **User intent** — who is served and what they are trying to accomplish.  
- **Success definition** — how you know the capability succeeded (metrics, qualitative signals, acceptance themes).

Product expresses this through artifacts such as a PRD, roadmap item, or [PRD-lite](../templates/prd-lite.md)—whatever your program uses as **authorization to build**.

### Architecture owns

- **Guardrails** — boundaries that protect integrity (AUTH posture, layering, integration rules).  
- **Patterns** — preferred ways to extend the system so it stays evolvable.  
- **Constraints** — performance, residency, compatibility, security expectations that apply regardless of feature enthusiasm.  
- **Risk boundaries** — known cliffs (data corruption, privilege escalation, blast radius) called out before coding spreads assumptions.

Architecture signs off at the level your organization requires—architecture review, RFC, standards checklist, or equivalent.

### Engineering owns

- **Execution within the shared build intent** — design details, tasks, tests, deployment mechanics—**without guessing** scope, success, or guardrails.

If engineering must invent product outcomes or rewrite constraints mid-flight, the **build intent is incomplete** or **out of date**: revise PRD/architecture/BIS rather than routing around them only in pull requests.

---

## Why a BIS-lite helps

Full specifications are not always warranted. **[BIS-lite](../templates/bis-lite.md)** is a **starter-level** sheet that still forces alignment:

- One capability, clear outcome, explicit in/out scope  
- User story and acceptance criteria tied to product success  
- [UDALI](udali-22-layer-model.md) layer map so reviews land on the right accountability buckets  
- AUTH and data/schema assumptions stated plainly  
- Known risks and **human checkpoints** for authority-bound work  
- Optional note on **MCP validation status** when agents consume standard context from an external server—without treating MCP as a substitute for application security  

Use BIS-lite when the change spans services, touches AUTH or contracts, or involves agent-generated implementation.

---

## Relationship to other artifacts

| Artifact | Role |
|----------|------|
| [PRD-lite](../templates/prd-lite.md) | Product-facing breadth; authorizes direction |
| [BIS-lite](../templates/bis-lite.md) | Shared build intent for engineering |
| [Customer story](../templates/customer-story.md) | Narrative slice from the user’s perspective |
| [Use case](../templates/use-case.md) | Structured interaction description |
| [Layer map](../templates/layer-map.md) | Detailed [UDALI](udali-22-layer-model.md) placement (can nest inside BIS-lite) |
| [Acceptance criteria](../templates/acceptance-criteria.md) | May mirror or extend section 7 of BIS-lite |

---

## Cadence

- **Draft the BIS-lite early** — wrong is OK; silent is not.  
- **Revise when PRD or architecture guardrails change** — keep one thread of truth.  
- **Link tasks** — tickets reference capability name and BIS-lite section IDs where helpful.

For a worked example of adjacent artifacts, see [`examples/ai-task-assistant/`](../examples/ai-task-assistant/).

---

## Further reading

- [Architecture principles](architecture-principles.md)  
- [AUTH-aware delivery](auth-aware-delivery.md)  
- [UDALI 22-layer model](udali-22-layer-model.md)  
- [MCP quickstart](mcp-quickstart.md)  
