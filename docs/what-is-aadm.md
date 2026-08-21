# What is AADM?

**AADM** (**Agentic Authority Delivery Model**) is an **open delivery posture** for building modern software—including systems that use AI agents—without surrendering structural integrity.

AADM is **not** a statute, ISO publication, or automatic “certification.” It is **documentation-first guidance** your organization may adopt, extend, or pair with **your own** governance and tooling.

## The problem AADM addresses

Fast iteration and agent assistance often collapse boundaries:

- Implementation starts before architecture intent is stable.  
- Automation reaches across layers that should remain separated.  
- Authorization and data access become implicit “someone will fix it later.”  

AADM responds with a **small set of enforceable ideas**:

1. **Intent before churn** — what you are building, for whom, and under what constraints is written down and revisable.  
2. **Named layers and roles** — work is placed in a consistent model so handoffs are explicit (see [UDALI coding personas](udali-personas.md) and [UDALI 22-layer model](udali-22-layer-model.md)).  
3. **Human accountability** — people remain responsible for high-risk decisions; automation assists within declared limits ([Human in the loop](../standards/human-in-the-loop.md)).  
4. **No accidental monolith of concerns** — unrelated responsibilities are not fused into a single unmaintainable surface ([No monolith rule](../standards/no-monolith-rule.md)).
5. **Control wraps generation** — a model that can act is surrounded by deterministic code that decides whether each action is permitted ([Agent runtime harness](../standards/agent-runtime-harness.md)).

## Four systems in one product

A product that uses AI is not one system with a model bolted on. It is four, and they have different rules. Most confusion about "how do we build this safely" is really a failure to say which of the four is under discussion.

| System | What it does | Behaves |
|--------|--------------|---------|
| **Deterministic** | The ordinary application — data, domain rules, contracts, persistence | The same way every time |
| **Generative** | Produces content on request: a summary, a draft, a classification | Differently each time, but only when called |
| **Agentic** | Chooses its own next step and acts through tools | Differently each time, and on its own initiative |
| **Control** | Decides whether a proposed step is permitted: budgets, authorization, gates, records, evaluation | Deterministically, always |

The fourth is the one that gets left out. **Control is not a peer of the other three — it is the wrapper around the second and third.** The generative and agentic systems are non-deterministic by nature, which is exactly why the thing governing them cannot be. When teams describe an agent as "a model plus tools plus a prompt," the control system is the missing component, and its duties fall by default to the model — the one part that can never carry a guarantee.

## Generative or agentic?

These two get the same label and need very different engineering.

