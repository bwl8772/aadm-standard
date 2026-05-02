# Adoption guide

Practical steps for teams adopting AADM **without** importing proprietary tooling.

## 1. Align on vocabulary

Run a one-hour workshop using:

- [`docs/what-is-aadm.md`](../docs/what-is-aadm.md)  
- [`docs/udali-22-layer-model.md`](../docs/udali-22-layer-model.md)  
- [`docs/glossary.md`](../docs/glossary.md)

Outcome: shared understanding of layers and roles.

## 2. Map your reality

Create an internal appendix: **your services ↔ UDALI layers**. Expect disagreement early; converge on a working map.

## 3. Wire templates into your workflow

Copy [`templates/`](../templates/) into your wiki or ticket system. Minimum set:

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

## 6. Optional MCP context servers

If your organization exposes standard docs via MCP:

- Follow [`docs/mcp-quickstart.md`](../docs/mcp-quickstart.md)  
- Never substitute MCP connectivity for application authorization  

## 7. Measure adoption health

Indicators:

- Fewer surprise cross-layer fixes  
- Faster reviews due to clearer artifacts  
- Incidents traceable to explicit contracts and owners  

## Getting help

Improvements belong in public issues—see [`CONTRIBUTING.md`](../CONTRIBUTING.md).
