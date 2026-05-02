# Human in the loop

A foundational AADM principle. Companion: [`aadm-core-principles.md`](aadm-core-principles.md) §2.

---

## Definition

**Human-in-the-loop (HITL)** means explicit **checkpoints** where accountable people **approve, reject, or attest** before irreversible or high-risk effects occur. Automation—including AI—may **draft, simulate, or recommend**; it does not inherit **organizational authority** for those checkpoints unless policy explicitly says otherwise.

---

## Why it matters

Models and scripts do not carry regulatory, contractual, or fiduciary accountability. When sensitive actions run unattended, organizations lose traceability and invite irreversible harm (data loss, privilege misuse, financial movement).

HITL preserves **traceable judgment** aligned to risk.

---

## What good looks like

- Written **triggers** (“what requires human approval”).  
- Named **roles** responsible for approval—not vague “someone reviewed.”  
- **Evidence**: ticket links, audit entries, or signed artifacts tied to releases.  
- **Least privilege** for automation paths leading up to the checkpoint.

---

## Common failure mode

Treating “the AI agreed” or “the pipeline passed” as approval for production schema drops, org-wide permission grants, or contractual commitments—without a human accountable party matching policy.

---

## Public checklist

- [ ] Inventory high-risk actions for your product class (see starter list below).  
- [ ] Map each to required approver role(s) and system gates.  
- [ ] Ensure agents/tools cannot bypass gates via alternate APIs or credentials.  
- [ ] Record approvals with enough context to reconstruct decisions later.  
- [ ] Verify rollback or mitigation paths exist **before** approving irreversible steps.

---

## Starter categories (tune to your policy)

Representative triggers—**not exhaustive**:

- Bulk deletion or destructive migrations  
- Issuance of powerful credentials or broad role grants  
- Financial or billing-impacting changes  
- Customer-visible contractual or pricing commitments  
- Safety- or compliance-critical configuration in regulated contexts  

---

## Relationship to AI agents

Agents accelerate preparation; they **do not replace** named approvers for governed actions. Maintain a crisp line between **suggestion** (draft PR, proposed plan) and **execution** (merge to production, apply migration).

---

## Further reading

- [`docs/auth-aware-delivery.md`](../docs/auth-aware-delivery.md)  
- [`templates/bis-lite.md`](../templates/bis-lite.md) §12 Human checkpoint  
