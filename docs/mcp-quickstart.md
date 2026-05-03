# MCP quickstart — connecting to an AADM MCP server

**Why connect:** Markdown in this **[standard repository](../README.md)** is ideal for humans and tickets; an **AADM MCP server** gives **coding agents** the same definitions, prompts, and bounded tools through **MCP**—reducing improvised prompts and copy-paste drift. You typically use **both**.

This guide explains how a **developer** connects an **AI coding environment** (for example, an MCP-capable IDE) to **an AADM MCP server** your team runs or receives from a **trusted operator**.

All URLs, hosts, and secrets below are **placeholders**. Replace them with values from **your** operator.

---

## 1. What the AADM MCP server does

An **AADM-aligned MCP server** is typically a **small HTTP service** that speaks **MCP over Streamable HTTP**. It exposes **standard context** to agents—commonly:

- **Resources** (for example, markdown definitions, layer guides, AUTH control text—exact URIs depend on the deployment)  
- **Prompts** (structured workflows your organization chooses to publish)  
- **Tools** (bounded operations such as listing or fetching standard material—**not** your product’s business APIs)

The framing used across AADM MCP deployments: **the MCP carries the standard to agents**; **your application repository** still owns production behavior, AUTH enforcement, tests, and evidence.

### Three agent workflows (Navigator · Investigator · Sentinel)

Reference AADM MCP servers commonly describe **three equal workflows**—each with **three stages**—so hosts do not collapse everything into “bug chat”:

| Persona | Typical intent | Shape (high level) |
|---------|----------------|---------------------|
| **Navigator** | Planned delivery (net new **or** meaningful improvement) | Scope → verify → readiness |
| **Investigator** | Broken versus expected, errors, regressions | Investigate → diagnose → remediate (evidence-gated before deep diagnosis) |
| **Sentinel** | Prove **existing** posture against AUTH / UDALI / evidence across seams | Scope & frame → trace & bind evidence → attest readiness |

**Sentinel** is **not** a renamed troubleshooter: it addresses **assurance and alignment**, not a single defect thread. **Investigator** owns the defect track; **`debug_defect`** (or your operator’s equivalent) is the usual first tool when the narrative is defect-shaped.

Capability copy on the server often surfaces this via **`standard_brief`** (summary JSON), **`route_lane`** when your orchestrator already fixed **`intent`** as `delivery` / `defect` / `assurance`, and **`orchestrator_anchor`** for long-running session and persistence reminders. Canonical narrative URIs vary by bundle; your operator lists stable **`resources/read`** targets.

### Reference AADM enforcement MCP tool ids

The **reference** open **AADM Standard Enforcement MCP** bundle registers tools under these ids (alphabetical). Your deployment may add aliases or omit tools; treat **`tools/list`** after **`initialize`** as authoritative for **your** server.

| Id | Role (short) |
|----|----------------|
| **`audit_outline`** | Audit / readiness scaffold |
| **`classify_bug`** | Bug symptom classification helper |
| **`debug_bundle`** | Larger combined troubleshooting payload (power users) |
| **`debug_defect`** | Primary Investigator / defect troubleshooter |
| **`debug_hint`** | Lightweight troubleshooting hint |
| **`fetch_auth`** | Load AUTH control markdown by id |
| **`fix_pack`** | Structured fix-oriented pack helper |
| **`map_feature`** | Feature → stack / AUTH hints (heuristic) |
| **`orchestrator_anchor`** | Long-thread orchestrator checklist |
| **`plan_delivery`** | Multi-role delivery handoff / boundary hints |
| **`role_guide`** | Compact UDALI doctrine slice by role |
| **`route_lane`** | Explicit `delivery` / `defect` / `assurance` lane metadata |
| **`standard_brief`** | Full capability snapshot (workflows, contracts, **`follow_on_catalog`** where published) |
| **`triage_bug`** | Symptom paragraph → triage hints (heuristic) |

**Resources are not tools:** canonical markdown still loads via MCP **`resources/read`** (for example workflow mode URIs); hosts sometimes surface that as **`suggested_tool`: `resources/read`** inside **`follow_on_catalog`** entries.

**Older prose** may still mention retired ids. Common mappings when updating runbooks or tickets:

| Older name | Use instead |
|------------|-------------|
| `aadm_capabilities` | **`standard_brief`** |
| `map_feature_to_layers` | **`map_feature`** |
| `udali_route_delivery` | **`plan_delivery`** |
| `aadm_troubleshooter` | **`debug_defect`** |

---

## 2. What it does not do

- **Not your application** — no customer features, domain workflows, or transactional product APIs run “inside” the MCP as a substitute for your app.  
- **Not the authority layer for production** — identity, authorization, and data access for **your** systems remain **in your services and gateways**, not in MCP responses.  
- **Not a compliance verdict** — tools may guide mapping or troubleshooting; they do **not** replace organizational sign-off, audits, or code review.  
- **Not a secret store** — API keys and tokens are **client-side configuration** for connecting **to** the server; they should be scoped and rotated like any internal integration.

This public repo documents **how to think about** MCP in an AADM program; it does **not** ship server source code or describe proprietary validation internals.

---

## 3. Prerequisites

