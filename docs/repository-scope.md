# Repository scope — standard, testing, and vocabulary

This repository is the **public home of the AADM standard**: narrative guidance, normative shorts, copy-paste templates, and learning examples. Use it to align humans, tickets, and reviews on **intent**, **layer placement**, **AUTH**, and **evidence**.

## Using the AADM MCP (hosted)

To connect coding agents to the **hosted** standard context server:

- **Product site and connection instructions:** [https://www.aadm.io](https://www.aadm.io)  
- **MCP endpoint:** `https://mcp.aadm.io/mcp`  
- **Short pointer in this repo:** [Using the AADM MCP](mcp-quickstart.md)  

This repository does **not** ship MCP server source or duplicate full integration docs—those live with the service on **www.aadm.io**.

## What lives here

- **Concepts and personas** — [What is AADM?](what-is-aadm.md), [UDALI coding personas](udali-personas.md), optional [L1–L22](udali-22-layer-model.md) slice  
- **Architecture and intent** — [Architecture principles](architecture-principles.md), [Build intent specification](build-intent-specification.md)  
- **Vocabulary** — [Glossary](glossary.md), including design terms (**Module**, **Interface**, **Seam**) and delivery lanes (**Navigator**, **Investigator**, **Sentinel**) described **without** reference to any particular runtime  
- **Assurance framing** — [Sentinel audit programs](sentinel-audit-programs.md), [Compliance checklist](compliance-checklist.md)  
- **Templates and examples** — [`../templates/`](../templates/), [`../examples/`](../examples/)  
- **Agent skills alignment** — [Creating and aligning skills with the AADM standard](skills.md)

## Testing and proof

AADM documentation does **not** replace execution discipline in your application. Claims about correctness, security, or readiness should be backed by **your** tests, CI gates, reviews, and operational controls—as summarized in [Architecture principles](architecture-principles.md) (*Evidence-backed claims*) and the [Compliance checklist](compliance-checklist.md) QA section.

## What is intentionally light or out of scope here

- **MCP server implementation** and **editor-specific** copy-paste blocks — follow **[www.aadm.io](https://www.aadm.io)** for the live integration story.  
- **Private or org-only** endpoints — not documented in this public repo.  

Otherwise this repo stays **portable markdown** so any team can adopt the standard with or without the hosted MCP.

---

**Shortcut:** [Documentation index](README.md) · [Project README](../README.md)
