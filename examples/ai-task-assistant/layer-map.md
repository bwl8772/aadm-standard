# Layer map — AI task assistant (example)

## Change summary

Introduce AI-assisted task drafting with explicit confirm step and workspace-scoped authorization.

## Primary layer

**Layer:** Application services / orchestration (conceptual L6 region)  
**Rationale:** Coordinates proposal flow, calls validation and policy checks, persists only after human confirm.

## Secondary layers

| Layer | Impact |
|-------|--------|
| Validation | Draft schema, content policy gates |
| Authorization model | Confirm path must re-evaluate permissions |
| Contracts | New draft endpoint shapes; event payload |
| Observability | Funnel metrics; audit trace linkage |

## Role review

| UDALI hat | Review focus | Reviewer |
|-----------|--------------|----------|
| Designer | Confirmation UX clarity, error states | _Example_ |
| Adapter | Event emission & integration idempotency | _Example_ |
| Logician | Schema + authorization matrix tests | _Example_ |

## Contract touches

- `POST /tasks/drafts` request/response  
- `POST /tasks` confirms draft id  
- `tasks.confirmed` event schema fragment (see JSON sample)

## Risks of cross-layer leakage

- UI-only checks while API accepts unscoped draft ids  
- Agent/tooling paths bypassing audit hooks  

## Evidence plan

- Integration tests for workspace isolation  
- Contract tests for event consumers  
- Dashboards for draft abandonment vs confirms  
