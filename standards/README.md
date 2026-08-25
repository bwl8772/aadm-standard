# Standards

Normative statements, catalogs, and workflows you can adopt or adapt. Full narrative discussion lives in [`docs/`](../docs/). Governance order: this folder is the source of truth, per [`../GOVERNANCE.md`](../GOVERNANCE.md).

## Governance

| File | Topic |
|------|-------|
| [`../GOVERNANCE.md`](../GOVERNANCE.md) | Governance charter — standard governs implementations |
| [`../CONFORMANCE.md`](../CONFORMANCE.md) | Conformance contract for AADM tooling |

## Core principles

| File | Topic |
|------|-------|
| [aadm-core-principles.md](aadm-core-principles.md) | Eight core principles with checklists |
| [architecture-before-code.md](architecture-before-code.md) | Intent before scaled implementation |
| [human-in-the-loop.md](human-in-the-loop.md) | Human approval for high-risk actions; the seven-rung autonomy ladder |
| [no-monolith-rule.md](no-monolith-rule.md) | Avoid undifferentiated lumping |
| [universal-engineering-truths.md](universal-engineering-truths.md) | Twenty stack-agnostic truths |
| [universal-coding-principles.md](universal-coding-principles.md) | Skeptical phase-by-phase checklist |
| [engineering-doctrine-top-10.md](engineering-doctrine-top-10.md) | Priority-ordered doctrine (damage-prevention order) |
| [ai-coder-hard-stops.md](ai-coder-hard-stops.md) | Actions an AI coder must refuse |

## Catalogs (numbered, append-only)

| File | Topic |
|------|-------|
| [auth-catalog.md](auth-catalog.md) | AUTH-00..55 — obligations grouped by authority domain |
| [seam-catalog.md](seam-catalog.md) | SEAM-00..15 — auditable handoff archetypes |
| [trace-catalog.md](trace-catalog.md) | TRACE-01..13 — data-contract drift patterns |
| [handoff-patterns.md](handoff-patterns.md) | HANDOFF-01..08 — cross-layer defect classes |
| [agentic-failure-modes.md](agentic-failure-modes.md) | AGENT-01..09 — agentic construction failure classes |

## Agent runtime and authority

How to build systems that use agents, and what governs them. Assembles the agentic AUTH controls (AUTH-44..47, AUTH-53..54) and the AI assurance controls (AUTH-48..52, AUTH-55) plus the runtime concerns the catalogs name individually but no single document assembled.

| File | Topic |
|------|-------|
| [agent-runtime-harness.md](agent-runtime-harness.md) | The control wrapper, the agent loop, reasons vs governs, the autonomy inversion |
| [agent-tool-design.md](agent-tool-design.md) | Tool contract — narrow, typed, classified, idempotent, reversible, audited; never-register list |
| [agent-context-and-state.md](agent-context-and-state.md) | Decision-scoped assembly, trust classification, durable state outside the transcript |
| [agent-run-record.md](agent-run-record.md) | The AgentRun envelope, events, and terminal states |
| [agent-evaluation.md](agent-evaluation.md) | Post-deployment metric catalog; why adaptive routing raises the validation burden |
| [multi-agent-restraint.md](multi-agent-restraint.md) | When a second agent is justified; agent-to-agent authority |
| [development-authority.md](development-authority.md) | The fourth authority — what an actor may change about the system itself |
| [ai-automation-profile.md](ai-automation-profile.md) | AR-01..AR-30 mapped to owning controls, UDALI placement, template fields, evidence, and test/eval type |

## Workflows

| File | Topic |
|------|-------|
| [three-lanes.md](three-lanes.md) | Navigator / Investigator / Sentinel — three lanes, three stages each |
| [sentinel-program.md](sentinel-program.md) | Sentinel attestation closure, VERT_*/HORI_*, audit programs |

## Placement and enforcement

| File | Topic |
|------|-------|
| [udali-layer-boundaries.md](udali-layer-boundaries.md) | Layer accountability |
| [udali-auth-enforcement-mapping.md](udali-auth-enforcement-mapping.md) | L1–L22 × AUTH-NN matrix |
| [evidence-model.md](evidence-model.md) | Citation namespaces and binding rules |

## Architecture vocabulary and patterns

| File | Topic |
|------|-------|
| [module-interface-seam-vocabulary.md](module-interface-seam-vocabulary.md) | Module, Interface, Seam, Adapter, Depth, Leverage, Locality |
| [data-flow-stacks.md](data-flow-stacks.md) | Five MECE pipelines + Realm-Bridge; stack-qualified role grammar |
| [business-logic-placement.md](business-logic-placement.md) | Frontend vs backend authority |
| [shared-backend-separate-pages.md](shared-backend-separate-pages.md) | One workflow, many screens |
| [reuse-before-build.md](reuse-before-build.md) | Stdlib → native → declared dep → custom → new dep |
| [operational-design-heuristics.md](operational-design-heuristics.md) | Singletons; repeated bugs and asks as signals |

## Seams and signals

| File | Topic |
|------|-------|
| [guidance-feedback-seam.md](guidance-feedback-seam.md) | One verdict → one resolver → one affordance, every surface |
| [signal-stack-compliance.md](signal-stack-compliance.md) | Twelve SIGL rows × N surface SURF columns |

---

These documents do **not** constitute legal or regulatory certification. Formal gates are defined by **your** policies, tests, and CI.
