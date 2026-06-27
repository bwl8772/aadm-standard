# Examples

Real engineering situations where the AADM standard changes how you work. Each example shows the **before** (what engineers typically do without structure) and the **after** (what AADM framing gives you) — focused on outcomes, not theory.

---

## Where the AADM Standard helps

| Situation | Without AADM | With AADM |
|-----------|-------------|-----------|
| New feature lands | One enormous PR, unclear review order, auth added later | Layer-aware slices with AUTH baked in from day one |
| Vague bug report | Grep, guess, rewrite, hope | Evidence-gated recon → classify → narrow fix |
| "Is this ready to ship?" | "Tests pass" and gut feeling | Attestation closure: required seams with evidence binding |
| Architecture review | Read code until confident or bored | Scoped dimensions, challenge questions per seam, prioritized risks |
| Auth decisions | Scattered across middleware, UI, and inline checks | Trust boundaries mapped into the plan before the first handler |

---

## Examples index

### Planning and delivery (Navigator lane)

| Example | What it shows |
|---------|---------------|
| [slicing-a-feature-into-layers.md](slicing-a-feature-into-layers.md) | Break "team invitations" into 9 ordered vertical slices with UDALI layer hints, AUTH tags, and acceptance criteria |
| [auth-aware-delivery-trust-boundaries.md](auth-aware-delivery-trust-boundaries.md) | Map trust boundaries for "org billing settings" into the plan before writing code — AUTH-NN controls per slice |
| [navigator-readiness-hr-app.md](navigator-readiness-hr-app.md) | Real story (pseudonymized): Navigator-shaped readiness — layer map, contracts, decider, hooks, tests prove behavior |

### Defects and investigation (Investigator lane)

| Example | What it shows |
|---------|---------------|
| [routing-a-vague-bug-report.md](routing-a-vague-bug-report.md) | Turn "notifications don't arrive" into structured recon → classification → narrow fix (no speculative rewrites) |
| [evidence-first-defect-resolution.md](evidence-first-defect-resolution.md) | Sanitized case: "save blocked" from stacked failures across read-model, client normalization, and write validation |

### Assurance and architecture (Sentinel lane)

| Example | What it shows |
|---------|---------------|
| [sentinel-attestation-feature-check.md](sentinel-attestation-feature-check.md) | Scope attestation dimensions for "scheduled send" → get required seam tiers → bind evidence per seam before release |
| [architecture-review-existing-module.md](architecture-review-existing-module.md) | Review an inherited "payment processing" module — vertical depth + horizontal seams → prioritized risk map |

### Worked project scaffold

| Example | What it shows |
|---------|---------------|
| [ai-task-assistant/](ai-task-assistant/) | MatchGrid-flavored vague task → structured action plan with BIS-lite, layer map, schema, and mock validation record |

---

## How to read these

1. **Start with your situation** — planning a feature? fixing a bug? proving readiness? Pick the matching lane.
2. **Read "Where the AADM Standard helps"** at the top of each example — that's the one-paragraph pitch.
3. **Follow the steps** — each example walks through the flow an engineer (or their agent) would follow.
4. **Note the MCP tool callouts** — if you have the hosted MCP connected, these tools automate the framing.

---

## Related

- [Templates](../templates/README.md) — copy-paste scaffolds for BIS-lite, issue slices, PRDs
- [Docs](../docs/README.md) — full reference (UDALI, AUTH, architecture principles, glossary)
- [Standards](../standards/README.md) — normative shorts (core principles, layer boundaries, seam catalog)
- [MCP quickstart](../docs/mcp-quickstart.md) — connect your agent to the hosted AADM MCP
