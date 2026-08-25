# AUTH catalog (Authority controls)

> Stable, append-only catalog of AADM authority controls. Each control names **a single obligation** that should be respected when designing, building, or auditing software. The catalog is stack-agnostic — it does not assume Postgres, Node, Python, REST, GraphQL, monolith, microservices, or any specific framework.

The catalog is grouped into **authority domains** (areas of governance responsibility). Within each domain, controls are listed in numeric (append-only) order. Numbers do not imply enforcement order; runtime ordering is described in [`udali-auth-enforcement-mapping.md`](udali-auth-enforcement-mapping.md).

| Section | Domain | Controls |
|---------|--------|----------|
| 1 | Foundation Authority | AUTH-00, AUTH-01, AUTH-02 |
| 2 | Content & Structure Authority | AUTH-03, AUTH-04, AUTH-05, AUTH-18 |
| 3 | Lifecycle & Lineage Authority | AUTH-06, AUTH-07, AUTH-08, AUTH-13 |
| 4 | Contract & Modality Authority | AUTH-09, AUTH-10, AUTH-21 |
| 5 | Data Quality Authority | AUTH-11, AUTH-12 |
| 6 | Decision & Approval Authority | AUTH-14, AUTH-15 |
| 7 | Security & Tenancy Authority | AUTH-16 |
| 8 | Synchronization Authority | AUTH-17 |
| 9 | UI & Client Authority | AUTH-19, AUTH-23 |
| 10 | Readiness & Delivery Authority | AUTH-20 |
| 11 | Query & Read Model Authority | AUTH-22 |
| 12 | Verification Authority | AUTH-24 |
| 13 | Execution Authority | AUTH-25, AUTH-26, AUTH-27, AUTH-28 |
| 14 | Observability Authority | AUTH-29, AUTH-30, AUTH-31 |
| 15 | Security & Policy Authority | AUTH-32, AUTH-33, AUTH-34 |
| 16 | AI Behavior Authority | AUTH-35, AUTH-36, AUTH-37 |
| 17 | Performance Authority | AUTH-38, AUTH-39, AUTH-40 |
| 18 | Deployment Authority | AUTH-41, AUTH-42, AUTH-43 |
| 19 | Agentic AI Behavior Authority *(candidate)* | AUTH-44, AUTH-45, AUTH-46, AUTH-47, AUTH-53, AUTH-54 |
| 20 | AI Assurance Authority *(candidate)* | AUTH-48, AUTH-49, AUTH-50, AUTH-51, AUTH-52, AUTH-55 |

A control is **named, numbered, and obligation-summarized** below. Adopters MAY require more depth (threat model, test specification, audit evidence type) in their own internal catalogs.

---

## 1. Foundation Authority

### AUTH-00 — System Authority Model

The adopter's system MUST declare, in writing, which actors hold authority over which subjects (users, tenants, content, configuration). Authority is not implicit. AUTH-00 is the meta-control that organizes all other AUTH controls; without it, the others have no anchor.

**Obligation summary:** Write down who has authority over what, before sensitive behavior is implemented.

### AUTH-01 — Reconcile Authority

When two or more authoritative sources can produce conflicting answers (different services, replicas, caches, edges), the system MUST declare which source reconciles and how — deterministically, not by race condition.

**Obligation summary:** Named tiebreaker when authoritative sources disagree.

### AUTH-02 — Recipe Authority

For procedural artifacts that drive system behavior (workflow recipes, pipeline definitions, AI agent runbooks), the system MUST declare which actor authors them, which actor approves them, and which version is in force.

**Obligation summary:** Recipes have an owner, an approver, and a current version.

---

## 2. Content & Structure Authority

### AUTH-03 — Content Ownership Authority

Every piece of stored content (record, document, blob) MUST be attributable to an owner — a user, tenant, or system actor — recorded in the data model, not inferred at read time.

**Obligation summary:** Content carries an owner; ownership is a stored field, not a guess.

### AUTH-04 — L2 Version Authority

Versioned structural artifacts (schemas, content templates, configuration documents — generically "L2" in AADM vocabulary) MUST have a deterministic version identifier and an authority that signs off on the active version.

**Obligation summary:** Versioned artifacts have a version number and a named version-sign-off authority.

### AUTH-05 — L2 Immutability Authority

Once an L2 version is published as authoritative, mutations MUST go through a new version, not edits to the existing one. The immutability rule and its exceptions are written down.

**Obligation summary:** Published versions don't mutate in place; new versions are issued for changes.

### AUTH-18 — L2 Content Inputs Authority

