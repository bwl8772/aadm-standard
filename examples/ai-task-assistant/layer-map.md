# Layer map — MatchGrid AI task assistant (example)

_Feature:_ AI-assisted **vague task → structured action plan** with human confirm.  
_Reference:_ [`docs/udali-22-layer-model.md`](../../docs/udali-22-layer-model.md).

---

## Feature / capability

`matchgrid-ai-action-plan-v1`

## One-line summary

Orchestrate a **bounded AI draft** into an **editable structured plan**, persist **only** after confirm with workspace AUTH and audit.

---

## Layer mapping

### Unifier

| Item | Notes |
|------|--------|
| Surfaces | “Plan this task” entry, proposal review, confirm/discard |
| UX / safety | Obvious AI-generated state; destructive actions not bundled into single misclick |
| Boundary clarity | Client receives proposal for display; server validates schema and AUTH |

### Designer

| Item | Notes |
|------|--------|
| Contracts / schema | Draft vs persisted plan shapes; alignment with [`sample-schema.json`](sample-schema.json) |
| Domain rules | Step ordering, required open questions/assumptions (product rule) |
| DTOs / mapping | Provider JSON → internal canonical plan DTO |

### Adapter

| Item | Notes |
|------|--------|
| Data access | Store ephemeral draft metadata + persisted `ActionPlan` |
| AuthN | Session / token validation on each mutation |
| AuthZ | Workspace membership checked on draft create and confirm |
| Caching | **None** v1 |

### Logician

| Item | Notes |
|------|--------|
| Application flow | Validate input → draft record → model call → validate structure → await confirm → persist |
| External systems | AI inference HTTP client (timeout, redacted logging) |
| AI / automation | Single bounded capability: “propose plan”; no autonomous assign |

### Integrator (I)

| Item | Notes |
|------|--------|
| Entrypoints | REST or RPC handlers for draft create, fetch proposal, confirm, discard |
| QA evidence | Contract tests on schema; integration tests for AUTH isolation; chaos **optional** |
| Deploy / config | Feature flag `ai_action_plan_v1`; secrets for provider in env |
| Integration validation | Staging run against AI sandbox; no prod keys in dev |

---

## Cross-cutting risks

- UI-only confirmation without server re-validation  
- Logging raw prompts containing PII  

## Evidence plan

- Integration tests: workspace A cannot confirm workspace B draft id  
- Unit tests: schema rejects malformed model output  
- Dashboard: draft → confirm funnel; error rate from provider  
