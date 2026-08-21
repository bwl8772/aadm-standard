# Example: Architecture review — vertical depth + horizontal seams on an existing module

**Scenario.** Your team inherited a "payment processing" module six months ago. It works, but nobody has formally reviewed whether it holds together across layers and boundaries. Before adding a new payment method, you want to audit the existing module's architecture — not rewrite it, just understand where it's strong and where it's fragile.

---

## Where the AADM Standard helps

Without AADM, "architecture review" means reading code until you feel confident (or give up). With AADM, you scope the review using **attestation dimensions** — declare what depth you're checking and which horizontal seams matter — then systematically ask **challenge questions** per seam. The output is a map of strengths and risks, not a vague "looks fine" or "rewrite it."

---

## Step 1 — Name the module and its boundary

**Module:** Payment Processing  
**Boundary:** Accepts payment intents from the order service, coordinates with external payment providers, persists transaction state, emits events for fulfillment.

**Interface (what callers depend on):**
- `POST /payments/intents` — create a payment intent
- `PaymentCompleted` event — emitted when funds are captured
- `PaymentFailed` event — emitted when capture fails permanently

---

## Step 2 — Declare attestation dimensions for the review

| Dimension | Value | Rationale |
|-----------|-------|-----------|
| Vertical claim | `through_domain_and_data` | We're reviewing logic and persistence, not the UI that calls it |
| Horizontal seams | `schema_contract`, `external_integration`, `async_or_event`, `auth_trust_boundary` | Payment module crosses all of these |
| Attestation depth | `standard` | Thorough review, not a quick smoke check |

---

## Step 3 — Walk vertical hops with challenge questions

| Vertical hop | Challenge question | Finding |
|--------------|--------------------|---------|
| `VERT_APP` (API boundary) | Is the payment intent contract versioned? Can the order service depend on shape stability? | Contract exists as a TypeScript type, but no explicit versioning or compatibility test. **Risk: shape drift.** |
| `VERT_DOMAIN` (logic) | Are retry rules, idempotency, and state transitions explicit and tested? | State machine exists with tests. Retry logic is inline in the service — not extracted, but tested. **Adequate.** |
| `VERT_DATA` (persistence) | Is transaction state durable across restarts? Is there a dead-letter path? | Postgres with transactional writes. No explicit dead-letter — failed jobs stay in `failed` state with no alert. **Risk: silent failure accumulation.** |

---

## Step 4 — Walk horizontal seams with challenge questions

| Horizontal seam | Challenge question | Finding |
|-----------------|-------------------|---------|
| `HORI_SCHEMA` | Is the payment intent DTO shape tested at the boundary (contract test)? | No contract test. Shape is validated by Zod at the route, but the order service has its own copy of the type. **Risk: desync between producer and consumer.** |
| `HORI_EXT` | Is the payment provider adapter isolated? Can you swap providers without touching domain logic? | Adapter pattern exists (`StripeAdapter`, `MockAdapter`). Provider-specific error mapping is clean. **Strong.** |
| `HORI_ASYNC` | Are payment events (completed/failed) guaranteed delivered? What happens if the event bus drops one? | Events published after DB commit (good). No outbox pattern — if publish fails after commit, event is lost. **Risk: eventual consistency gap.** |
| `HORI_AUTH` | Who can create a payment intent? Can a user trigger payment for another user's order? | Route checks `user_id === order.user_id`. No row-level policy at the DB layer. **Adequate for now, fragile if new callers added.** |

---

## Step 5 — Summarize strengths and risks

**Strengths:**
- Clean adapter pattern for external providers (swap without domain changes)
- State machine with good unit test coverage
- Transactional persistence (no partial writes)

**Risks (prioritized):**
1. **No outbox pattern** — event loss possible between DB commit and publish (HORI_ASYNC)
2. **No contract test** — payment intent shape can drift between services (HORI_SCHEMA)
3. **Silent failure accumulation** — no alert on dead jobs (VERT_DATA)
4. **No API versioning** — shape changes break callers without warning (VERT_APP)

---

## Step 6 — Decide what to do

This is an architecture review, not a rewrite mandate. The findings feed into:

- **Navigator:** If adding a new payment method, include the outbox pattern in that slice (piggybacking on planned work).
- **Investigator:** If a bug surfaces related to lost events, the finding is already classified — skip recon.
- **Sentinel:** If a release gate requires attestation, these risks are the latent seams that need either evidence or explicit risk acceptance.

---

## Outcome

A 30-minute structured review produced a prioritized risk map with specific seam ids — not "the payment module is complex." The team knows exactly which seams are fragile and can plan work against them instead of reacting to incidents.

---

## Key AADM terms used

| Term | Role in this example |
|------|---------------------|
| Module / Interface | The unit under review and its contract surface |
| Attestation dimensions | Scoping the review (depth + horizontal seams + rigor) |
| VERT_* / HORI_* | Specific seams checked with challenge questions |
| Challenge questions | Per-seam probes that surface strengths or risks |
| `architecture_lens` | MCP tool that returns layer band, AUTH, and doctrine for a module |
| `survey_context_brief` | MCP tool that maps callers and collaborators |
| Sentinel / Navigator / Investigator | Findings hand off to the right lane for action |
