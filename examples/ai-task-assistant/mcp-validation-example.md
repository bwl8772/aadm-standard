# MCP validation example (illustrative)

This note shows **how teams might** use an external MCP server to give agents consistent standard context—**without** embedding proprietary server code in this repository.

## Scenario

Developers use an IDE agent to draft a `bis-lite.md` update for the AI task assistant feature. The organization runs an MCP server that exposes **read-only resources**:

- `standard://aadm/glossary`  
- `standard://aadm/layer-model-summary`  
- `standard://templates/bis-lite`

## Client configuration (conceptual)

Values are placeholders:

- **Endpoint:** `https://mcp.internal.example.com/mcp`  
- **Transport:** per your MCP client (for example, HTTP with streamed responses)  
- **Credentials:** short-lived tokens scoped to **read-only** standard resources  

_No secrets appear in repositories; tokens are injected via your secret store._

## Validation developers perform

1. **Scope check** — Agent may read standards; it may **not** obtain production credentials via MCP.  
2. **Output review** — Pull requests still require human review; MCP does not auto-merge.  
3. **Artifact diff** — `bis-lite.md` changes match confirmed UX and API contracts.  

## Failure modes to guard against

- MCP misconfigured with overly broad tool permissions  
- Agents treating draft markdown as executable truth without tests  
- Missing authorization checks because “the agent wrote the code”  

## Relationship to this repo

The **standard** lives here as markdown. A **private or org-hosted MCP server** may mirror these files for convenience—that deployment is **outside** this public adoption repository.

See also: [`docs/mcp-quickstart.md`](../../docs/mcp-quickstart.md).
