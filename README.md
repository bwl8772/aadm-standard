# AADM Standard

**AADM** (**Agentic Authority Delivery Model**) is an open delivery standard for software systems built with — and not just for — agentic tools. It provides a stack-agnostic vocabulary, three delivery lanes, numbered control and seam catalogs, and an evidence model that lets human reviewers, audits, and AI coders speak the same language about authority, contracts, and proof.

> **This repository is the source of truth.** Any MCP server, agent skill, IDE plugin, or hosted service that uses the name "AADM" enforces and echoes what's here — see [`GOVERNANCE.md`](GOVERNANCE.md) and [`CONFORMANCE.md`](CONFORMANCE.md). You do **not** need the hosted MCP to use AADM — see [AI coder playbook](docs/ai-coder-playbook.md).

---

## 1. What AADM is

AADM is a **structured way to deliver AI-assisted systems** so that speed does not erase accountability. It combines:

- **Three lanes** for shaping work — Navigator (planned change), Investigator (defect with evidence), Sentinel (assurance across seams). See [`standards/three-lanes.md`](standards/three-lanes.md).
- **AUTH catalog** — 48 obligation controls (AUTH-00..47) grouped into 19 authority domains. See [`standards/auth-catalog.md`](standards/auth-catalog.md).
- **SEAM catalog** — 16 auditable handoff archetypes (SEAM-00..15). See [`standards/seam-catalog.md`](standards/seam-catalog.md).
- **TRACE catalog** — 13 data-contract drift patterns (TRACE-01..13). See [`standards/trace-catalog.md`](standards/trace-catalog.md).
- **UDALI placement** — five collaboration bands (Unifier, Designer, Adapter, Logician, Integrator) and an optional 22-layer lattice (L1–L22). See [`docs/udali-personas.md`](docs/udali-personas.md) and [`docs/udali-22-layer-model.md`](docs/udali-22-layer-model.md).
- **Sentinel attestation geometry** — VERT_*, HORI_*, closure rules. See [`standards/sentinel-program.md`](standards/sentinel-program.md).
- **Evidence model** — citation namespaces and binding rules so findings dedupe across tools, tickets, and audits. See [`standards/evidence-model.md`](standards/evidence-model.md).
- **AGENT catalog** — 9 agentic construction failure classes (AGENT-01..09). See [`standards/agentic-failure-modes.md`](standards/agentic-failure-modes.md).
- **Agent runtime and authority** — the control wrapper, tool contract, run record, autonomy ladder, and the fourth authority (what an actor may change about the system itself). See [`standards/agent-runtime-harness.md`](standards/agent-runtime-harness.md) and [`standards/development-authority.md`](standards/development-authority.md).

AADM is **descriptive guidance and a normative catalog**. Proof of correctness stays in **your application** — automated tests, CI gates, operational controls.

---

## 2. Why AADM exists

Agent-assisted engineering collapses the distance between idea and diff. Without shared scaffolding, teams drift into ambiguous ownership, cross-layer fixes, and implicit authorization — often masked by plausible generated output.

AADM exists so organizations can standardize how agentic work is framed, classified, and reviewed — not so every team reinvents guardrails from scratch.

---

## 3. The problem it solves

| Failure mode | What AADM emphasizes instead |
|--------------|-------------------------------|
| Intent scattered across chats and one-off prompts | **Navigator Scope** — written intent, explicit non-goals |
| Defects "fixed" without diagnosis | **Investigator Stage 1** — evidence and anchors before classification |
| Reviews that cannot tell what was actually tested | **Sentinel** — vertical traces, horizontal matrices, anchored evidence |
| Agents effectively "own" architecture decisions | **UDALI bands** and AUTH controls bound to specific layers |
| High-risk actions blended into everyday automation | **Human checkpoints** at each lane stage; AI-coder hard stops |
| AUTH theme as marketing prose | **AUTH-00..47** with named obligation per control |
| Generic "compliance score" replacing real tests | **AUTH-24 (Verification)** — application tests own the verdict |

---

## 4. The three lanes

