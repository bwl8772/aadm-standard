# Acceptance criteria

_Observable conditions for “done.” Tie each item to a use case or BIS-lite capability when possible._

## Feature / change ID

<!-- Capability name or ticket link -->

## Criteria summary

<!-- Optional one-liner -->

---

## Acceptance criteria

### Functional

- [ ] <!-- behavior the user/system can observe -->
- [ ] 

### AUTH / permissions

- [ ] <!-- who may / may not perform the action -->
- [ ] 

### Data / correctness

- [ ] <!-- integrity, formatting, edge cases -->
- [ ] 

### Reliability / UX

- [ ] <!-- errors, empty states, degraded behavior -->
- [ ] 

### Observability / audit (if applicable)

- [ ] <!-- logs, metrics, audit events -->
- [ ] 

### Documentation / rollout

- [ ] <!-- user-facing help, flags, rollback notes -->
- [ ] 

---

## Explicitly out of scope

- <!-- reminders so scope creep is visible in review -->

---

## Example (fictional)

**Feature / change ID:** `timesheet-csv-export-v1`

**Acceptance criteria:**

- [ ] **Functional:** Given a week with entries, exported CSV columns match spec (date, duration, project id).  
- [ ] **AUTH:** Given another member’s ID in a tampered request, export is denied and returns no rows.  
- [ ] **Data:** Given labels that could start with `=`, cells are neutralized per CSV safety policy.  
- [ ] **Reliability:** Given no entries, UI shows chosen policy (empty file vs message) per PRD.  
- [ ] **Audit:** Successful export writes one audit event with user id and week.  

**Out of scope:** Bulk export for whole workspace; scheduled email delivery.

---

_Use [use-case.md](use-case.md) for flows; [bis-lite.md](bis-lite.md) §7 for shared build-intent alignment._