Inputs to L2 versions (the fields a structural artifact accepts) MUST be validated against an explicit schema before the artifact is persisted. Schema is the contract; free-form blobs are not.

**Obligation summary:** Inputs to structural artifacts are schema-checked at write time.

---

## 3. Lifecycle & Lineage Authority

### AUTH-06 — L3 Lifecycle Authority

Stateful artifacts that move through declared lifecycle states (orders, claims, jobs — generically "L3") MUST have a state machine and a named owner of each transition.

**Obligation summary:** State transitions are defined; transitions have a responsible authority.

### AUTH-07 — L3 Lineage Authority

When an L3 artifact derives from another (a corrected order, a retried job, a forked document), the lineage MUST be recorded — child knows parent, system can audit the chain.

**Obligation summary:** Derived artifacts record their lineage.

### AUTH-08 — L2 Required-for-L3 Authority

L3 lifecycle transitions that depend on an L2 version MUST refuse to proceed when the required L2 version is absent or invalid. Silent fallback to default behavior is non-conformant.

**Obligation summary:** Lifecycle work refuses to run when its structural dependencies are missing.

### AUTH-13 — Recipe Lifecycle Authority

Recipes (per AUTH-02) MUST themselves have a lifecycle — draft, in-review, active, deprecated. Recipes in non-active states do not drive production behavior.

**Obligation summary:** Recipes have a state machine; only "active" recipes run in production.

---

## 4. Contract & Modality Authority

### AUTH-09 — Modality Authority

When the same logical operation is exposed through multiple modalities (HTTP, gRPC, GraphQL, message queue, CLI, AI tool call), the authoritative behavior MUST live behind a single internal API; modalities are skins.

**Obligation summary:** Multiple modalities share one authoritative implementation, not parallel copies.

### AUTH-10 — Spec Version Enforcement Authority

When an external specification (OpenAPI, AsyncAPI, JSON Schema, protobuf, AI tool schema) is published, runtime behavior MUST conform to the declared spec version. Drift between spec and runtime is non-conformant.

**Obligation summary:** Runtime behavior matches the published spec version.

### AUTH-21 — DTO / Contract Boundary Authority

Data crossing module or service boundaries MUST flow through an explicit Data Transfer Object (DTO) shape, validated on entry and conformed on exit. Domain objects do not leak across boundaries; DTOs are the contract layer.

**Obligation summary:** All boundary crossings use validated DTOs.

---

## 5. Data Quality Authority

### AUTH-11 — Staleness Authority

When cached or derived data may be older than its source, the system MUST declare a staleness budget and surface freshness signals to consumers (timestamp, ETag, freshness flag). Silent stale reads on authoritative decisions are non-conformant.

**Obligation summary:** Cached/derived reads carry freshness info; authoritative decisions reject stale data.

### AUTH-12 — Completeness Authority

When a record requires N fields to be considered complete for a downstream operation, the completeness check MUST live in a named place (domain or service), not be re-invented per UI screen.

**Obligation summary:** "Complete enough to proceed" is checked once, in a named module.

---

## 6. Decision & Approval Authority

### AUTH-14 — Approval Authority

Operations that require human approval (high-risk, irreversible, policy-bound) MUST route through a declared approval flow with a recorded approver and timestamp. Implicit "no one objected" is not approval.

**Obligation summary:** Sensitive operations require recorded human approval.

### AUTH-15 — Error / 409 Authority

When an operation cannot proceed because the system is in a conflicting state (concurrent edit, lifecycle precondition unmet, duplicate creation), the system MUST return a structured conflict response with enough information for the caller to recover.

**Obligation summary:** Conflict responses are structured, informative, and actionable.

---

## 7. Security & Tenancy Authority

### AUTH-16 — Tenancy Authority

In multi-tenant systems, every authoritative read and write MUST carry a tenant identifier checked at the data boundary. Cross-tenant reads require an explicit, audited mechanism.

**Obligation summary:** Tenancy is enforced at the data boundary on every operation.

---

## 8. Synchronization Authority

### AUTH-17 — Sync State Authority

When a system replicates state between stores (cache ↔ database, search index ↔ source of truth, edge ↔ origin), the system MUST declare which store is authoritative and how sync state is observed and recovered.

**Obligation summary:** Replicated state declares its authoritative store and its sync recovery mechanism.

---

## 9. UI & Client Authority

### AUTH-19 — UI Authority Exposure

Authority decisions (what an actor may see, modify, or trigger) MUST be exposed to the UI as structured signals — booleans, capability flags, or enumerations — not inferred from absence of error.