AADM organizes all delivery work into three lanes. Pick one, then run its three stages with a human gate at each.

| Lane | Primary question | Stages |
|------|------------------|--------|
| **Navigator** | What planned change are we making, and are we gated to build it? | Scope → Verify → Readiness |
| **Investigator** | What broke versus expected, and what is the evidence-gated fix scope? | Investigate & evidence → Diagnose & classify → Scoped remediation |
| **Sentinel** | Does the system already match AUTH, UDALI, gates, and evidence — vertically and horizontally? | Scope & frame → Trace & bind evidence → Attest readiness |

Lanes do not collapse into each other. Defect-shaped work goes to Investigator first, even when wrapped in a Navigator request. See [`standards/three-lanes.md`](standards/three-lanes.md).

---

## 5. The UDALI delivery model

**UDALI** structures AADM delivery as **five collaboration bands** — Unifier, Designer, Adapter, Logician, Integrator — so teams can discuss *where* a decision belongs and *who* should scrutinize it. Many teams also use **optional finer slices** (numbered L1–L22) inside those groupings.

Together they cover contracts, validation, authorization posture, integration boundaries, persistence, observability, and shipping. The [`udali-auth-enforcement-mapping.md`](standards/udali-auth-enforcement-mapping.md) shows which AUTH controls live at which layer.

Start here:

- [`docs/udali-personas.md`](docs/udali-personas.md) — five groupings
- [`docs/udali-22-layer-model.md`](docs/udali-22-layer-model.md) — optional L1–L22 reference
- [`standards/udali-layer-boundaries.md`](standards/udali-layer-boundaries.md) — boundary discipline
- [`standards/udali-auth-enforcement-mapping.md`](standards/udali-auth-enforcement-mapping.md) — UDALI ↔ AUTH placement

---

## 6. How teams apply the standard

1. **Pick the right lane.** Read [`standards/three-lanes.md`](standards/three-lanes.md). The lane choice shapes everything that follows.
2. **Speak one vocabulary.** UDALI bands, AUTH controls, SEAM archetypes, TRACE patterns, VERT_/HORI_ closure — see [`docs/glossary.md`](docs/glossary.md) and [`standards/evidence-model.md`](standards/evidence-model.md).
3. **Map your work.** For each change, identify the AUTH controls touched, the SEAM crossings involved, and the UDALI bands materially affected.
4. **Prove claims with tests.** Per AUTH-24, every AUTH obligation has a paired test or runtime check. CI is authoritative.
5. **Use the AI-coder playbook.** [`docs/ai-coder-playbook.md`](docs/ai-coder-playbook.md) gives copy-paste prompts that work with any agent.

---

## 7. Using AADM with any AI coder (no MCP required)

The standard is **self-contained markdown**. Any AI coder that can read attached files can apply AADM:

- Pick a lane ([`standards/three-lanes.md`](standards/three-lanes.md)).
- Attach the relevant standard files to your agent's context.
- Use the prompts in [`docs/ai-coder-playbook.md`](docs/ai-coder-playbook.md).
- Verify outputs against [`standards/evidence-model.md`](standards/evidence-model.md).

There is **no required tooling**. Adopters who use the hosted AADM MCP gain convenience; adopters who don't lose nothing.

