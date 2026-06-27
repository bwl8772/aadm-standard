# Example: Routing a vague bug report through Investigator recon

**Scenario.** A support ticket says: "Users report that notifications sometimes don't arrive." No stack trace, no repro steps, no specific user. Your agent (or you) needs to turn this into a structured investigation — not a speculative rewrite of the notification service.

---

## Where the AADM Standard helps

Without AADM, the natural instinct is to grep for "notification," start changing code, and hope the fix sticks. With AADM, the **Investigator lane** forces **recon before remediation**: classify the failure, gather evidence, then act. The standard blocks premature fixes — the most expensive kind.

---

## Step 1 — Recognize the lane

The ticket describes **broken vs expected**. That's **Investigator**, not Navigator (no new feature) and not Sentinel (no broad assurance audit). The workflow is:

> Investigate → Diagnose → Remediate

---

## Step 2 — State the symptom in one sentence

> Some users do not receive notifications that the system should have sent.

If you can't state it in one sentence, you don't understand the bug yet — stay in recon.

---

## Step 3 — Path A recon (before any code change)

Path A asks: **what do we actually know?**

| Recon question | What to look for |
|----------------|------------------|
| Which notifications? | Push? Email? In-app? All three? |
| Which users? | Specific accounts, roles, regions, or random? |
| When did it start? | Correlate with a deploy, a config change, or a provider incident? |
| What does "not arrive" mean? | Never sent? Sent but not delivered? Delivered but not displayed? |
| What does the system log? | Is the notification job enqueued? Does it succeed? Does the provider ACK? |

**Evidence to gather (not code to change):**

- Check notification job queue: are jobs being created for these users?
- Check provider delivery logs (email/push): are they accepting the payload?
- Check the read path: if in-app, is the notification persisted but not queried?
- Check feature flags / user preferences: is the user opted out?

---

## Step 4 — Classify the failure (Path B gate)

Only after recon surfaces evidence do you classify:

| Classification | Example finding |
|----------------|-----------------|
| **Enqueue failure** | Job never created → logic bug in trigger (Logician layer) |
| **Delivery failure** | Job created, provider rejects → adapter/integration issue (Adapter layer) |
| **Display failure** | Delivered but not shown → read-path or UI bug (Unifier layer) |
| **Config/preference** | User opted out or flag disabled → not a bug, close ticket |

Until you reach one of these classifications, **no code changes are allowed** (AADM: `rootCauseClassificationStatus: UNCLASSIFIED` → no fix).

---

## Step 5 — Remediate (only after classification)

Say recon found: jobs are created, but the email adapter silently swallows `429 Too Many Requests` from the provider and marks the job as "sent."

**Classification:** Delivery failure — Adapter layer — provider rate-limit not surfaced.

**Fix scope (narrow):**
- Adapter: surface 429 as a retryable failure, not success
- Worker: retry with backoff on retryable failures
- Observability: alert on sustained 429 rate

**Not in scope (Investigator guardrails):**
- Rewriting the notification service architecture
- Adding a new provider
- Changing the queue system
- Bumping timeouts without evidence

---

## Outcome

A vague "notifications don't work" ticket became a classified, layer-specific fix with evidence at each step. Reviewer sees the recon trail, the classification, and a narrow remediation — not a 12-file speculative refactor.

---

## Key AADM terms used

| Term | Role in this example |
|------|---------------------|
| Investigator lane | Workflow for broken-vs-expected: investigate → diagnose → remediate |
| Path A (recon) | Evidence gathering before any hypothesis or code change |
| Path B (diagnosis) | Classification gate — only crosses when evidence supports it |
| Root cause classification | The moment you name what layer and what kind of failure |
| `debug_defect` | The MCP tool that enforces this gated flow |
| Premature fix violation | What AADM flags if you change code before classification |