**Obligation summary:** UI gets explicit signals about what's permitted; doesn't guess from 403s.

### AUTH-23 — Client State / Hooks Authority

Client-side caches, hooks, and stores MUST treat server responses as authoritative. Optimistic updates require a declared reconciliation path back to server truth.

**Obligation summary:** Client state is tentative; server is authoritative; optimistic UI reconciles.

---

## 10. Readiness & Delivery Authority

### AUTH-20 — Publish / Readiness Authority

Before a new capability is exposed to users (feature flag flip, route deployment, model promotion), the system MUST declare a readiness check that gates the publish. "Deployed" and "available to users" are separate events.

**Obligation summary:** Publish is gated by a declared readiness check distinct from deployment.

---

## 11. Query & Read Model Authority

### AUTH-22 — CRQ / Read Model Authority

When queries are served from a read model distinct from the write model (CQRS, materialized views, projections), the read model MUST declare which write model it derives from and how it stays in sync (see AUTH-17).

**Obligation summary:** Read models name their source write model and their sync contract.

---

## 12. Verification Authority

### AUTH-24 — Test / Verification Authority

Behavior covered by an AUTH control MUST be verified by automated tests or runtime checks. Documentation alone is not verification. The adopter's CI is where pass/fail lives.

**Obligation summary:** Every AUTH obligation has a paired test or runtime check.

---

## 13. Execution Authority

### AUTH-25 — Execution Idempotency Authority

Operations exposed to retry (async jobs, webhook handlers, integrations) MUST be idempotent against a declared idempotency key, or the system MUST declare why idempotency is unnecessary.

**Obligation summary:** Retryable operations are idempotent on a named key.

### AUTH-26 — Retry / Backoff Authority

External calls that may fail transiently MUST follow a declared retry and backoff policy. Tight retry loops against rate-limited dependencies are non-conformant.

**Obligation summary:** Retries follow a declared policy; backoff is real, not nominal.

### AUTH-27 — Transaction Boundary Authority

When an operation spans multiple persistent writes that must succeed or fail together, the transaction boundary MUST be explicit — either a database transaction, a saga with declared compensations, or an outbox pattern.

**Obligation summary:** Multi-write atomicity is declared, not inherited from luck.

### AUTH-28 — Async Job / Long-Running Task Authority

Long-running tasks MUST report progress through a declared mechanism, have a timeout, and have a path to observe outcome and (where applicable) cancel.

**Obligation summary:** Async work is observable, bounded, and cancellable when policy requires.

---

## 14. Observability Authority

### AUTH-29 — Audit Log Authority

Operations that affect authority or stored content MUST emit audit log entries with actor, subject, action, and outcome. Audit logs are tamper-evident in scope appropriate to risk.

**Obligation summary:** Authority-affecting operations leave audit trails.

### AUTH-30 — Traceability Authority

A request that crosses multiple modules or services MUST carry a correlation identifier (trace id, request id, conversation id) that allows the chain to be reconstructed.

**Obligation summary:** Cross-boundary work is traceable end-to-end.

### AUTH-31 — Decision Logging Authority

Decisions made by automated systems (rules engines, AI agents, authorization checks) that affect users or content MUST be logged with enough context to explain the decision after the fact.

**Obligation summary:** Automated decisions are explainable from logs.

---

## 15. Security & Policy Authority

### AUTH-32 — Policy Enforcement Authority

Authorization policies MUST be enforced where meaning is interpreted (typically the domain or service layer), not only on the outermost UI or API surface. Enforcement is deny-by-default for sensitive resources.

**Obligation summary:** AuthZ enforced at meaning, not just at the edge.

### AUTH-33 — Field-Level Access Authority

When some fields of a record are visible to some actors and not others, field-level access MUST be enforced — not solved by "the UI hides it." Server-side field filtering is the obligation.

**Obligation summary:** Field-level confidentiality is enforced server-side.

### AUTH-34 — Contextual Authorization Authority

When authorization depends on context (current state of the record, time, geography, attribute relationships), the contextual rule MUST be encoded in the authorization layer, not scattered through controllers.

**Obligation summary:** Context-dependent authorization lives in the authorization layer.

---

## 16. AI Behavior Authority

### AUTH-35 — Prompt Governance Authority

System prompts and prompt templates that drive AI behavior MUST be versioned, reviewed, and stored in a controlled location. User-supplied content is data, not prompt.

**Obligation summary:** Prompts are versioned artifacts; user content does not silently merge with system instructions.

### AUTH-36 — Output Constraint Authority

