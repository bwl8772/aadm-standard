# AADM readiness checklist (public self-assessment)

Use this checklist to assess whether a capability or release is **aligned with AADM delivery expectations**—intent, artifacts, layers, AUTH, AI boundaries, QA, human gates, and optional MCP validation.

**This checklist is a public self-assessment.** It is **not** legal advice and **not** a certification. **Formal validation** may require an **AADM MCP server** deployment, **your organization’s** governance review, or regulated control evidence beyond these boxes.

---

## 1. Outcome readiness

_Product owns outcomes, scope, and success definition._

- [ ] **Problem and opportunity** stated in plain language  
- [ ] **Measurable or observable success** defined (metrics or qualitative signals)  
- [ ] **In-scope / out-of-scope** boundaries written down  
- [ ] **Stakeholders** identified for the initiative  

Related: [Build intent specification](build-intent-specification.md), [PRD-lite template](../templates/prd-lite.md)

---

## 2. Story readiness

_User intent is explicit before engineering guesses._

- [ ] **Customer story** uses “As a … I want … so that …” (or equivalent)  
- [ ] **Primary user / persona** is specific enough to review UX and safety  
- [ ] **Related use cases** linked or drafted  

Related: [Customer story template](../templates/customer-story.md), [Use case template](../templates/use-case.md)

---

## 3. BIS readiness

_Build intent bridges PRD authorization, architecture constraints, and engineering execution._

- [ ] **BIS-lite** (or full build intent) exists for non-trivial changes  
- [ ] **Capability name** stable enough for tickets and traceability  
- [ ] **Problem, in-scope, out-of-scope** consistent with PRD-lite  
- [ ] **Acceptance criteria** drafted before calling work “done”  
- [ ] **Known risks** and mitigations captured  

Related: [Build intent specification](build-intent-specification.md), [BIS-lite template](../templates/bis-lite.md)

---

## 4. [UDALI](udali-22-layer-model.md) layer readiness

_No silent skips across accountability groupings._

- [ ] **Layer map** completed (Unifier, Designer, Adapter, Logician, Integrator)  
- [ ] **N/A** entries documented with a one-line rationale where a grouping does not apply  
- [ ] **Cross-layer risks** (AUTH leakage, schema drift, missing audits) called out  
- [ ] **Evidence plan** sketched for touched groupings  

Related: [UDALI 22-layer model](udali-22-layer-model.md), [Layer map template](../templates/layer-map.md), [UDALI layer boundaries](../standards/udali-layer-boundaries.md)

---

## 5. Auth and permission readiness

_Authentication and authorization are design subjects, not afterthoughts._

- [ ] **Roles / actors** for this capability identified  
- [ ] **Sensitive operations** (mutations, exports, admin paths) listed  
- [ ] **Server-side enforcement** planned where domain meaning is applied—not UI-only  
- [ ] **Deny-by-default** posture understood for high-risk verbs  
- [ ] **Secrets** absent from repos and examples; rotation posture understood  

Related: [AUTH-aware delivery](auth-aware-delivery.md), [Human in the loop](../standards/human-in-the-loop.md)

---

## 6. Schema readiness

_Contracts at boundaries are deliberate._

- [ ] **APIs, events, or persistence shapes** touched by the change are named  
- [ ] **Compatibility** expectations noted (breaking vs additive)  
- [ ] **Validation** strategy exists for inbound/outbound payloads (including AI outputs where used)  
- [ ] **PII / sensitive fields** classified and handled per policy  

---

## 7. AI / service readiness

_Agent or model paths are bounded capabilities—not ambient god modes._

- [ ] **AI involvement** is explicit in scope (or explicitly **none**)  
- [ ] **Inputs and outputs** constrained (schemas, max size, allowed tools)  
- [ ] **Failure modes** defined (timeout, provider error, policy block)  
- [ ] **Logging** avoids leaking secrets or inappropriate personal data  
- [ ] **Human checkpoint** identified where policy requires approval before irreversible effects  

Related: [AADM core principles](../standards/aadm-core-principles.md) §3 Agentic systems by design

---

## 8. QA readiness

_Evidence matches risk._

- [ ] **Test or review plan** covers functional, AUTH, and critical edge cases  
- [ ] **Integration validation** planned if external dependencies change behavior by environment  
- [ ] **Observability** hooks identified for operational signals (where applicable)  
- [ ] **Rollback / feature-flag** posture documented when deployment risk warrants  

Related: [Acceptance criteria template](../templates/acceptance-criteria.md)

---

## 9. Human checkpoint readiness

_Governance matches organizational risk._

- [ ] **Irreversible or high-risk actions** mapped to required human approval  
- [ ] **Approver roles** named (not “someone will review”)  
- [ ] **Evidence of approval** will be captured in your system of record  
- [ ] **Agents and automation** cannot bypass checkpoints via alternate credentials or tools  

Related: [Human in the loop](../standards/human-in-the-loop.md)

---

## 10. MCP validation readiness

_Context servers support consistency; they do not replace app AUTH._

- [ ] **Whether MCP applies** is explicit (yes / no / N/A) for this initiative  
- [ ] If yes: leads recognize **Navigator** (delivery), **Investigator** (defects), and **Sentinel** (assurance across seams) as **distinct** MCP-facing workflows—Sentinel is **not** a substitute for defect tooling on a concrete bug thread  
- [ ] If yes: **endpoint and scope** documented by your operator (not pasted into git)  
- [ ] **Least privilege** for MCP credentials; read-only where sufficient  
- [ ] **Outputs from MCP or agents** treated as drafts until reviewed against BIS and tests  
- [ ] **Formal validation** (if required) scheduled or owned—not assumed from this checklist alone  

Related: [MCP quickstart](mcp-quickstart.md)

---

## Sign-off

Record **reviewers, dates, and evidence links** in **your internal** system of record—not in this public repository when sensitive.
