# AADM Standard

**AADM** (**Agentic Authority Delivery Model**) is a delivery model for **outcome-driven agentic systems**: work proceeds with **clear intent**, **architecture boundaries**, **human checkpoints**, and **layer-aware execution**.

This repository is the **public home for the standard**—education, templates, and examples. **AADM is not another prompt framework.** It does not replace your stack’s security model, authorization rules, or application code.

---

## 1. What AADM is

AADM is a **structured way to deliver AI-powered systems** so that speed does not erase accountability. It combines:

- **Written intent** before broad implementation churn  
- A **layered view of delivery** ([UDALI coding personas](docs/udali-personas.md), optional [L1–L22](docs/udali-22-layer-model.md)) so changes land in the right review context  
- **Explicit human authority** for sensitive or irreversible decisions  
- **Shared vocabulary** ([Glossary](docs/glossary.md)) so reviews stay anchored—Module / Interface / Seam, UDALI, AUTH, delivery lanes (**Navigator**, **Investigator**, **Sentinel**)

It is descriptive guidance you can adopt in policies, tickets, and architecture reviews. **Proof stays in your application:** automated tests, CI gates, and operational controls—not in prose alone (see [Architecture principles](docs/architecture-principles.md), [Repository scope](docs/repository-scope.md)).

---

## 2. Why AADM exists

Agent-assisted engineering collapses distance between “idea” and “diff.” Without shared scaffolding, teams drift into ambiguous ownership, cross-layer fixes, and implicit authorization—often masked by plausible generated output.

AADM exists so organizations can **standardize how agentic work is framed and reviewed**, not so every team reinvents guardrails from scratch.

---

## 3. The problem it solves

| Failure mode | What AADM emphasizes instead |
|--------------|-------------------------------|
| Intent scattered across chats and one-off prompts | **Build intent** and templates that anchor scope |
| Agents effectively “own” architecture decisions | **Architecture boundaries** and layer-aware placement |
| High-risk actions blended into everyday automation | **Human checkpoints** for authority-bound work |
| Reviews that cannot tell what layer was violated | **Layer maps** and UDALI-shaped accountability |

AADM does **not** claim to guarantee compliance or security by documentation alone. Your applications still prove correctness through **code, tests, and operational controls**.

---

## 4. The UDALI delivery model

**UDALI** structures AADM delivery as **five collaboration groupings**—Unifier, Designer, Adapter, Logician, Integrator—so teams can discuss **where** a decision belongs and **who** should scrutinize it. Many teams also use **optional finer slices** (numbered **L1–L22**) inside those groupings.

Together they cover contracts, validation, authorization posture, integration boundaries, persistence, observability, and shipping—so agent-generated changes can be reviewed with the same discipline as human-written ones.

Start here:

- **[UDALI coding personas](docs/udali-personas.md)** — five groupings (accountability spine)  
- **[UDALI 22-layer model](docs/udali-22-layer-model.md)** — optional L1–L22 reference  
- **[UDALI layer boundaries](standards/udali-layer-boundaries.md)**  
- **[Glossary](docs/glossary.md)**

---

## 5. How teams apply the standard

1. **Anchor intent** — PRD / BIS-lite, layer map, acceptance criteria ([templates/](templates/)).  
2. **Speak one vocabulary** — UDALI placement, AUTH themes, and design words (**Module**, **Interface**, **Seam**) from the [Glossary](docs/glossary.md).  
3. **Pick the right delivery lane** — **Navigator** (planned change), **Investigator** (broken vs expected), **Sentinel** (assurance across seams)—three **human-facing** postures, defined in the glossary; they help route reviews and audits without collapsing everything into “bug chat.”  
4. **Prove claims with tests** — tie risky behavior to falsifiable checks and CI; use the [Compliance checklist](docs/compliance-checklist.md) as a self-assessment.  

---

## 6. Using the AADM MCP

**Coding agents** can load AADM context and tools from the **hosted MCP** documented on the product site.

