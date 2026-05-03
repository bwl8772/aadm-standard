# Adoption guide

Practical steps for teams adopting AADM. **Use this repository for shared intent and templates**, and **add an AADM MCP server** when coding agents should load **canonical** standard context—both together reduce drift; neither replaces application AUTH.

## 1. Align on vocabulary

Run a one-hour workshop using:

- [`docs/what-is-aadm.md`](../docs/what-is-aadm.md)  
- [`docs/udali-22-layer-model.md`](../docs/udali-22-layer-model.md)  
- [`docs/glossary.md`](../docs/glossary.md)

Outcome: shared understanding of layers and roles.

## 2. Map your reality

Create an internal appendix: **your services ↔ [UDALI](../docs/udali-22-layer-model.md) groupings**. Expect disagreement early; converge on a working map.

## 3. Wire templates into your workflow

Copy [`templates/`](../templates/) (see [`templates/README.md`](../templates/README.md)) into your wiki or ticket system. Minimum set:

- `bis-lite.md` + `layer-map.md` for engineering-led changes  
- `acceptance-criteria.md` for completion discipline  

## 4. Add architecture checkpoints

Before large merges, require:

- Layer map attached  
- Contract notes for boundary changes  
- AUTH summary if roles or sensitive data move  

## 5. Govern automation

If developers use AI agents:

- Publish **allowed scopes** (read-only docs vs code generation)  
- Require human approval for production-impacting merges  
- Treat agent outputs as **reviewable drafts**

## 6. Connect an AADM MCP server (recommended for agent-heavy teams)

Static markdown prevents nothing by itself—agents need **stable distribution**. After templates are in place:

- Follow [`docs/mcp-quickstart.md`](../docs/mcp-quickstart.md) to connect your IDE or gateway and to copy **current tool ids** (`standard_brief`, `debug_defect`, …); reconcile internal wikis if they still say `aadm_capabilities`, `aadm_troubleshooter`, `map_feature_to_layers`, or `udali_route_delivery`.  
- Teach leads the **three workflow personas** reference bundles publish—**Navigator** (delivery), **Investigator** (defects), **Sentinel** (assurance across seams)—so tickets route to the right framing; Sentinel is **not** a substitute for defect tooling when the ask is a concrete bug.  
- Keep MCP credentials **scoped**; never expose production secrets for “context only.”  
- Remember: MCP **does not** substitute for application authorization—see [`docs/auth-aware-delivery.md`](../docs/auth-aware-delivery.md).

## 7. Measure adoption health

Indicators:

- Fewer surprise cross-layer fixes  
- Faster reviews due to clearer artifacts  
- Incidents traceable to explicit contracts and owners  

## Getting help

Improvements belong in public issues—see [`CONTRIBUTING.md`](../CONTRIBUTING.md).
