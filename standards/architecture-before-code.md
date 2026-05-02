# Architecture before code

A foundational AADM principle. Companion: [`aadm-core-principles.md`](aadm-core-principles.md) §1.

---

## Definition

When ambiguity or blast radius is significant, teams clarify **architecture intent**—boundaries, contracts, authorization posture, and how correctness will be demonstrated—**before** scaling implementation across many files or services.

Spikes and prototypes remain welcome; they must be **honest experiments**, not accidental foundations.

---

## Why it matters

Under schedule pressure (including agent-assisted coding), code spreads faster than shared mental models. Early mistakes become implicit dependencies: wrong DTOs, hidden coupling, and partial AUTH stories that every later PR pays interest on.

Architecture-first behavior buys **cheaper rework** by forcing naming and boundaries while change is still cheap.

---

## What good looks like

- A short **build intent** (for example [`templates/bis-lite.md`](../templates/bis-lite.md)) exists before wide coding.  
- **Contracts** at edges are sketched and reviewed when multiple teams touch them.  
- **Layer map** captures primary accountability groupings for the change.  
- Prototypes are **time-boxed**, labeled, and either discarded or promoted through review—not silently merged as “the way.”

---

## Common failure mode

Shipping a demo branch that “already works” without reconciling schema compatibility, multi-tenant boundaries, or rollback—then declaring architecture debt “future work” that never schedules.

---

## Public checklist

- [ ] Problem and outcome documented ([`docs/build-intent-specification.md`](../docs/build-intent-specification.md)).  
- [ ] Contracts at exposed boundaries identified (API, events, jobs).  
- [ ] AUTH-sensitive operations listed before implementation scales.  
- [ ] [`templates/layer-map.md`](../templates/layer-map.md) drafted for non-trivial changes.  
- [ ] Spikes have an expiry: promote with review or delete.

---

## Practical notes

**Minimum before scaling**

- Contract sketches  
- Verification strategy by risk (tests, monitors, manual gates)  
- Rollback or feature-flag posture if deployment risk warrants  

**Promotion**

Promote experimental code when contracts, tests, and operational readiness align—not merely when a stakeholder demo succeeds.

---

## Further reading

- [`docs/architecture-principles.md`](../docs/architecture-principles.md)  
- [`docs/what-is-aadm.md`](../docs/what-is-aadm.md)  
