# BIS-lite — MatchGrid AI task assistant (example)

_Build intent: PRD authorizes; architecture constrains; this doc tells engineering what is built._  
See [`docs/build-intent-specification.md`](../../docs/build-intent-specification.md).

---

## 1. Capability name

`matchgrid-ai-action-plan-v1`

## 2. User / customer outcome

Members move from **vague tasks** to **reviewable, ordered action plans** faster, with **explicit human confirmation** before persistence.

## 3. Problem statement

Unstructured requests cause rework and stalled execution; ad-hoc chat with generic AI lacks workspace scope, audit trail, and guardrails.

## 4. In-scope

- Free-text in → **draft** structured plan out (schema in [`sample-schema.json`](sample-schema.json)).  
- Edit UI + **Confirm / Discard**.  
- Persistence + audit on confirm only.  
- Workspace-scoped AUTH; policy gates for AI enablement.

## 5. Out-of-scope

- Auto-assigning tasks to other users without separate UX/policy.  
- Training custom models; long-term memory across workspaces.  
- Executive dashboards or billing changes.

## 6. User story

**As a** MatchGrid member, **I want** the assistant to **draft a structured plan from rough text**, **so that** I can **confirm** a clear plan aligned to my workspace.

## 7. Acceptance criteria

_(Mirror [`use-case.md`](use-case.md) § Acceptance criteria.)_

- [ ] Confirm path persists only after AUTH success; drafts ephemeral until then.  
- [ ] Cross-workspace attempts denied.  
- [ ] Audit event on confirm.  
- [ ] Policy/timeouts handled per alternate flows A1–A4.

## 8. [UDALI](../../docs/udali-personas.md) layer map

| Grouping | Role for this change |
|----------|----------------------|
| **Unifier** | Planning UX; safe defaults; clear confirm/discard; “AI-assisted” labeling |
| **Designer** | Action plan schema; domain meaning of Step / Goal; DTOs for draft vs persisted entity |
| **Adapter** | Repositories for draft session + plan; AUTH on create/confirm; optional cache **N/A** for v1 |
| **Logician** | Orchestration: validate → call AI adapter → map response → persist on confirm; retries bounded |
| **Integrator** | API routes/handlers; integration tests; feature flag rollout; staging validation vs AI sandbox |

_Optional detail:_ [`layer-map.md`](layer-map.md).

## 9. Auth / permission assumptions

- Roles: `workspace-member`, `workspace-admin`.  
- **Create draft** and **confirm plan** require active membership in workspace.  
- AI credential is **server-side only**; never returned to browser.

## 10. Data / schema assumptions

- Draft payload conforms to [`sample-schema.json`](sample-schema.json) (extend with persistence ids after confirm).  
- No attachments v1; text fields max length enforced.

## 11. Known risks

| Risk | Mitigation / owner |
|------|---------------------|
| Model hallucination | Required open questions/assumptions; human confirm |
| Prompt injection | Treat user text as untrusted; structured output validation |
| Provider outage | A2 alternate flow; degrade to manual template |

## 12. Human checkpoint

- **Product** signs off on UX copy for AI-assisted flows.  
- **Architecture** signs off on AUTH boundary and AI data scope.  
- **Engineering** requires peer review for AI orchestration + persistence paths.

## 13. Standard alignment / validation notes

| Field | Value |
|-------|--------|
| **Applies?** | Yes (optional process step) |
| **Status** | Example only — see [`mock-validation-record-example.md`](mock-validation-record-example.md) (**mocked** output) |
| **Notes** | Teams may record an internal checklist or pilot against **public standard docs**; proof still lives in **tests and CI**. |

---

## Links

- PRD / authorization: _fictional — `PRD-MG-014`_  
- Architecture / RFC: _fictional — `RFC-MG-AI-BOUNDARIES`_  
- Epic / ticket: _fictional — `MG-4821`_  
