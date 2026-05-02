# Example project: MatchGrid — AI task assistant

## Outcome first

**Users ship unclear work with less thrash.** When someone types a fuzzy goal (“fix the onboarding mess”), MatchGrid’s **AI task assistant** proposes a **structured action plan**—ordered steps, clarifying questions, and explicit assumptions—so the user can **edit, approve, and execute** instead of staring at a blank task list.

This folder is a **fictional end-to-end slice** for learning **AADM** artifacts. It contains **markdown and JSON examples only**—no application code and **no MCP server runtime** (see [`mcp-validation-example.md`](mcp-validation-example.md) for **mocked** tool output).

### MatchGrid naming

The name **MatchGrid** and domain **matchgrid.co** are used here as a **plausible product setting** for documentation. This is **not** an official product specification for the live site; [**matchgrid.co**](https://www.matchgrid.co) may describe different offerings—treat this repo’s MatchGrid details as **illustrative only**.

---

## What this example demonstrates

1. **Outcome first** — README and [`story.md`](story.md) lead with value.  
2. **User story** — Canonical “As a / I want / so that” line.  
3. **Acceptance criteria** — Observable checks in [`use-case.md`](use-case.md) and [`bis-lite.md`](bis-lite.md).  
4. **UDALI delivery mapping** — Five groupings in [`layer-map.md`](layer-map.md) (aligned with [`docs/udali-22-layer-model.md`](../../docs/udali-22-layer-model.md)).  
5. **Basic schema** — [`sample-schema.json`](sample-schema.json) for a draft action plan payload.  
6. **Mock MCP validation** — [`mcp-validation-example.md`](mcp-validation-example.md): **fabricated** MCP-style response, clearly labeled; **the public standard repo does not ship or run an MCP server.**

---

## Files

| File | Purpose |
|------|---------|
| [`story.md`](story.md) | Customer story and outcome |
| [`use-case.md`](use-case.md) | Title, objective, actors, flows, acceptance criteria, postconditions |
| [`bis-lite.md`](bis-lite.md) | Build intent bridge (product authorize → architecture constrain → engineering execute) |
| [`layer-map.md`](layer-map.md) | Unifier / Designer / Adapter / Logician / Implementer |
| [`sample-schema.json`](sample-schema.json) | JSON Schema fragment for an action-plan draft |
| [`mcp-validation-example.md`](mcp-validation-example.md) | **Mock** MCP validation record (not live tooling) |

---

## Suggested reading order

1. [`story.md`](story.md)  
2. [`use-case.md`](use-case.md)  
3. [`bis-lite.md`](bis-lite.md)  
4. [`layer-map.md`](layer-map.md)  
5. [`sample-schema.json`](sample-schema.json)  
6. [`mcp-validation-example.md`](mcp-validation-example.md)  

Then compare to [`templates/README.md`](../../templates/README.md) and the [`docs/` index](../../docs/README.md).