AI output that drives system action MUST be constrained by a declared schema or grammar and validated before action. Free-text output cannot trigger privileged operations unparsed.

**Obligation summary:** AI output is schema-validated before it can act.

### AUTH-37 — Model Selection Authority

Choice of model (provider, version, capability tier) for a given operation MUST be deliberate, recorded, and changeable through a controlled path. Silent model upgrades that change behavior are non-conformant.

**Obligation summary:** Model selection is an explicit, controlled, observable decision.

---

## 17. Performance Authority

### AUTH-38 — Rate Limiting Authority

Operations that can be triggered at scale (public APIs, AI calls, search queries) MUST have declared rate limits per actor, tenant, or surface, with clear behavior at the limit.

**Obligation summary:** Rate limits are declared, enforced, and observable.

### AUTH-39 — Cost Guardrail Authority

Operations that consume metered resources (third-party API calls, AI tokens, storage) MUST have declared cost guardrails per actor or operation, with clear behavior when the guardrail trips.

**Obligation summary:** Cost has a declared ceiling that the system enforces.

### AUTH-40 — Latency Budget Authority

User-facing operations MUST have a declared latency budget. Operations exceeding the budget either complete asynchronously with progress signal or fail with an actionable message.

**Obligation summary:** Latency has a budget; exceeding it has a declared behavior.

---

## 18. Deployment Authority

### AUTH-41 — Environment Isolation Authority

Production, staging, and development MUST be isolated — separate credentials, separate data stores, separate networks where policy requires. Cross-environment leakage is non-conformant.

**Obligation summary:** Environments are isolated; credentials and data don't cross.

### AUTH-42 — Feature Flag Authority

Feature flags that gate user-visible behavior MUST be managed through a controlled system, with a declared owner, exposure scope, and retirement plan. Flags are not permanent infrastructure.

**Obligation summary:** Flags have owners, scopes, and retirement plans.

### AUTH-43 — Release Gate Authority

Releases to production MUST pass a declared gate (CI green, security scan, change approval, rollout plan). The gate is automated where possible; manual gates have a named human authority.

**Obligation summary:** Release is a gated, recorded event — never ambient.

---

## 19. Agentic AI Behavior Authority *(candidate domain)*

> These controls address autonomous-agent edge cases that fall between AUTH-34, AUTH-35, AUTH-36, AUTH-38/39, and AUTH-14. They are **candidate** — adopt and enforce them now; the flag marks catalog maturity (wording may still be refined at ratification), not a barrier to use. Per GOVERNANCE §5, merge to `main` makes them part of the standard; ratification review stabilizes titles and obligation text.

### AUTH-44 — Tool Invocation Authority

When an agent decides to call a tool, the invocation MUST be authorized in the current context — not just "the tool is registered." Authorization checks apply to the calling agent and the operation it intends.

**Obligation summary:** Every agent-initiated tool call is independently authorized.

### AUTH-45 — Agent Loop & Budget Authority

Autonomous agents MUST run inside declared bounds — maximum iterations, maximum tool calls, maximum spawned subtasks, maximum wall-clock — as safety guards, not just cost controls.

**Obligation summary:** Agent loops are bounded; bounds are enforced before they're hit.

### AUTH-46 — Context Trust Boundary Authority

Content reaching an agent from untrusted sources (user input, tool output, retrieved documents, RAG results) MUST be treated as data, not instructions. Indirect prompt injection is a real threat surface.

**Obligation summary:** Untrusted content is data; only authorized content becomes instruction.

### AUTH-47 — Agent Memory Integrity Authority

When agents have persistent memory (user preferences, prior context, learned facts), the memory MUST have provenance — what wrote it, when, and from what source — and a mechanism to detect and recover from poisoning.

**Obligation summary:** Agent memory is provenanced and recoverable.

### AUTH-53 — Agent Scope Authority

Every agent MUST have a declared, machine-readable authority envelope — what it may read, create, modify, execute, request, approve, and never perform — enforced deny-by-default by the runtime, with task-scoped least-privilege credentials. Delegated authority narrows: a spawned agent cannot hold more than its parent, and an agent can never widen its own envelope or approve its own request.

**Obligation summary:** Each agent has an enforced authority envelope; reach equals the envelope, not the credential.

### AUTH-54 — Tool Contract Authority

Every capability exposed to a model MUST be registered as a governed interface: narrow (one effect, no mode switch), typed (validated input and output schemas, honest description), side-effect classified (read / write / external / irreversible; unclassified defaults to irreversible), idempotent where retryable, reversible or explicitly not, and audited. Capabilities that cannot hold these properties (arbitrary code or query execution, secret retrieval, permission grants, controls-modifying or bulk-destructive operations, generic outbound HTTP) MUST NOT be registered at all.

