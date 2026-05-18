# Example: Slicing a new feature into layer-aware work items

**Scenario.** Product says "add team invitations" — users can invite others by email, invitees accept/decline, and the team roster updates. Your agent (or your team) needs to break this into ordered implementation slices instead of one enormous PR.

---

## Where the AADM Standard helps

Without AADM, the feature lands as a single branch touching 15 files with no clear review order. With AADM, you decompose into **vertical slices** — each with a UDALI layer hint, AUTH controls, and acceptance criteria — so work is reviewable, testable, and shippable in order.

---

## Step 1 — Describe the feature (one paragraph)

> Team invitations: an authenticated user can invite an email address to join their team. The invitee receives an email with a unique link. Clicking the link creates their membership (or prompts sign-up first). The inviter sees pending/accepted/declined status. Admins can revoke pending invites.

---

## Step 2 — Identify layers touched

Using UDALI layer placement:

| Layer band | What's involved |
|------------|-----------------|
| **U (Unifier)** | Invite form, pending list, accept/decline UI |
| **D (Designer)** | Invite schema/DTO, status enum, email template contract |
| **A (Adapter)** | Invite repository, email provider adapter, token store |
| **L (Logician)** | Invitation service (create, validate token, expire, revoke) |
| **I (Integrator)** | Routes/controllers, integration tests, deployment of email worker |

---

## Step 3 — Cut vertical slices (ordered)

Each slice is independently demoable or testable:

| # | Slice | Layer hint | Depends on |
|---|-------|-----------|------------|
| 1 | Invite schema and contract (DTO shape, status enum, token format) | D — L7 | — |
| 2 | Invitation service: create invite, generate token, set expiry | L — L13 | Slice 1 |
| 3 | Invite repository and token persistence | A — L10 | Slice 1 |
| 4 | Email adapter: send invite email via provider | A — L10 | Slice 1 |
| 5 | API routes: POST /invites, GET /invites, DELETE /invites/:id | I — L18 | Slices 2, 3 |
| 6 | Accept/decline flow: validate token, create membership | L — L13 | Slices 2, 3 |
| 7 | UI: invite form + pending list + status display | U — L1 | Slice 5 |
| 8 | Admin revoke: permission check + status transition | L — L13, A — L10 | Slices 2, 5 |
| 9 | Integration tests: full invite → accept → roster updated | I — L21 | All above |

---

## Step 4 — Tag AUTH controls per slice

| Slice | AUTH controls that apply |
|-------|------------------------|
| 5, 8 | AUTH-02 (identity required), AUTH-05 (role-based access: admin for revoke) |
| 6 | AUTH-02 (token validates identity), AUTH-11 (no escalation: invitee gets member, not admin) |
| 4 | AUTH-24 (email adapter: secrets in env, not in repo) |

---

## Step 5 — Define acceptance per slice

Each slice gets a one-liner acceptance criterion:

1. Contract compiles, types exported, snapshot test passes
2. Service unit test: create returns token, expiry is future, duplicate email rejects
3. Repo integration test: persist → retrieve → status transitions
4. Email adapter test: sends with correct template vars (mock provider)
5. API integration test: 201/400/403 on create, list filters by team, delete requires admin
6. Accept flow: valid token → membership created, expired token → 410, used token → 409
7. UI renders form, submits, displays pending/accepted/declined
8. Admin revoke: 204 for admin, 403 for member, status moves to `revoked`
9. E2E: invite → email sent → accept link → membership visible on roster

---

## Outcome

Nine reviewable PRs (or work items) instead of one. Each has a clear layer owner, AUTH tags, and pass/fail criteria. The Navigator lane frames this as **scope → verify → readiness** — you know what "done" means at each step.

---

## Key AADM terms used

| Term | Role in this example |
|------|---------------------|
| Vertical slice | One independently testable piece of the feature, cutting through layers |
| UDALI layer hint | Which persona/layer owns each slice (guides reviewer assignment) |
| AUTH controls | Which security/trust obligations bind at each slice |
| Navigator lane | The workflow: scope the plan, verify per slice, confirm readiness |
| `slice_issues` | The MCP tool that scaffolds this decomposition |