| | Generative | Agentic |
|---|---|---|
| **Initiative** | You call it | It decides to act |
| **Shape** | One turn: input, output | A loop: propose, admit, execute, repeat |
| **Effects** | None of its own — you apply the output | Its own, through tools |
| **Needs** | Output constraint ([AUTH-36](../standards/auth-catalog.md#auth-36--output-constraint-authority)), prompt governance ([AUTH-35](../standards/auth-catalog.md#auth-35--prompt-governance-authority)) | All of that, plus the full control wrapper: budgets, per-call authorization, trust classification, run records, terminal states |

Most features called "AI" are generative. Getting the distinction wrong costs in both directions: an agentic capability built with generative discipline has no bounds at all, and a generative feature built with agentic apparatus carries machinery it will never use.

The test is not whether a model is involved. It is whether anything happens without a human asking for it.

## Choosing the least powerful capability that works

Reach for the weakest option that satisfies the requirement, and move up only when it genuinely fails.

1. **Deterministic code.** If the rule can be written down, write it down. Predictable, testable, free at runtime.
2. **Deterministic code plus a generative step.** Judgement or language is needed, but a human still applies the result.
3. **A single agent with a control wrapper.** Something must act on its own initiative, across several steps.
4. **More than one agent.** Only for a different authority, a different trust domain, or genuinely independent judgement ([Multi-agent restraint](../standards/multi-agent-restraint.md)).

Each step up buys capability and pays in non-determinism, cost, latency, and control surface. The heuristic exists because the pressure runs the other way: agentic is the interesting option, and it is easy to reach for it when a function would have been better and provable.

## What AADM is not

- **Not a framework** you install to “solve security.”  
- **Not a replacement** for language-specific best practices or platform controls.  
- **Not a legal or regulatory certification**—this public repo offers no exams, seals of approval, or proprietary scoring engines.  
- **Not a runtime** — this repository publishes **markdown** for humans and tickets; proof lives in **your** tests and CI ([Repository scope](repository-scope.md)).

## Standard artifacts vs proof

- **Here:** principles, templates, examples, and vocabulary—easy to fork, review in PRs, and attach to tickets.  
- **In your application:** failing tests, CI gates, code review, and operational signals that **prove** behavior.  

Shared language helps: **Module**, **Interface**, **Seam**, UDALI placement, AUTH themes, and delivery lanes (**Navigator**, **Investigator**, **Sentinel**)—see [Glossary](glossary.md).

## The build sequence

The order for delivering an agentic capability. It is one list because the steps are usually scattered across separate documents and separate weeks, and the scattering is what produces an agent shipped with its controls still on the roadmap.

1. **Write the intent.** What this capability is for, for whom, and its non-goals ([Build intent specification](build-intent-specification.md)).
2. **Place it in layers.** Where each concern belongs before any of it is written ([Architecture before code](../standards/architecture-before-code.md)).
3. **Declare the four authorities.** Authentication, authorization, auditing, and — before an agent touches the repository — [development authority](../standards/development-authority.md).
4. **Build the deterministic core.** Everything that does not need a model. This is most of it, and it is what the agent will act through rather than around.
5. **Define the tool contract.** Narrow, typed, side-effect classified, idempotent, reversible, audited ([Agent tool design](../standards/agent-tool-design.md)). The tool surface is the permission model.
6. **Build the control wrapper.** Budgets, per-call authorization, trust classification, terminal states ([Agent runtime harness](../standards/agent-runtime-harness.md)). Before the model, not after.
7. **Wire the run record.** Every run, including denied steps ([Agent run record](../standards/agent-run-record.md)). This is the precondition for every later autonomy decision.
8. **Add the model.** Now — into a loop that already has bounds.
9. **Set the autonomy rung and its gates.** State the rung explicitly ([Human in the loop](../standards/human-in-the-loop.md)).
10. **Define the post-deployment metrics.** Before launch, so the first rung decision has a population to cite ([Agent evaluation](../standards/agent-evaluation.md)).
11. **Bind the evidence.** Paired tests for every obligation ([AUTH-24](../standards/auth-catalog.md#auth-24--test--verification-authority)); pass/fail lives in your CI.

Steps 5 through 7 are the ones commonly deferred, and deferring them is not a sequencing preference. An agent that reached production without a run record cannot later justify a higher autonomy rung, because the evidence that would justify it describes a period that was never recorded.

## Using the AADM MCP (hosted)

For **coding agents**, AADM also exposes a **hosted MCP**: overview and **connection instructions** on **[www.aadm.io](https://www.aadm.io)**, MCP URL **`https://mcp.aadm.io/mcp`**. This repo links there rather than duplicating setup steps—see [Using the AADM MCP](mcp-quickstart.md).

## Optional contract trace (TRACE) and SEAM scope

When the dominant risk is **data contract drift**, teams may add a **contract trace** slice: bound the slice, pick critical fields, walk material seams, record PASS / FAIL / DEFERRED with repo anchors. Practice is described in [Sentinel audit programs](sentinel-audit-programs.md) and the [Glossary](glossary.md).

Use **SEAM-NN** archetypes ([`standards/seam-catalog.md`](../standards/seam-catalog.md)) to declare **which crossing types** are in scope—including normalization and dispatch paths that never appear as database columns. Hosted MCP subscribers load archetypes with **`fetch_seam`** ([MCP quickstart](mcp-quickstart.md)).

## What you get from adopting AADM

- Shared vocabulary across engineering, architecture, and operations  
- Templates that turn fuzzy requests into reviewable artifacts  
- A disciplined place for agent-assisted work **without** pretending agents are architects-of-record  

## Relationship to UDALI

**UDALI** names roles and delivery groupings used throughout AADM materials. Think of AADM as the **delivery posture** and UDALI as the **organizing map**. See [UDALI coding personas](udali-personas.md) for the five groupings (U, D, A, L, I) and [UDALI 22-layer model](udali-22-layer-model.md) for the optional **L1–L22** slice.

## Next steps

- [UDALI coding personas](udali-personas.md) and [UDALI 22-layer model](udali-22-layer-model.md)  
- [Architecture principles](architecture-principles.md)  
- [Build intent specification](build-intent-specification.md)  
- [Glossary](glossary.md)  
- [Creating and aligning skills with the AADM standard](skills.md)  
- [Using the AADM MCP](mcp-quickstart.md) · [www.aadm.io](https://www.aadm.io)  
- [Repository scope](repository-scope.md)  