**Obligation summary:** A tool is a governed contract before it is callable; some capabilities are never tools.

---

## 20. AI Assurance Authority *(candidate domain)*

> These controls govern whether AI-produced work is trusted for consequence — evaluated, evidenced, verified complete, regression-guarded, qualified, and workflow-gated. Domain 19 governs whether the agent stays inside its bounds; this domain governs whether what it produced is any good and provably done. **Candidate** — adopt and enforce now; the flag marks wording maturity, not a barrier to use (see the domain 19 note). The adoption map for this domain is [`ai-automation-profile.md`](ai-automation-profile.md).

### AUTH-48 — Output Evaluation Authority

AI-generated work that carries consequence MUST be evaluated by something other than the agent or model that produced it: deterministic checks for every criterion expressible as a rule, a separate AI evaluator with explicit criteria for semantic judgement, and a structured machine-readable verdict (outcome, per-criterion result, reason, evidence). Failed verdicts route through a bounded correct-and-re-evaluate cycle that escalates to a human at the correction budget. Passing schema validation is never proof of correctness.

**Obligation summary:** Generated work is judged independently of its producer, with a verdict automation can act on.

### AUTH-49 — Claim Grounding Authority

AI-generated claims, recommendations, diagnoses, and decisions that a consumer will rely on MUST carry the evidence required to verify them — resolvable references to sources the run actually read, with trust classification. Unsupported claims are marked or withheld per declared policy, never emitted as equal to supported ones; inference is not presented as retrieval.

**Obligation summary:** Consequential claims cite resolvable evidence; unsupported claims are visible as such.

### AUTH-50 — Completion Verification Authority

An automation MUST verify the resulting system state, or the required artifact, before recording an agent's work as complete. The completion condition is declared in advance and observable; verification reads the state of record, not the agent's report; partial completion is recorded as `incomplete`, never rounded up.

**Obligation summary:** Completion is a verified fact about the system, not the agent's assertion.

### AUTH-51 — Regression Corpus Authority

Material AI and automation failures MUST be captured as replayable cases — inputs, context, governing configuration, expected and actual behaviour — and retained as a corpus that is replayed against changes to models, prompts, harnesses, agents, tools, evaluators, contracts, and workflows. Each material failure results in a durable change to a governing mechanism; passing cases are retained, not retired.

**Obligation summary:** A failure seen once is detectable forever; every governing change replays the corpus.

### AUTH-52 — AI Change Qualification Authority

A new or changed governing configuration — model, prompt, harness, tool surface, evaluator, or routing policy — MUST satisfy declared evaluation and regression thresholds before it carries production authority. Thresholds are declared before results are known; every route in a routed configuration qualifies separately; provider-side model changes are detected and qualified, not silently inherited; autonomy rung promotions cite evaluation evidence.

**Obligation summary:** Governing changes earn production authority through declared, pre-committed thresholds.

### AUTH-55 — Automation Workflow Gate Authority

Business-critical AI automation MUST run inside an explicitly defined workflow whose states, permitted transitions, and mandatory gates are owned by deterministic software the model cannot modify. Transitions are decided from recorded gate results, never from the agent's account of them; the AI decides how to work within its authorized state but cannot expand scope, reorder states, or waive a gate; failures route through declared retry, correction, rollback, or safe-termination paths that escalate on exhaustion.

**Obligation summary:** The lifecycle is owned outside the model; gates are satisfied by their owning controls, not by assertion.

---

## How adopters should use this catalog

1. **Map your changes.** For each meaningful change, identify which AUTH controls are touched. Use the layer-by-layer mapping in [`udali-auth-enforcement-mapping.md`](udali-auth-enforcement-mapping.md) as a starting point.
2. **Place each obligation.** Use the [UDALI personas](../docs/udali-personas.md) and [22-layer model](../docs/udali-22-layer-model.md) to decide where each AUTH lives in your code.
3. **Bind tests.** Per AUTH-24, each obligation that applies has a paired test. The test, not the document, is the proof.
4. **Audit through Sentinel.** During assurance passes, cite `AUTH-NN` ids in findings.
5. **Extend if needed.** Local internal controls are welcome; namespace them clearly (e.g., `ORG-AUTH-01`).

This catalog does not replace your regulated-control mapping (NIST 800-53, ISO 27001, SOC 2, etc.). It is a delivery-time control vocabulary that lets architects, agents, and reviewers speak the same language about authority.
