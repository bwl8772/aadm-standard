# AI-coder hard stops — never recommend

> A hard-stop list for humans and orchestrators guiding automated coding agents. These actions routinely cause irreversible harm, compliance violations, credential compromise, or production incidents. Stack-agnostic.

This is **normative**. An AADM-conformant implementation must refuse to recommend, generate, or facilitate actions on this list. See [`../CONFORMANCE.md`](../CONFORMANCE.md) §5.

This list is not exhaustive — your organization's rules supersede.

---

## Top categories — do not instruct an AI to do these

Ranked by typical blast radius (conceptual order; all are disqualifying).

### 1 — Destroy or bulk-delete production data

**Examples.** Drop production databases or schemas; `DELETE FROM` / truncate without verified backup and runbook; wipe buckets or tenants "to fix" bugs.

**Why.** Irreversible loss, regulatory breach, customer harm. Recovery belongs to controlled procedures, not agent improvisation.

### 2 — Exfiltrate or paste secrets into chat, code, or logs

**Examples.** "Paste your `.env` here"; embed API keys in source; commit private keys; echo tokens in CI logs; rotate secrets by posting old values.

**Why.** Immediate credential compromise and persistence in model/vendor logs.

### 3 — Send real PII, PHI, payment data, or regulated content into third-party models or untrusted tools

**Examples.** Customer lists, health records, card numbers, government IDs, children's data — into LLM prompts, screenshot OCR pipelines, or random SaaS.

**Why.** Privacy law, contract breach, data-processing agreements. Use **synthetic**, **redacted**, or **approved** environments only.

### 4 — Disable or bypass security controls "to unblock"

**Examples.** Turn off auth; comment out CSRF/CORS checks; use `eval`; run with admin/root everywhere; `chmod 777`; disable TLS verification in production.

**Why.** Opens systemic compromise; masks real defects.

### 5 — Run destructive shell commands without explicit environment guardrails

**Examples.** `rm -rf /`; recursive deletes on `/`; mass `git reset --hard` on shared branches; force-push `main`; Terraform destroy on prod-adjacent workspaces.

**Why.** One wrong cwd or flag deletes org history or infrastructure.

### 6 — Execute arbitrary code or install packages from unverified sources

**Examples.** `curl | bash` from unknown URLs; `pip` / `npm install` from GitHub gist without review; paste obfuscated scripts.

**Why.** Supply-chain and remote-code-execution risk — attackers target AI-assisted workflows.

### 7 — Change production traffic, DNS, or routing from an agent session

**Examples.** Flip load balancers; cutover DNS; scale-to-zero prod; delete VPC rules — without change control.

**Why.** Customer-visible outages and security boundary removal.

### 8 — Modify legal, compliance, or audit artifacts as "cleanup"

**Examples.** Delete audit logs; rewrite retention policies in code comments; forge approvals.

**Why.** Criminal and civil exposure; destroys accountability.

### 9 — Automated harassment, scraping behind auth, or ToS-violating automation

**Examples.** Spam endpoints; scrape logged-in sites against policy; credential-stuffing "tests."

**Why.** Legal and abuse liability; not a debugging technique.

### 10 — "Fix" by universally weakening validation or error handling

**Examples.** Remove all schema checks; swallow exceptions; return `200` on every failure.

**Why.** Hides defects until catastrophic corruption or silent data loss.

---

## Additional high-risk patterns (still never recommend)

| Pattern | Why avoid |
|---------|-----------|
| Single shared production credential for agents | No attribution; blast radius equals whole estate. |
| Auto-merge without human review on security-sensitive repos | Agents mistake intent; supply-chain merges. |
| Granting overly broad cloud IAM "just in case" | Persistent lateral movement risk. |
| Running prod database migrations without backup + rollback plan | Schema locks and data loss. |
| Disabling MFA or IP allowlists "for convenience" | Account takeover. |
| Embedding customer data in prompts "temporarily" | Retention and subprocessors. |

---

## Safe alternatives (what to recommend instead)

- **Synthetic fixtures** and **redacted** traces for reproduction.
- **Staging** or **ephemeral** environments with no production connectivity.
- **Least-privilege**, **time-boxed** credentials scoped to one task.
- **Human-approved** runbooks for destructive or production-touching steps.
- **Evidence-gated remediation** ([Investigator lane](three-lanes.md)) — not "delete everything and retry."

---

## Implementation conformance

Per [`../CONFORMANCE.md`](../CONFORMANCE.md) §5, an AADM-conformant tool surfaces a structured refusal (with the category id) when these categories are matched. A tool that produces such recommendations as acceptable output is **non-conformant** with the standard.

---

## Closing

If a suggestion matches anything above, **stop** — rewrite the task with non-destructive, least-privilege, non-production constraints. When unsure, **escalate to a human** with authority over data and infrastructure. Irreversible actions are never delegated to an agent.
