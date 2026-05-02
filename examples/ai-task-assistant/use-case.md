# Use case — UC-MG-101 Turn vague task into structured action plan (example)

_Fictional MatchGrid scenario for AADM documentation._

---

## Use case title

Turn a vague task into a structured action plan (AI-assisted draft + human confirm).

## Objective

Let an authenticated member submit rough text and receive an **editable structured plan** they can save as workspace-owned work after explicit confirmation.

## Actors

| Actor | Description |
|-------|-------------|
| Primary | MatchGrid member (workspace user) |
| Secondary | AI provider (external model API—contracted boundary); audit sink |

## Trigger

Member chooses **“Plan this task”**, enters free-text (the vague task), and submits for drafting.

## Preconditions

- Member is authenticated and belongs to workspace **ws-demo-001**.  
- Workspace has **AI assistant** enabled per org policy.  
- Member has permission to **create plans** (not necessarily assign others).

## Basic flow

1. System validates input length and basic safety filters (empty, oversize).  
2. System creates a **non-persistent draft** record with correlation id.  
3. System invokes the **bounded AI path** with scoped prompt + member context (no unrelated tenant data).  
4. System returns a **structured plan proposal** matching [`sample-schema.json`](sample-schema.json) shape (goal, steps, open questions, assumptions).  
5. Member edits fields inline; selects **Confirm** or **Discard**.  
6. On **Confirm**, system re-checks authorization, persists an **ActionPlan** version 1, emits audit event `plan.confirmed`.  
7. On **Discard**, draft is removed; nothing persisted.

## Alternate flows

| ID | Condition | Flow |
|----|-----------|------|
| A1 | Policy denies AI for workspace | Show disabled state; offer manual template only |
| A2 | Model timeout or provider error | Show retry + link to manual planning |
| A3 | Content policy flags potential sensitive data | Block generation; show guidance (no model call) |
| A4 | Member loses permission mid-session | Confirm denied; draft not persisted |

## Acceptance criteria

- [ ] **Outcome:** Given valid vague text, member sees a proposal with **≥1 ordered step** and **≥1 open question or assumption** (forces honesty about ambiguity).  
- [ ] **Human gate:** Given member clicks **Confirm**, persisted plan exists **only after** server-side AUTH passes; **no** silent save on stream complete.  
- [ ] **Isolation:** Given another workspace id in a forged API call, draft create and confirm **fail** without exposing other tenants’ data.  
- [ ] **Transparency:** Given any saved plan, UI shows **“AI-assisted draft—review required”** until member confirms edits (copy policy—example).  
- [ ] **Audit:** Given confirm, audit stream receives event with `workspaceId`, `memberId`, `planId`, `correlationId`.  

## Postconditions

- **Confirm:** Durable `ActionPlan` visible per workspace sharing rules; audit row written.  
- **Discard:** No `ActionPlan`; draft ephemeral state cleared.
