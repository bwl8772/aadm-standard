# Adoption guide

Practical steps for teams adopting AADM. **Use this repository for shared intent and templates**; pair with **your** tests, CI, and governance—documentation does not replace application AUTH.

## 1. Align on vocabulary

Run a one-hour workshop using:

- [`docs/what-is-aadm.md`](../docs/what-is-aadm.md)  
- [`docs/udali-personas.md`](../docs/udali-personas.md)  
- [`docs/udali-22-layer-model.md`](../docs/udali-22-layer-model.md)  
- [`docs/glossary.md`](../docs/glossary.md) — include **Module**, **Interface**, **Seam**, and delivery lanes (**Navigator** / **Investigator** / **Sentinel**)

Outcome: shared understanding of layers, roles, and review routing.

## 2. Map your reality

Create an internal appendix: **your systems ↔ [UDALI](../docs/udali-personas.md) groupings** (optional [L1–L22](../docs/udali-22-layer-model.md)). Expect disagreement early; converge on a working map.

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

If you maintain **agent skills** (packaged playbooks), align them with lanes and vocabulary—see [`docs/skills.md`](../docs/skills.md) (*Creating and aligning skills with the AADM standard*).

## 6. Connect coding agents (hosted MCP)

Use the **hosted AADM MCP** when agents should load standard context and tools consistently:

- **Instructions and product context:** [https://www.aadm.io](https://www.aadm.io)  
- **MCP endpoint:** `https://mcp.aadm.io/mcp`  
- **Shortcut in this repo:** [`docs/mcp-quickstart.md`](../docs/mcp-quickstart.md)  

Least-privilege credentials only; MCP does **not** replace application AUTH ([`docs/auth-aware-delivery.md`](../docs/auth-aware-delivery.md)).

## 7. Tie claims to tests

For each material initiative:

- Name **CI jobs** or suites that must stay green before release  
- Prefer **falsifiable** checks at declared **Interfaces** and **Seams** ([Glossary](../docs/glossary.md))  
- Use [`docs/compliance-checklist.md`](../docs/compliance-checklist.md) as a periodic self-assessment  

## 8. Measure adoption health

Indicators:

- Fewer surprise cross-layer fixes  
- Faster reviews due to clearer artifacts  
- Incidents traceable to explicit contracts and owners  

## Getting help

Improvements belong in public issues—see [`CONTRIBUTING.md`](../CONTRIBUTING.md).
