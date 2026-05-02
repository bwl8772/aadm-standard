# Customer story — MatchGrid AI task assistant (example)

## Outcome

Users turn **vague intentions** into **reviewable action plans**—ordered steps, risks called out, and questions surfaced—without leaking authority to automation. MatchGrid stays accountable: **people confirm** before anything durable is saved or assigned.

---

## User story

**As a** MatchGrid **member juggling unclear work**, **I want** an AI assistant to **propose a structured action plan from a rough description**, **so that** I can **edit, approve, and share** a concrete plan instead of rewriting the same ambiguity in chat threads.

---

## Context (fictional)

**Sam** runs a small delivery pod. Tasks often arrive as half-sentences in notes. Engineers waste cycles asking “what did you mean?” The assistant is meant to **accelerate clarity**, not **decide priority or permissions** for others.

---

## Acceptance themes (high level)

- Proposals are **editable** and **non-binding** until Sam confirms.  
- Plans **scoped to Sam’s workspace**; no cross-tenant bleed.  
- **Destructive or assigning actions** require explicit confirmation and policy checks.  

Detailed checks live in [`use-case.md`](use-case.md) and [`bis-lite.md`](bis-lite.md).

---

## Related artifacts

- [`use-case.md`](use-case.md) — UC-MG-101 Turn vague task into structured action plan  
- [`bis-lite.md`](bis-lite.md) — Build intent  
- [`layer-map.md`](layer-map.md) — UDALI-style mapping  
