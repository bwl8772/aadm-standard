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
| [human-in-the-loop.md](human-in-the-loop.md) | Human approval for high-risk actions |
| [no-monolith-rule.md](no-monolith-rule.md) | Avoid undifferentiated lumping |
| [universal-engineering-truths.md](universal-engineering-truths.md) | Twenty stack-agnostic truths |
| [universal-coding-principles.md](universal-coding-principles.md) | Skeptical phase-by-phase checklist |
| [engineering-doctrine-top-10.md](engineering-doctrine-top-10.md) | Priority-ordered doctrine (damage-prevention order) |
| [ai-coder-hard-stops.md](ai-coder-hard-stops.md) | Actions an AI coder must refuse |

## Catalogs (numbered, append-only)

| File | Topic |
|------|-------|
| [auth-catalog.md](auth-catalog.md) | AUTH-00..47 — obligations grouped by authority domain |
| [seam-catalog.md](seam-catalog.md) | SEAM-00..15 — auditable handoff archetypes |
| [trace-catalog.md](trace-catalog.md) | TRACE-01..12 — data-contract drift patterns |
| [handoff-patterns.md](handoff-patterns.md) | HANDOFF-01..06 — cross-layer defect classes |

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
