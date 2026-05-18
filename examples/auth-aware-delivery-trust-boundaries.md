# Example: AUTH-aware delivery — identifying trust boundaries before coding

**Scenario.** You're adding "organization billing settings" — admins can update payment methods, view invoices, and change subscription tiers. Before writing a single handler, you need to know where trust boundaries live so authorization isn't an afterthought bolted on in code review.

---

## Where the AADM Standard helps

Without AADM, auth decisions are scattered: some in middleware, some inline, some in the UI with no server check. Reviews catch gaps inconsistently. With AADM, **AUTH-aware delivery** means you map trust boundaries into the plan at the same time as layer ownership — so the first PR already has the right guards, not the third revision.

---

## Step 1 — List the actions and who can do them

| Action | Who should be allowed | Who must be blocked |
|--------|----------------------|---------------------|
| View invoices | Org admin, billing admin | Regular members, guests |
| Update payment method | Billing admin only | Org admin (read-only here), members |
| Change subscription tier | Org admin | Billing admin (can't escalate), members |
| View billing dashboard | Org admin, billing admin | Members (see nothing) |

---

## Step 2 — Identify AUTH controls that apply

From the AADM AUTH catalog:

| AUTH id | Rule | How it applies here |
|---------|------|---------------------|
| AUTH-02 | Identity must be established before any privileged action | Every billing endpoint requires authenticated session |
| AUTH-05 | Role-based access: permissions derive from declared roles, not implicit state | Admin vs billing-admin vs member — explicit role check on each route |
| AUTH-11 | No privilege escalation: an action cannot grant more access than the actor has | Billing admin cannot promote themselves to org admin via billing UI |
| AUTH-15 | Separation of read vs write trust | Viewing invoices (read) is broader than updating payment (write) — different trust levels |
| AUTH-20 | Server-side enforcement: UI can hide, but the server must reject | Don't rely on "button is hidden" — the API route itself checks role |

---

## Step 3 — Map trust boundaries to layers

| Layer | Trust boundary | What to enforce |
|-------|---------------|-----------------|
| **U (Unifier / UI)** | Visual gating only (not authoritative) | Hide tabs/buttons for unauthorized roles; always assume server validates |
| **I (Integrator / Routes)** | Route-level middleware | `requireRole('billing_admin')` on payment mutation routes; `requireRole('admin', 'billing_admin')` on read routes |
| **L (Logician / Service)** | Business rule enforcement | Tier change service checks: caller role ≥ required role for this action; rejects escalation attempts |
| **A (Adapter / Data)** | Row-level scoping | Queries scoped to `org_id` from authenticated session — no cross-org data leaks |

---

## Step 4 — Write the plan with AUTH baked in

Each slice from the Navigator plan now carries its AUTH tag:

| Slice | AUTH controls | Enforcement point |
|-------|--------------|-------------------|
| Billing dashboard API (GET) | AUTH-02, AUTH-05, AUTH-15 | Route middleware: admin or billing_admin |
| Update payment method (POST) | AUTH-02, AUTH-05, AUTH-20 | Route middleware: billing_admin only; server rejects even if UI allows |
| Change tier (PATCH) | AUTH-02, AUTH-05, AUTH-11 | Service layer: validates caller cannot escalate via tier change |
| Invoice list (GET) | AUTH-02, AUTH-05, AUTH-15 | Route middleware: admin or billing_admin; scoped to org |

---

## Step 5 — Verification (how you prove it)

| What to test | Test type | Example |
|--------------|-----------|---------|
| Admin can view, member cannot | Integration test | `POST /billing/invoices` as member → 403 |
| Billing admin can update payment | Integration test | `POST /billing/payment-method` as billing_admin → 200 |
| Billing admin cannot change tier | Integration test | `PATCH /billing/tier` as billing_admin → 403 |
| No cross-org data | Integration test | Request with org_id A, session org_id B → 403 or empty |
| UI hides but server rejects | E2E test | Manually craft request with hidden button's endpoint → 403 |

---

## Outcome

The feature ships with authorization designed in from the start. Code review checks implementation against the AUTH map — not "did someone remember to add a middleware?" Gaps are visible in the plan, not discovered in production.

---

## Key AADM terms used

| Term | Role in this example |
|------|---------------------|
| AUTH-aware delivery | Planning auth as part of delivery, not after |
| AUTH-NN controls | Specific obligation rules from the AADM catalog |
| Trust boundary | Where responsibility or privilege level changes |
| Navigator lane | Planned feature delivery with AUTH baked into scope |
| `fetch_auth` | MCP tool that returns the official text of any AUTH-NN rule |
| AUTH-20 (server-side enforcement) | UI gating is not enough — server must reject |
