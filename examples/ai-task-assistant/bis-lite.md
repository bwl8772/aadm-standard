# BIS-lite — AI task assistant (example)

## Summary

Add AI-assisted drafting for tasks from meeting snippets with human confirmation, strict workspace scoping, and auditability.

## Scope

**In:** Draft generation from selected text; confirmation UX; persistence API; audit events.  
**Out:** Full meeting transcription pipeline; mobile clients (phase 2).

## Architecture placement

- **Primary UDALI layers:** Application services (orchestration), Validation (draft sanity), Authorization model (confirm path).  
- **Systems touched:** Web app, task API, audit stream, policy service (existing).

## Interfaces

| Interface | Type | Owner | Notes |
|-----------|------|-------|-------|
| `POST /tasks/drafts` | API | Tasks team | Non-persistent proposals |
| `POST /tasks` | API | Tasks team | Persist after confirm |
| `tasks.confirmed` | Event | Platform | Audit sink |

## Data

- **Entities:** Task draft, Task, AuditEvent  
- **Sensitivity class:** Internal collaboration; **no** attachments in v1  
- **Retention:** Tasks follow workspace policy; drafts deleted after 24h if abandoned  

## AUTH

- **Roles:** workspace-member, workspace-admin  
- **Sensitive operations:** Confirming tasks that assign others (requires assignee visibility)

## Non-functional targets

- **Latency:** see use case  
- **Availability:** degrades gracefully—manual task creation always available  

## Verification

| Layer | Evidence |
|-------|----------|
| Validation | Unit tests for draft schema |
| Authorization | Integration tests deny cross-workspace confirm |
| Observability | Metric: draft→confirm funnel; audit completeness checks |

## Rollout

- Feature flag `ai_task_drafts` default off; pilot workspaces only  

## Decision log

| Date | Decision | Rationale |
|------|----------|-----------|
| _Example_ | Drafts non-persistent until confirm | Reduce accidental data retention |