| Resource | Link |
|----------|------|
| **Overview and connection instructions** | **[https://www.aadm.io](https://www.aadm.io)** |
| **MCP endpoint** (use with your client’s MCP config; details on the site) | **`https://mcp.aadm.io/mcp`** |

Repository shortcut (canonical endpoint + reminders): **[Using the AADM MCP](docs/mcp-quickstart.md)**. Full steps stay on **[www.aadm.io](https://www.aadm.io)** so they stay current with the service.

---

## 7. What is included in this repo

| Path | Contents |
|------|----------|
| [`docs/`](docs/) | Concepts, principles, build intent, AUTH, skills alignment, hosted MCP pointer, repository scope, compliance checklist, glossary ([**index**](docs/README.md)) |
| [`standards/`](standards/) | Normative shorts ([**index**](standards/README.md)) |
| [`templates/`](templates/) | Story, use case, PRD-lite, BIS-lite, acceptance criteria, layer map ([**index**](templates/README.md)) |
| [`examples/`](examples/) | Worked artifact sets ([**index**](examples/README.md); e.g. [`ai-task-assistant/`](examples/ai-task-assistant/)) |
| [`community/`](community/) | Adoption guide, roadmap, maintainer contact expectations |

---

## 8. What is intentionally not included

- **MCP server source code** in this repo (the **hosted** service is reached via [www.aadm.io](https://www.aadm.io) and **`https://mcp.aadm.io/mcp`** — see §6 and [Using the AADM MCP](docs/mcp-quickstart.md))  
- **Proprietary enforcement algorithms**, scoring models, or certification materials  
- **Client-specific** templates or confidential playbooks  
- **Paid-only** or **enterprise-only** workflow bundles  

Formal gates remain **your** policies, tests, and CI. This repository stays portable markdown.

---

## 9. Quickstart

1. Read **[What is AADM?](docs/what-is-aadm.md)** and the **[documentation index](docs/README.md)**.  
2. Skim **[Architecture principles](docs/architecture-principles.md)** and **[Build intent specification](docs/build-intent-specification.md)**.  
3. Copy **[templates/](templates/)** (see **[templates/README.md](templates/README.md)**) into your wiki or ticket system; complete **[layer map](templates/layer-map.md)** for your next meaningful change.  
4. Walk **[AUTH-aware delivery](docs/auth-aware-delivery.md)** with your security or platform owners.  
5. **Testing discipline:** define acceptance criteria and wire **CI checks** that fail when boundaries regress—see [Compliance checklist](docs/compliance-checklist.md) §QA and [Repository scope](docs/repository-scope.md).  
6. **Agent skills:** if you package playbooks as skills, align them to delivery lanes and templates—see [Creating and aligning skills with the AADM standard](docs/skills.md).  
7. **Hosted MCP:** connect agents per **[www.aadm.io](https://www.aadm.io)** using endpoint **`https://mcp.aadm.io/mcp`** — see [Using the AADM MCP](docs/mcp-quickstart.md).

---

## 10. Example workflow

1. **Shape intent** — Draft **BIS-lite** + **layer map** for the change (`templates/bis-lite.md`, `templates/layer-map.md`).  
2. **Align product narrative** — Optional **customer story** / **use case** for traceability (`templates/customer-story.md`, `templates/use-case.md`).  
3. **Define done** — **Acceptance criteria** with AUTH and observability checks (`templates/acceptance-criteria.md`).  
4. **Implement with discipline** — Agents (if any) work inside declared scope; code reviews reference the layer map.  
5. **Human checkpoint** — Approvals for authority-bound operations per [`standards/human-in-the-loop.md`](standards/human-in-the-loop.md).  
6. **Verify** — Run the tests and gates your layer map implies; capture evidence links in your ticket or ADR.

See **[examples/ai-task-assistant/](examples/ai-task-assistant/)** for a concrete artifact chain, and **[examples/navigator-readiness-hr-app.md](examples/navigator-readiness-hr-app.md)** for a delivery-shaped thread grounded in contracts and tests.

---

## 11. Contribution guidelines (summary)

- **Scope:** improvements to **public standard content**, templates, and examples—**not** proprietary server code in this repo.  
- **Tone:** authoritative, practical, and evidence-aware; **avoid overclaiming**.  
- **Process:** see **[CONTRIBUTING.md](CONTRIBUTING.md)**; interactions follow **[CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md)**.  
- **Security:** report sensitive issues per **[SECURITY.md](SECURITY.md)**.

---

## 12. Link map — key docs

| Topic | Document |
|-------|----------|
| AADM overview | [docs/what-is-aadm.md](docs/what-is-aadm.md) |
| UDALI (personas + L1–L22) | [docs/udali-personas.md](docs/udali-personas.md), [docs/udali-22-layer-model.md](docs/udali-22-layer-model.md) |
| Principles | [docs/architecture-principles.md](docs/architecture-principles.md) |
| Build intent | [docs/build-intent-specification.md](docs/build-intent-specification.md) |
| AUTH placement | [docs/auth-aware-delivery.md](docs/auth-aware-delivery.md) |
| AADM site & MCP connection | [https://www.aadm.io](https://www.aadm.io) · MCP endpoint `https://mcp.aadm.io/mcp` · [docs/mcp-quickstart.md](docs/mcp-quickstart.md) |
| Repository scope (standard vs runtime) | [docs/repository-scope.md](docs/repository-scope.md) |
| Compliance prompts | [docs/compliance-checklist.md](docs/compliance-checklist.md) |
| Sentinel audits (UDALI vs AADM) | [docs/sentinel-audit-programs.md](docs/sentinel-audit-programs.md) |
| Definitions | [docs/glossary.md](docs/glossary.md) |
| Skills & agent packs | [docs/skills.md](docs/skills.md) |
| Core normative summary | [standards/aadm-core-principles.md](standards/aadm-core-principles.md) |
| Human authority | [standards/human-in-the-loop.md](standards/human-in-the-loop.md) |
| Adoption path | [community/adoption-guide.md](community/adoption-guide.md) |
| Documentation hub | [docs/README.md](docs/README.md) |

---

## License

Documentation and templates are released under the **[MIT License](LICENSE)** unless otherwise noted in a specific file.

---

## Trademark note

“AADM” and related names may be protected for project identity. This license covers **repository content**, not automatic rights to use project names in commerce. Use names in good faith to refer to the standard.
