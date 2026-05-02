# Human in the loop

Certain decisions must remain **human-governed**. Automation may prepare, propose, or simulate—but **approval** stays with accountable owners.

## Categories requiring explicit human approval

Organizations should tune this list; representative triggers include:

- Production schema destruction or bulk data deletion  
- Financial movements or irreversible billing changes  
- Issuing powerful credentials or broad role grants  
- Binding legal commitments or customer-visible contractual changes  
- Safety-critical controls in regulated environments  

## Operating rules

1. **Dual control where policy demands it**—no single unattended automation path.  
2. **Evidence of review**—who approved, when, and under what artifact version.  
3. **Scoped automation**—agents receive least privilege consistent with the prepared change.  
4. **Rollback readiness**—humans understand how to revert or mitigate before approval.

## Relationship to AI agents

Agents accelerate drafting; they do **not** inherit moral or legal accountability for organizational actions. Maintain clear **separation between suggestion and execution**.

For delivery patterns, see [`docs/architecture-principles.md`](../docs/architecture-principles.md).
