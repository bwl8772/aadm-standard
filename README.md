# AADM Standard

**AADM** (**Agentic Authority Delivery Model**) is a delivery model for **outcome-driven agentic systems**: work proceeds with **clear intent**, **architecture boundaries**, **human checkpoints**, and **layer-aware execution**.

This repository is the **public home for the standard**—education, templates, and examples. **AADM is not another prompt framework.** It does not replace your stack’s security model, authorization rules, or application code.

---

## 1. What AADM is

AADM is a **structured way to deliver AI-powered systems** so that speed does not erase accountability. It combines:

- **Written intent** before broad implementation churn  
- A **layered view of delivery** (UDALI) so changes land in the right review context  
- **Explicit human authority** for sensitive or irreversible decisions  
- **Separation of concerns** between **human-readable standard artifacts** (this repo) and **agent-distributed context** (typically an **AADM MCP server** you connect from your IDE)

It is descriptive guidance you can adopt in policies, tickets, and architecture reviews. **Teams using coding agents benefit from both:** markdown templates here for humans *and* an MCP deployment so agents load **consistent** definitions, prompts, and bounded tools—not guesses from stale snippets.

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

**UDALI** structures AADM delivery as **five collaboration groupings**—Unifier, Designer, Adapter, Logician, Integrator (**I** in UDALI; not “implementer”)—so teams can discuss **where** a decision belongs and **who** should scrutinize it. Many teams also use **optional finer slices** (sometimes discussed as ~22 concerns) inside those groupings.

Together they cover contracts, validation, authorization posture, integration boundaries, persistence, observability, and shipping—so agent-generated changes can be reviewed with the same discipline as human-written ones.

Start here:

- **[UDALI 22-layer model](docs/udali-22-layer-model.md)**  
- **[UDALI layer boundaries](standards/udali-layer-boundaries.md)**  
- **[Glossary](docs/glossary.md)**

---

## 5. How this public repo relates to an AADM MCP server

| **This repository (public)** | **AADM MCP server (separate distribution)** |
|------------------------------|---------------------------------------------|
| Standard narrative, education, templates, examples | **Agent-facing surface** for loading curated standard context (and, where provided, workflows) over MCP |
| Something you **read, fork, and cite** | Something you **run and connect** from an MCP-capable client |
| No requirement to use any particular server | Purpose-built to give agents **stable resources, prompts, and tools** tied to the standard—so adoption is consistent across repos |

### Why teams still run an AADM MCP server

Static docs alone are easy to misquote or drift from in prompts. A server **does not replace** this repository; it **distributes** what the standard means to agents in a **controlled, versionable** way. Typical benefits:

- **Same vocabulary** in every session—UDALI, AUTH, build intent—without pasting large files into chats  
- **Three equal agent workflows** on reference deployments—**Navigator** (delivery), **Investigator** (defects), **Sentinel** (assurance across seams)—so framing is not troubleshoot-only; see [Glossary](docs/glossary.md) and [MCP quickstart](docs/mcp-quickstart.md)  
- **Prompt and workflow surfaces** aligned to how your organization applies AADM  
- **Bounded tools** for diagnostics or mapping (capabilities vary by deployment—see publisher docs)

**Important clarifications:**

