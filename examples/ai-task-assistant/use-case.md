# Use case — UC-101 Capture task from meeting snippet (example)

## Primary actor

Jordan (authenticated workspace member).

## Preconditions

- Jordan belongs to workspace **acme-collab**.  
- Workspace policy allows AI-assisted drafting (org toggle enabled).

## Main success scenario

1. Jordan selects text from meeting notes.  
2. Jordan invokes “Create tasks from selection.”  
3. System proposes structured tasks with titles, owners (if inferable), due hints.  
4. Jordan edits proposals and confirms.  
5. System persists tasks and emits audit events.

## Extensions

- **A1:** Owner unclear → task saved as **unassigned** with follow-up flag.  
- **A2:** Selection contains PII policy violation → draft blocked with guidance (no persistence).

## Postconditions

- Tasks visible per workspace sharing rules.  
- Audit trail records creator and confirmation timestamp.

## Non-functional notes

- Draft generation **p95 under 5s** for ≤2k tokens input (organizational SLO).  
- Authorization re-checked on **confirm**, not only on page load.

## Layer mapping

Primary: **Application services / composition**  
Secondary: **Validation**, **Authorization model**, **Observability hooks**

See [`layer-map.md`](layer-map.md).
