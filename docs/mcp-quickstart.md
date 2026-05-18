# Using the AADM MCP

The markdown in **this repository** is the portable **AADM Standard**—for humans, tickets, and reviews. When you want **coding agents** to load the same definitions, workflows, and bounded tools in a consistent way, use the **hosted AADM MCP** documented with the product.

---

## Where to go

| What you need | Where |
|----------------|--------|
| **Overview, product context, and step-by-step connection guides** | **[https://www.aadm.io](https://www.aadm.io)** |
| **MCP endpoint** (configure your client with the URL below; follow **www.aadm.io** for auth, headers, and sessions) | **`https://mcp.aadm.io/mcp`** |

**Use [www.aadm.io](https://www.aadm.io) as the source of truth** for how to connect your IDE or gateway. This doc only lists the **canonical endpoint** so repositories can link without repeating instructions that may change on the service side.

---

## Boundaries (quick reminders)

- **MCP supplies context and agent-facing workflows**, not your product’s business authorization. Application code, gateways, and CI remain authoritative ([AUTH-aware delivery](auth-aware-delivery.md)).  
- **Pass/fail** for behavior stays in **your** tests and pipelines ([Repository scope](repository-scope.md)).  
- Protect MCP credentials like any internal API: **least privilege**, **no secrets in git**.

## Citation namespaces (assurance & trace)

When agents or auditors cite findings, keep families distinct:

| Prefix | Use for |
|--------|---------|
| **AUTH-NN** | Governance obligation |
| **SEAM-NN** | Handoff archetype in scope ([`standards/seam-catalog.md`](../standards/seam-catalog.md); MCP: **`fetch_seam`**) |
| **TRACE-NN** | Contract drift pattern |
| **VERT_* / HORI_*** | Sentinel attestation depth / boundary kind |

Persistent column traces alone miss in-process normalization and dispatch—include **SEAM-04** / **SEAM-05** when agent or recipe ids are synthesized without a database anchor.

---

## Related

- [Documentation index](README.md)  
- [Repository scope](repository-scope.md)  