- This repo **does not ship** MCP server source, binaries, or private implementation details—those live in the **MCP project / distribution** your team runs or obtains from a **trusted provider**.  
- MCP supplies **context and agent-facing workflows**, not **your** product’s business authorization—application code and gateways remain authoritative (see [AUTH-aware delivery](docs/auth-aware-delivery.md)).  
- Exact behavior depends on **which** MCP deployment you use; read its publisher’s documentation.  
- **Reference tool ids** on the open enforcement MCP bundle (**`standard_brief`**, **`debug_defect`**, **`route_lane`**, etc.) are listed in [MCP quickstart](docs/mcp-quickstart.md#reference-aadm-enforcement-mcp-tool-ids), with a short **legacy-name** mapping for older docs.

---

## 6. What is included in this repo

| Path | Contents |
|------|----------|
| [`docs/`](docs/) | Concepts, principles, build intent, AUTH, MCP quickstart, compliance checklist, glossary ([**index**](docs/README.md)) |
| [`standards/`](standards/) | Normative shorts ([**index**](standards/README.md)) |
| [`templates/`](templates/) | Story, use case, PRD-lite, BIS-lite, acceptance criteria, layer map ([**index**](templates/README.md)) |
| [`examples/`](examples/) | Worked artifact sets ([**index**](examples/README.md); e.g. [`ai-task-assistant/`](examples/ai-task-assistant/)) |
| [`community/`](community/) | Adoption guide, roadmap, maintainer contact expectations |

---

## 7. What is intentionally not included

- **MCP server source code, binaries, or internal implementation details**  
- **Proprietary enforcement algorithms**, scoring models, or certification materials  
- **Client-specific** templates or confidential playbooks  
- **Paid-only** or **enterprise-only** workflow bundles  

If you want **agent connectivity**, **consistent standard loading**, or **workflow tooling** beyond static markdown, plan for an **AADM MCP server deployment** (run or hosted) **alongside** this repo—not instead of it. Application enforcement remains in your services and CI.

---

## 8. Quickstart

1. Read **[What is AADM?](docs/what-is-aadm.md)** and the **[documentation index](docs/README.md)**.  
2. Skim **[Architecture principles](docs/architecture-principles.md)** and **[Build intent specification](docs/build-intent-specification.md)**.  
3. Copy **[templates/](templates/)** (see **[templates/README.md](templates/README.md)**) into your wiki or ticket system; complete **[layer map](templates/layer-map.md)** for your next meaningful change.  
4. Walk **[AUTH-aware delivery](docs/auth-aware-delivery.md)** with your security or platform owners.  
5. **For AI-assisted development:** connect your environment to **an AADM MCP server** using **[MCP quickstart](docs/mcp-quickstart.md)**—least-privilege credentials only; never substitute MCP access for production AUTH.

---

## 9. Example workflow

1. **Shape intent** — Draft **BIS-lite** + **layer map** for the change (`templates/bis-lite.md`, `templates/layer-map.md`).  
2. **Align product narrative** — Optional **customer story** / **use case** for traceability (`templates/customer-story.md`, `templates/use-case.md`).  
3. **Define done** — **Acceptance criteria** with AUTH and observability checks (`templates/acceptance-criteria.md`).  
4. **Implement with discipline** — Agents (if any) work inside declared scope; code reviews reference the layer map.  
5. **Human checkpoint** — Approvals for authority-bound operations per [`standards/human-in-the-loop.md`](standards/human-in-the-loop.md).  
6. **MCP (recommended for agent-heavy teams)** — Wire your IDE to **an AADM MCP server** so agents pull **canonical** standard context; merge requests and tests still gate quality—MCP does not auto-ship code.

See **[examples/ai-task-assistant/](examples/ai-task-assistant/)** for a concrete artifact chain.

---

## 10. Contribution guidelines (summary)

- **Scope:** improvements to **public standard content**, templates, and examples—**not** proprietary server code in this repo.  
- **Tone:** authoritative, practical, and evidence-aware; **avoid overclaiming**.  
- **Process:** see **[CONTRIBUTING.md](CONTRIBUTING.md)**; interactions follow **[CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md)**.  
- **Security:** report sensitive issues per **[SECURITY.md](SECURITY.md)**.

---

## 11. Link map — key docs

| Topic | Document |
|-------|----------|
| AADM overview | [docs/what-is-aadm.md](docs/what-is-aadm.md) |
| UDALI layers | [docs/udali-22-layer-model.md](docs/udali-22-layer-model.md) |
| Principles | [docs/architecture-principles.md](docs/architecture-principles.md) |
| Build intent | [docs/build-intent-specification.md](docs/build-intent-specification.md) |
| AUTH placement | [docs/auth-aware-delivery.md](docs/auth-aware-delivery.md) |
| MCP (generic) | [docs/mcp-quickstart.md](docs/mcp-quickstart.md) |
| MCP reference tool ids | [docs/mcp-quickstart.md#reference-aadm-enforcement-mcp-tool-ids](docs/mcp-quickstart.md#reference-aadm-enforcement-mcp-tool-ids) |
| Compliance prompts | [docs/compliance-checklist.md](docs/compliance-checklist.md) |
| Sentinel audits (UDALI vs AADM) | [docs/sentinel-audit-programs.md](docs/sentinel-audit-programs.md) |
| Definitions | [docs/glossary.md](docs/glossary.md) |
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
