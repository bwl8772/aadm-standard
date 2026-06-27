# HANDOFF pattern catalog

> Cross-layer handoff bug classes — stable `HANDOFF-NN` ids. Distinct from `TRACE-NN` (contract drift) and `SEAM-NN` (auditable handoff archetypes). Stack-agnostic.

## When to use HANDOFF ids

`HANDOFF-NN` names a defect *pattern class* that crosses module or layer boundaries — not a single bug. Use it when a defect looks like:

- A write happened, but the downstream cache or query did not refresh.
- A route or handler skipped the normal command/repository boundary.
- Two parts of the codebase have shadow copies of what should be one DTO/enum/normalizer.
- An external side effect ran before the durable write was committed.
- Two purge or wipe paths exist and diverged.
- A multi-step lifecycle is not transaction-bound, leaving inconsistent state on partial failure.

## Catalog

| Id | Gate | Pattern |
|----|------|---------|
| **HANDOFF-01** | `mutation_invalidation` | Write → consumer cache/query invalidation completeness. |
| **HANDOFF-02** | `write_path_bypass` | Route/handler bypasses command/repository boundary. |
| **HANDOFF-03** | `shadow_type` | Duplicate DTO/enum/normalizer export paths. |
| **HANDOFF-04** | `effect_before_persist` | External side effect before durable write. |
| **HANDOFF-05** | `parallel_purge` | Two wipe/purge brains diverge. |
| **HANDOFF-06** | `multi_step_atomicity` | Lifecycle steps not transaction-bound. |

## How findings use these

Combine with AUTH, SEAM, TRACE, VERT_/HORI_ where applicable:

```
HANDOFF-04 + SEAM-05 + AUTH-25 + VERT_APP
```

That says: an external side effect ran before the durable write (HANDOFF-04) at a dispatch/orchestration composition (SEAM-05), violating idempotency obligations (AUTH-25), at the application vertical band (VERT_APP).

## Relationship to other catalogs

| Family | Question it answers |
|--------|---------------------|
| **AUTH-NN** | What obligation must be enforced? |
| **SEAM-NN** | Which crossing must be in scope? |
| **TRACE-NN** | What drift class failed? |
| **HANDOFF-NN** | What multi-step / cross-layer defect pattern explains the failure? |

HANDOFF patterns most often appear during Investigator Stage 2 classification, when the defect crosses multiple layers and no single AUTH or SEAM cleanly names it.

## Related

- [`auth-catalog.md`](auth-catalog.md)
- [`seam-catalog.md`](seam-catalog.md)
- [`trace-catalog.md`](trace-catalog.md)
- [`three-lanes.md`](three-lanes.md)