| Requirement | Notes |
|-------------|--------|
| **Server URL** | MCP HTTP URL including path **`/mcp`** (scheme + host + `/mcp`). A separate **`/health`** path is often used for load balancers (not MCP protocol traffic). |
| **Client support** | An MCP client that supports **Streamable HTTP** (check your IDE’s MCP docs). |
| **Trust** | You connect only to servers **you operate or explicitly trust**. |
| **Headers / sessions** | Clients must send the **`Accept`** headers required by Streamable HTTP (`application/json` **and** `text/event-stream` on **`POST /mcp`**). Session behavior follows your MCP SDK and server (for example, session id headers after `initialize`). |
| **Runtime (if self-hosted)** | Operators typically document Node version, build, and port—follow **their** runbook, not guesses here. |

---

## 4. Example configuration placeholder

Editors vary. Many accept an **`mcpServers`** block. Below is a **generic placeholder** (stdio-style wrapper plus env vars). Your client might instead use an **`url`** field pointing directly at **`https://your-aadm-mcp-host.example/mcp`**—follow your vendor’s schema.

```json
{
  "mcpServers": {
    "aadm": {
      "command": "node",
      "args": ["path/to/aadm-mcp-client.js"],
      "env": {
        "AADM_MCP_ENDPOINT": "https://your-aadm-mcp-endpoint.example/mcp",
        "AADM_API_KEY": "your-api-key"
      }
    }
  }
}
```

**Notes:**

- Use **`…/mcp`** as the MCP entrypoint, not the bare origin alone (unless your operator documents otherwise).  
- Prefer **environment-specific** keys and **short-lived** credentials where possible.  
- **`AADM_API_KEY`** is illustrative—real deployments may use different env names or OAuth; match **your host’s** documentation.

---

## 5. Example validation request

After replacing the host, you can sanity-check **Streamable HTTP** with **`curl`** using **`POST`** and both **`Accept`** types:

```bash
curl -sS -X POST "https://your-aadm-mcp-host.example/mcp" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json, text/event-stream" \
  -d '{
    "jsonrpc": "2.0",
    "id": 1,
    "method": "initialize",
    "params": {
      "protocolVersion": "2024-11-05",
      "capabilities": {},
      "clientInfo": { "name": "quickstart-probe", "version": "1.0.0" }
    }
  }'
```

A successful interaction typically returns a JSON-RPC envelope (and may use streaming/SSE depending on client and server). Your client library usually handles follow-up calls (`notifications/initialized`, `resources/list`, etc.).

**Liveness (non-MCP):** operators often expose **`GET /health`** on the **origin** (without `/mcp`) for uptime checks—use that for probes that should **not** speak MCP.

---

## 6. Example validation response

Responses depend on protocol version and transport framing. An **illustrative** successful JSON-RPC body might look like:

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "protocolVersion": "2024-11-05",
    "capabilities": {
      "resources": {},
      "tools": {},
      "prompts": {}
    },
    "serverInfo": {
      "name": "example-aadm-mcp",
      "version": "0.0.0-placeholder"
    }
  }
}
```

Your actual payload may differ (additional keys, streaming chunks, or session headers such as **`Mcp-Session-Id`** on the HTTP response). Treat this sample as **shape-only**, not a conformance test.

---

## 7. Common errors

| Symptom | Likely cause | What to try |
|---------|----------------|-------------|
| **406 Not Acceptable** on `GET` or `POST` `/mcp` | Missing or wrong **`Accept`** header (browser default often omits `text/event-stream`) | Use both `application/json` and `text/event-stream` on **`POST /mcp`** per Streamable HTTP expectations; don’t treat a bare browser GET as a health check for MCP. |
| **404 Session not found** | Session id lost, replica mismatch, or client reconnecting without prior **`initialize`** | Re-run session setup; if the server runs behind multiple instances, ask operators whether **sticky routing** is required for your session id. |
| **Connection refused / TLS errors** | Wrong host, port, or corporate proxy | Confirm **`https://`** vs **`http://`**, path **`/mcp`**, and VPN/proxy rules. |
| **401 / 403** | Auth missing or key scoped incorrectly | Verify API key or auth header requirements with the server operator. |
| **Empty or confusing stream** | Parsing SSE vs JSON manually | Prefer the **official MCP client** for your environment instead of hand-rolled parsers. |

---

## 8. Security reminders

- **Least privilege** — Keys should allow **MCP access only**, not admin access to unrelated systems.  
- **No secrets in repos** — Keep placeholders in docs; inject real values via OS env or secret manager.  
- **TLS** — Prefer HTTPS for remote servers; pin expectations per your org policy.  
- **Data handling** — Anything you send in prompts or tool args may be logged by **clients or servers**; avoid pasting production data into probes.  
- **Defense in depth** — MCP improves **context**; it does **not** replace **application AUTH**, code review, or CI.

See also [AUTH-aware delivery](auth-aware-delivery.md).

---

## 9. Next steps

1. Obtain the **MCP base URL** (`…/mcp`) and **auth** requirements from your **server operator**.  
2. Add the server to your **IDE MCP configuration** (stdio wrapper or direct URL—per vendor docs).  
3. Run the **`initialize`** probe (section 5), then list **resources/prompts/tools** using your client.  
4. Align tickets with [Build intent specification](build-intent-specification.md) and record **MCP validation status** in [BIS-lite](../templates/bis-lite.md) when your process calls for it.  
5. Read [Compliance checklist](compliance-checklist.md) if governance teams need traceability beyond tooling.

---

## Further reading

- Model Context Protocol documentation for **your** client and SDK version  
- [Glossary](glossary.md) — MCP, AUTH, build intent  
