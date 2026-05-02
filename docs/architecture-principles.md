# Architecture principles

These principles summarize how AADM expects teams to protect **architecture integrity** while delivering quickly—including with AI assistance.

## 1. Declared intent beats implicit convention

Architecture is not “what emerged from the last sprint.” Capture **why** the system is shaped as it is, what is frozen, and what is experimental. Revisit intent when behavior diverges.

## 2. Boundaries are contracts

Anything crossing a process or team boundary—HTTP APIs, events, batch files, agent tool calls—should have an explicit **contract**: schema, error model, compatibility expectations. Contracts are versioned and reviewed.

## 3. Authorization is not an afterthought

**Authentication** (who is this?) and **authorization** (what may they do?) must have **named homes** in your design. Agents and tools inherit the same discipline: no shadow privileges.

## 4. Thin edges, explicit cores

Push complexity to **well-tested domains** with narrow public surfaces. Avoid “god services” that simultaneously integrate, authorize, render, and persist.

## 5. Observability follows responsibility

If a component can deny service, corrupt data, or leak information, its owners must be able to **detect**, **diagnose**, and **audit** behavior—without relying on heroic manual tracing.

## 6. Human accountability for irreversible risk

Automation may propose; humans approve changes that are legally sensitive, financially irreversible, or broadly destructive. See [Human in the loop](../standards/human-in-the-loop.md).

## 7. Prefer replaceable parts

Design so major concerns can be swapped (providers, models, queues) without rewriting business meaning. This reduces vendor lock-in and makes experimentation safer.

## 8. Architecture before code churn

When ambiguity is high, spend cycles on **structure and constraints** before mass implementation. This is the thesis of [Architecture before code](../standards/architecture-before-code.md).

## 9. Map work to layers deliberately

Use the UDALI layer map to prevent silent cross-layer fixes—especially under agent pressure. See [UDALI 22-layer model](udali-22-layer-model.md).

## 10. Evidence-backed claims

Statements like “secure,” “correct,” or “meets our regulatory obligations” should point to **tests, reviews, or monitored signals** appropriate to the claim’s scope—without implying AADM or MCP tooling alone constitutes legal certification.

---

For concise normative wording your team can adopt verbatim, see [`standards/`](../standards/).