| Topic | Link |
|-------|------|
| Tool-agnostic AI coder playbook | [`docs/ai-coder-playbook.md`](docs/ai-coder-playbook.md) |
| Hosted MCP (optional) | [`docs/mcp-quickstart.md`](docs/mcp-quickstart.md) · [www.aadm.io](https://www.aadm.io) |

---

## 8. Governance — the standard governs implementations

AADM is implemented by multiple surfaces today (hosted MCP, agent skills, IDE plugins, documentation portals). Without a written order of precedence, those implementations drift. The repository sets that order:

1. Statutory law and regulated control frameworks (HIPAA, GDPR, SOX, FedRAMP, etc.).
2. Your organization's own written, ratified standards.
3. **This repository** (`aadm-standard`).
4. Implementations (MCP servers, agent skills, IDE plugins).
5. Generated agent content.

Full charter and conformance contract:

- [`GOVERNANCE.md`](GOVERNANCE.md) — order of precedence; what every implementation must guarantee.
- [`CONFORMANCE.md`](CONFORMANCE.md) — testable obligations for AADM-conformant tooling.

---

## 9. What is included in this repo

| Path | Contents |
|------|----------|
| [`GOVERNANCE.md`](GOVERNANCE.md), [`CONFORMANCE.md`](CONFORMANCE.md) | Governance charter and conformance contract |
| [`docs/`](docs/) | Concepts, principles, build intent, AUTH, skills, MCP quickstart, AI coder playbook, compliance checklist, glossary ([**index**](docs/README.md)) |
| [`standards/`](standards/) | Normative catalogs and workflows ([**index**](standards/README.md)) |
| [`templates/`](templates/) | Story, use case, PRD-lite, BIS-lite, acceptance criteria, layer map ([**index**](templates/README.md)) |
| [`examples/`](examples/) | Worked artifact sets ([**index**](examples/README.md)) |
| [`community/`](community/) | Adoption guide, roadmap, maintainer expectations |

---

## 10. What is intentionally not included

- **MCP server source code.** The hosted convenience service lives at [www.aadm.io](https://www.aadm.io). This repo defines the standard the MCP must follow.
- **Proprietary scoring or certification.** AADM does not score adopter repositories.
- **Client-specific templates or confidential playbooks.**
- **Paid-only workflow bundles.** The standard itself is MIT-licensed and must remain reachable without payment ([`GOVERNANCE.md`](GOVERNANCE.md) §4).

Formal gates remain **your** policies, tests, and CI. This repository stays portable markdown.

---

## 11. Quickstart

1. Read [`docs/what-is-aadm.md`](docs/what-is-aadm.md) and [`GOVERNANCE.md`](GOVERNANCE.md).
2. Read [`standards/three-lanes.md`](standards/three-lanes.md) — pick a lane for your next piece of work.
3. Skim [`standards/auth-catalog.md`](standards/auth-catalog.md), [`standards/seam-catalog.md`](standards/seam-catalog.md), [`standards/evidence-model.md`](standards/evidence-model.md).
4. Open [`docs/ai-coder-playbook.md`](docs/ai-coder-playbook.md) and paste the relevant lane's prompt to your AI coder.
5. Wire CI checks that fail when your declared AUTH obligations regress — see [`docs/compliance-checklist.md`](docs/compliance-checklist.md).
6. Optional: connect your agent to the hosted MCP per [`docs/mcp-quickstart.md`](docs/mcp-quickstart.md).

---

## 12. Example workflow — Navigator slice

1. **Scope.** BIS-lite + layer map for the change; explicit non-goals. See [`templates/bis-lite.md`](templates/bis-lite.md), [`templates/layer-map.md`](templates/layer-map.md).
2. **Verify.** Acceptance criteria; AUTH controls in scope mapped to tests; readiness signals; contracts sketched. See [`templates/acceptance-criteria.md`](templates/acceptance-criteria.md).
3. **Readiness.** Human approval recorded; CI green; rollback plan. See [`standards/three-lanes.md`](standards/three-lanes.md) §Readiness.

See [`examples/ai-task-assistant/`](examples/ai-task-assistant/) for a concrete artifact chain and [`examples/navigator-readiness-hr-app.md`](examples/navigator-readiness-hr-app.md) for a delivery-shaped thread.

---

## 13. Link map — key documents

| Topic | Document |
|-------|----------|
| Governance charter | [`GOVERNANCE.md`](GOVERNANCE.md) |
| Conformance contract | [`CONFORMANCE.md`](CONFORMANCE.md) |
| Three lanes (Navigator/Investigator/Sentinel) | [`standards/three-lanes.md`](standards/three-lanes.md) |
| AUTH catalog (AUTH-00..47) | [`standards/auth-catalog.md`](standards/auth-catalog.md) |
| SEAM catalog (SEAM-00..15) | [`standards/seam-catalog.md`](standards/seam-catalog.md) |
| TRACE catalog (TRACE-01..13) | [`standards/trace-catalog.md`](standards/trace-catalog.md) |
| HANDOFF patterns (HANDOFF-01..08) | [`standards/handoff-patterns.md`](standards/handoff-patterns.md) |
| Sentinel attestation closure (VERT_/HORI_) | [`standards/sentinel-program.md`](standards/sentinel-program.md) |
| AGENT failure modes (AGENT-01..09) | [`standards/agentic-failure-modes.md`](standards/agentic-failure-modes.md) |
| Agent runtime harness (control wrapper) | [`standards/agent-runtime-harness.md`](standards/agent-runtime-harness.md) |
| Agent tool design (tool contract) | [`standards/agent-tool-design.md`](standards/agent-tool-design.md) |
| Agent context and state | [`standards/agent-context-and-state.md`](standards/agent-context-and-state.md) |
| Agent run record (AgentRun) | [`standards/agent-run-record.md`](standards/agent-run-record.md) |
| Agent evaluation | [`standards/agent-evaluation.md`](standards/agent-evaluation.md) |
| Multi-agent restraint | [`standards/multi-agent-restraint.md`](standards/multi-agent-restraint.md) |
| Development authority (fourth authority) | [`standards/development-authority.md`](standards/development-authority.md) |
| Human in the loop + autonomy ladder | [`standards/human-in-the-loop.md`](standards/human-in-the-loop.md) |
| UDALI ↔ AUTH enforcement mapping | [`standards/udali-auth-enforcement-mapping.md`](standards/udali-auth-enforcement-mapping.md) |
| Evidence model | [`standards/evidence-model.md`](standards/evidence-model.md) |
| Universal engineering truths | [`standards/universal-engineering-truths.md`](standards/universal-engineering-truths.md) |
| Engineering doctrine top 10 | [`standards/engineering-doctrine-top-10.md`](standards/engineering-doctrine-top-10.md) |
| AI-coder hard stops | [`standards/ai-coder-hard-stops.md`](standards/ai-coder-hard-stops.md) |
| Module–Interface–Seam vocabulary | [`standards/module-interface-seam-vocabulary.md`](standards/module-interface-seam-vocabulary.md) |
| AI coder playbook (tool-agnostic) | [`docs/ai-coder-playbook.md`](docs/ai-coder-playbook.md) |
| Hosted MCP (optional) | [`docs/mcp-quickstart.md`](docs/mcp-quickstart.md) · [www.aadm.io](https://www.aadm.io) |
| AADM overview | [`docs/what-is-aadm.md`](docs/what-is-aadm.md) |
| UDALI personas + L1–L22 | [`docs/udali-personas.md`](docs/udali-personas.md), [`docs/udali-22-layer-model.md`](docs/udali-22-layer-model.md) |
| Glossary | [`docs/glossary.md`](docs/glossary.md) |

---

## 14. Contribution

- **Scope.** Improvements to public standard content, templates, and examples — not proprietary server code in this repo.
- **Tone.** Authoritative, practical, evidence-aware. Avoid overclaiming.
- **Process.** See [`CONTRIBUTING.md`](CONTRIBUTING.md); interactions follow [`CODE_OF_CONDUCT.md`](CODE_OF_CONDUCT.md).
- **Security.** Report sensitive issues per [`SECURITY.md`](SECURITY.md).

Substantive changes to the standard — new AUTH control, new SEAM, new lane stage, new doctrine slice — require a written rationale and backwards-compatibility note. Numeric ids (`AUTH-NN`, `SEAM-NN`, `TRACE-NN`, `HANDOFF-NN`, `AGENT-NN`, `L`-numbers, `SIGL-NN`) are **append-only**; retired ids stay in the catalog marked `RETIRED`.

---

## License

Documentation and templates are released under the [MIT License](LICENSE) unless otherwise noted in a specific file.

---

## Trademark note

"AADM" and related project marks may be protected for project identity. This license covers repository content, not automatic rights to use project names in commerce. Using the AADM name to describe an implementation that does not meet [`CONFORMANCE.md`](CONFORMANCE.md) is a trademark concern, independent of the content license.
