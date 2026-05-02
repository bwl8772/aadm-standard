# No monolith rule

A **monolith**, in the AADM sense, is an undifferentiated accumulation of unrelated responsibilities behind a single boundary—often accelerated when automation adds code without structural discipline.

## Standard

Teams SHALL prevent unrelated concerns from merging into a single unmaintainable module or service **without an explicit architectural decision** recording:

- Why consolidation is justified  
- What boundaries remain inside the unit  
- How AUTH, data, and observability remain intelligible  

## Guidance

- Prefer **bounded contexts** with explicit contracts.  
- Refactor when module cohesion drops—early confusion compounds under AI churn.  
- Keep **integration adapters** thin; domain logic should not become a patchwork of provider-specific branches.

## Signals you may be drifting

- Files or packages named “utils,” “common,” or “helpers” growing without ownership  
- Cyclic dependencies spanning domains  
- Authorization checks duplicated inconsistently across many entrypoints  

For principles, see [`docs/architecture-principles.md`](../docs/architecture-principles.md).
