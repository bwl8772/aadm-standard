# BIS-lite (build intent specification — starter)

_Use this as the lightweight **build intent** shared by product, architecture, and engineering. Fill every section or mark **N/A** with a one-line reason._

**Reminder:** **PRD authorizes** work; **architecture constrains** work; **this BIS tells engineering what is actually being built**—see [Build intent specification](../docs/build-intent-specification.md).

---

## 1. Capability name

<!-- Short, stable handle for tickets and PR titles (e.g. “workspace-task-drafts-v1”). -->

## 2. User / customer outcome

<!-- Observable value: what improves for the user or customer when this ships? -->

## 3. Problem statement

<!-- Current pain, gap, or opportunity in plain language. -->

## 4. In-scope

<!-- Bullet list: behavior, surfaces, data, integrations included. -->

## 5. Out-of-scope

<!-- Explicit exclusions to prevent silent creep. -->

## 6. User story

<!-- As a … I want … so that … (or link to customer-story.md). -->

## 7. Acceptance criteria

<!-- Observable checks; link to acceptance-criteria.md if separate. -->

- [ ] <!-- criterion -->
- [ ] 

## 8. [UDALI](../docs/udali-personas.md) layer map

<!-- Primary grouping(s) and secondary touches: Unifier / Designer / Adapter / Logician / Integrator. -->

| Grouping | Role for this change |
|----------|----------------------|
| Unifier | <!-- --> |
| Designer | <!-- --> |
| Adapter | <!-- --> |
| Logician | <!-- --> |
| Integrator | <!-- --> |

_Optional:_ attach [layer-map.md](layer-map.md) for deeper placement.

## 9. Auth / permission assumptions

<!-- Roles, sensitive actions, deny-by-default notes. Do not paste secrets or tokens. -->

## 10. Data / schema assumptions

<!-- Entities, sensitivity, retention/residency, contract/version notes (APIs, events, DB). -->

## 11. Known risks

| Risk | Mitigation / owner |
|------|---------------------|
| | |

## 12. Human checkpoint

<!-- Approvals or manual steps required before/after ship (e.g. elevated privilege, prod config, legal/compliance). Name role responsible. -->

## 13. MCP validation status

<!-- Record whether agents verified against your org’s AADM MCP server (recommended for agent-heavy teams). High-level notes only—no secrets. MCP complements this repo; it does not replace app AUTH. -->

| Field | Value |
|-------|--------|
| Applies? | <!-- Yes / No / N/A --> |
| Status | <!-- e.g. Not started / Draft reviewed / Ready for implementation --> |
| Notes | <!-- what was loaded or validated at a high level—no internal server details --> |

---

## Links

- PRD / authorization: <!-- link or ID -->
- Architecture constraints / RFC: <!-- link or ID -->
- Epic / ticket: <!-- link -->
