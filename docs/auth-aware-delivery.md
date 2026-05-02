# AUTH-aware delivery

**AUTH** in AADM materials refers jointly to **authentication** (establishing identity) and **authorization** (deciding what an identity may do). Delivery is **AUTH-aware** when these concerns are **explicit**, **layer-placed**, and **reviewed**—not absorbed implicitly by agents, prompts, or convenience helpers.

## Why this matters for agentic systems

Agents and MCP-style tools can traverse documentation, generate code, and invoke APIs rapidly. Without discipline, teams risk:

- **Privilege bleed** — generated code performs actions beyond the user’s intent  
- **Ambient credentials** — tokens available to automation “just work” everywhere  
- **Authorization theater** — checks exist only at the UI while APIs remain porous  

AADM does not prescribe a specific identity product. It prescribes **clarity**.

## Separation of concerns

| Concern | Question | Typical placement |
|---------|-----------|-------------------|
| Authentication | Who is this caller? | Gateway, identity provider integration, session issuance |
| Authorization | What may they do to which resources? | Policy checks adjacent to domain operations |
| Auditing | Who did what, when? | Tamper-evident logs tied to identity context |

Authentication middleware should not silently substitute for domain authorization rules.

## Delivery checkpoints

For meaningful changes, capture:

1. **Actors** — human roles, service principals, agents (if any)  
2. **Resources** — data objects, configuration, administrative actions  
3. **Actions** — explicit verbs on resources  
4. **Deny-by-default posture** — default is refusal unless a rule permits  

Align these checkpoints with your [layer map](../templates/layer-map.md): authorization rules belong where domain meaning lives, not buried only in presentation layers.

## Agents and tools

When agents use tools (including MCP servers):

- Scope credentials **narrowly** to the agent’s task  
- Prefer **read-only** context servers where possible  
- Require **human approval** for destructive or broadly scoped operations  
- Treat tool outputs as **untrusted input** to your application unless validated  

## Documentation hygiene

- Never commit secrets, API keys, or customer identifiers—even in examples.  
- Reference policies and diagrams by link; rotate examples regularly.  

## Further reading

- [Architecture principles](architecture-principles.md)  
- [Compliance checklist](compliance-checklist.md)  
- [MCP quickstart](mcp-quickstart.md) (generic connectivity patterns)  
