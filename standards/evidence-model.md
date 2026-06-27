# Evidence model — citation namespaces and binding rules

> How AADM findings cite obligations, crossings, drift patterns, attestation geometry, layers, and surfaces — so reviewers, agents, ticketing systems, and audits dedupe and align. Stack-agnostic.

## Citation prefixes (canonical)

| Prefix | Role | Source |
|--------|------|--------|
| `AUTH-NN` | Governance control | [`auth-catalog.md`](auth-catalog.md) |
| `SEAM-NN` | Auditable handoff archetype | [`seam-catalog.md`](seam-catalog.md) |
| `TRACE-NN` | Data-contract drift pattern | [`trace-catalog.md`](trace-catalog.md) |
| `HANDOFF-NN` | Cross-layer handoff bug class | [`handoff-patterns.md`](handoff-patterns.md) |
| `VERT_*` | Sentinel vertical attestation seam | [`sentinel-program.md`](sentinel-program.md) |
| `HORI_*` | Sentinel horizontal attestation seam | [`sentinel-program.md`](sentinel-program.md) |
| `L1`–`L22` | UDALI placement layer | [UDALI 22-layer model](../docs/udali-22-layer-model.md) |
| `SIGL-NN` | Signal Stack row (1–12) | [`signal-stack-compliance.md`](signal-stack-compliance.md) |
| `SURF:<name>` | Filled surface column attestation | [`signal-stack-compliance.md`](signal-stack-compliance.md) |

## Combining prefixes

One finding may reference multiple families. This is intentional — each prefix names a different facet.

**Example.**

```
SIGL-10 + SEAM-13 + TRACE-01 + HORI_GUIDANCE_FEEDBACK + AUTH-22 + L15 + SURF:EmployeeProfile
```

That string says: the client mapper row (SIGL-10) at the guidance–feedback seam (SEAM-13) drifted in meaning (TRACE-01), cited as the guidance horizontal kind in attestation closure (HORI_GUIDANCE_FEEDBACK), bound to read-model authority (AUTH-22), at the AI Services layer (L15), on the EmployeeProfile surface (SURF:EmployeeProfile).

## Rules

1. **Do not overload one prefix.** Each prefix has one meaning.
2. **`VERT_*` is not an alternate spelling for `L*`.** Vertical closure bands are coarse; UDALI layers are fine-grained placement.
3. **`SIGL-NN` is not UDALI `L*`.** SIGL-08 (verdict producer row) usually lives at UDALI L13, but the numbers are not interchangeable.
4. **`SEAM-NN` vs `VERT_*` / `HORI_*`.** SEAM names *which crossing types* to include in scope (including transient normalization). VERT/HORI name *attestation geometry* from closure — complementary, not interchangeable.
5. **Evidence anchor goes in the application repository.** A finding without a path, test, or CI link is not bound — it is, at best, a deferred gap.

## Status values for cells in a matrix

| Value | Meaning |
|-------|---------|
| `PROVEN` | Anchor in the repository (path, test, CI) demonstrates the obligation/crossing/row is enforced. |
| `FAILED` | Anchor demonstrates the obligation/crossing/row is **not** enforced; cite TRACE or HANDOFF where applicable. |
| `DEFERRED` | Out of scope for this pass, with **named owner** and stated risk. Owner is mandatory. |
| `n/a` | Explicitly inapplicable to this slice; one-line rationale required. |

Blank cells in a matrix are non-conformant. Every row is one of the four values above.

## What is not evidence

- **Prose.** Documentation, narrative, or this standard's text is **framing**, not evidence.
- **Verbal assurance.** "The team confirmed X works" is not an anchor.
- **External vendor scores.** A score from a third-party tool is *one input* — not a substitute for repo-bound proof.

## Repository ownership

Evidence URLs, paths, and CI pointers live in **host systems** and **application repositories**. AADM does not store audit state.

## Related

- [`GOVERNANCE.md`](../GOVERNANCE.md) — order of precedence.
- [`CONFORMANCE.md`](../CONFORMANCE.md) — testable obligations for any AADM-conformant tool.
- [`sentinel-program.md`](sentinel-program.md) — attestation closure and audit programs.
