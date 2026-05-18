# Example: Sentinel attestation — scoping dimensions for a feature before release

**Scenario.** Your team shipped a "scheduled send" feature in a messaging product. Before release sign-off, you want structured proof that the feature holds across layers and seam boundaries — not just "tests pass."

---

## Where the AADM Standard helps

Without AADM, "check the feature" means running CI and hoping coverage is adequate. With AADM, you **declare what you're claiming** (attestation dimensions) and get back **exactly which seams need evidence** (the closure). Gaps become visible before deploy, not after.

---

## Step 1 — State the vertical claim

Ask: "How deep does this feature go?"

Scheduled send touches UI (compose → schedule picker), application boundary (API route accepting the schedule payload), domain logic (scheduler service enforcing time rules), data access (persisting the scheduled job), and platform (cron/worker that fires the send).

**Vertical claim:** `full_vertical_ui_to_metal`

---

## Step 2 — Declare horizontal seams in play

Ask: "Which cross-cutting boundary types does this feature cross?"

- **Schema contract** — the schedule payload shape crosses client → API → worker.
- **Async/event** — the scheduled job fires asynchronously, not inline.
- **Auth trust boundary** — only the sender (or org admin) can cancel a scheduled message.

**Horizontal seams:** `schema_contract`, `async_or_event`, `auth_trust_boundary`

---

## Step 3 — Set attestation depth

This is a release gate, not a quick smoke check.

**Attestation depth:** `standard`

---

## Step 4 — Read the closure output

With those three dimensions supplied (via `sentinel_closure` in the hosted MCP, or manually against the standard), the closure returns seam tiers:

| Seam id | Tier | What you must prove |
|---------|------|---------------------|
| `VERT_UI` | required | Schedule picker renders, validates, submits correctly |
| `VERT_APP` | required | Route accepts/rejects payload per contract |
| `VERT_DOMAIN` | required | Scheduler service enforces time rules, idempotency |
| `VERT_DATA` | required | Job persists, survives restart, dequeues once |
| `VERT_PLATFORM` | required | Worker fires on time, observability emits on success/failure |
| `HORI_SCHEMA` | required | Payload shape identical client → API → worker (contract test) |
| `HORI_ASYNC` | required | Job delivery guarantees: at-least-once, dedup, dead-letter |
| `HORI_AUTH` | required | Cancel/reschedule gated to sender or admin role |

**Latent** (not claimed, residual risk acknowledged):
- `HORI_EXTERNAL` — if using a third-party email provider, that seam is latent unless explicitly claimed.

---

## Step 5 — Bind evidence per required seam

For each **required** row, point to a concrete artifact:

| Seam | Evidence |
|------|----------|
| `VERT_UI` | `tests/compose/schedule-picker.spec.ts` — renders, validates date, submits |
| `VERT_APP` | `tests/api/schedule-send.integration.ts` — 201/400/403 cases |
| `VERT_DOMAIN` | `tests/services/scheduler.unit.ts` — time rules, idempotency |
| `VERT_DATA` | `tests/repos/scheduled-jobs.integration.ts` — persist, restart, dequeue |
| `VERT_PLATFORM` | `tests/workers/send-worker.integration.ts` — fires, emits metrics |
| `HORI_SCHEMA` | `tests/contracts/schedule-payload.contract.ts` — shape parity |
| `HORI_ASYNC` | `tests/workers/delivery-guarantees.integration.ts` — dedup, DLQ |
| `HORI_AUTH` | `tests/api/schedule-send.auth.ts` — sender-only cancel, admin override |

---

## Outcome

The team now has a **traceable map** from claim to proof. Reviewer sees what's covered, what's latent (acknowledged risk), and where evidence lives — instead of scanning 200 test files hoping nothing was missed.

---

## Key AADM terms used

| Term | Role in this example |
|------|---------------------|
| Attestation dimensions | The three inputs declared (depth + vertical claim + horizontal seams) |
| Attestation closure | The output — required/additional/latent seam tiers |
| VERT_* | Vertical seam ids (depth through the stack) |
| HORI_* | Horizontal seam ids (cross-cutting boundaries) |
| Sentinel lane | The workflow that owns "prove alignment" |
