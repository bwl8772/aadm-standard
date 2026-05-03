# Mock MCP validation output (example only)

**This file is entirely fictional.** It shows what a team might **paste into a ticket or design doc** after running an **external** MCP-assisted review workflow.

---

## What this is not

- **Not** output produced by this **aadm-standard** repository (there is **no MCP runtime** here).  
- **Not** a live API response from any deployed server.  
- **Not** proprietary scoring or validation internals—field names are **illustrative**.

---

## Scenario (fictional)

A developer uses their IDE’s MCP client against **their organization’s** AADM MCP deployment (hosted elsewhere). They load **public standard excerpts** (or mirrored docs) and record a **manual checklist result** plus a **mock JSON record** for traceability.

---

## Mock MCP validation record

Below is **fabricated JSON** for documentation practice only.

```json
{
  "validation_record_kind": "MOCK_EXAMPLE_DO_NOT_USE_FOR_PRODUCTION",
  "generated_at": "2026-05-02T12:00:00Z",
  "workspace_context": "matchgrid-example-docs-only",
  "capability_id": "matchgrid-ai-action-plan-v1",
  "standard_sources_claimed": [
    "public AADM docs — udali-22-layer-model",
    "public AADM docs — auth-aware-delivery",
    "public AADM docs — build-intent-specification"
  ],
  "checks": [
    {
      "id": "UDALI-MAP-PRESENT",
      "result": "pass",
      "notes": "layer-map.md maps Unifier through Integrator"
    },
    {
      "id": "AUTH-CONFIRM-PATH",
      "result": "pass",
      "notes": "bis-lite states confirm requires server-side AUTH"
    },
    {
      "id": "HUMAN-CHECKPOINT-DOCUMENTED",
      "result": "pass",
      "notes": "Human confirm called out in use case and bis-lite"
    },
    {
      "id": "SCHEMA-ALIGNMENT",
      "result": "pass",
      "notes": "sample-schema.json requires goal + ≥1 step"
    }
  ],
  "limitations": [
    "No automated compliance verdict",
    "No connection to MatchGrid production systems",
    "No model inference performed inside this repo"
  ],
  "operator_signature": "mock-operator@example.invalid"
}
```

---

## How teams should use real MCP (high level)

1. Connect the client to **your** MCP endpoint per [`docs/mcp-quickstart.md`](../../docs/mcp-quickstart.md).  
2. Keep **secrets** out of git; store validation summaries in your **internal** ticket system if needed.  
3. Never treat MCP output as **authorization** to skip code review or tests.

---

## Relationship to artifacts in this folder

| Artifact | Role |
|----------|------|
| [`bis-lite.md`](bis-lite.md) §13 | Where a team might **reference** a real validation id |
| This file | **Demo-only** mock for learners |
