# Operational design heuristics — singletons and repetition signals

> When is a singleton justified versus harmful? When do repeated bugs or repeated "asks" mean the work should escalate from tactical patches to structural improvement? Stack-agnostic.

## Part A — When to think about adding a singleton

A **singleton** here means **one shared instance** for a type across the process (or a narrowly scoped "single instance" pattern).

### Favor a singleton or single shared instance when several are true

1. **True single coordinated resource.** Exactly one handle must exist (connection pool facade, process-wide metrics registry, platform SDK that forbids multiple init).
2. **Stable, stateless or immutable after init.** Behavior does not vary per user or tenant in ways that would require isolation, **unless** you explicitly partition inside it (e.g. keyed stores).
3. **Clear ownership and lifecycle.** Startup, shutdown, and failure modes are documented; tests can substitute or reset via seams you control.
4. **Alternatives were heavier.** Duplex wiring or per-call factories would obscure the real invariant ("there is one policy engine for this runtime").
5. **Team agrees on testing strategy.** Mocking or replacement path exists (interface, provider swap, test container) — not "static forever."

### Pause or avoid a singleton when several are true

1. **Hidden mutable global state.** Anything that accumulates request context, user prefs, or tenant data without explicit partitioning is a footgun (especially multi-tenant).
2. **"Convenient grab bag."** Global locator that grows until everything imports `TheApp.getInstance()` — coupling explosion.
3. **Hard to test.** Tests need ordering tricks, sleeps, or env hacks to isolate behavior.
4. **Duplicate domains.** Two features each define "the" cache or "the" client — you likely need one bounded module and explicit interfaces, not two singletons.
5. **UDALI cross-band smell.** UI or controller code reaches for a global to skip proper handoff; domain rules hide inside a global.

### Prefer before defaulting to "singleton"

| Instead of | Consider |
|------------|----------|
| Global `getInstance()` everywhere | Constructor injection + one composition root |
| Static mutable fields | Request-scoped or explicit context parameter |
| Second singleton "for convenience" | Merge misplaced seams or introduce a facade with one lifetime |

**Non-coder test.** "If two tenants or two tests ran interleaved, could this shared thing leak state?" If yes, fix partitioning before naming it a singleton.

---

## Part B — When to surface code improvement (repeated bugs or repeated asks)

**Repeated bugs** and **repeated asks** (human or agent session) are **signals**, not automatic mandates. The goal is to stop paying the same tax without skipping evidence.

### Repeated bugs — escalate toward structural work when

1. **Same failure signature.** Same exception class, same seam (route, persistence access, auth check), or same race pattern appears **more than twice** with independent triggers.
2. **Fixes are local but violations recur.** Each patch "works" but the **class** of mistake keeps appearing (copy-paste drift, missing invariant).
3. **Evidence points to a missing invariant.** No single test or assertion guards the rule everyone assumes.
4. **Blast radius grows.** Each incident touches more callers or more data shapes.

**Before refactoring.** Use [Investigator discipline](three-lanes.md#investigator--evidence-gated-defect-resolution) — anchors, classification, scoped remediation — not a broad rewrite from frustration.

**Structural responses (pick proportionate depth):**

| Depth | Examples |
|-------|----------|
| Thin | Clearer error at the seam, logging correlation id, doc note |
| Medium | Shared validator, one helper behind a stable Interface, contract test |
| Heavy | Extract orchestration module, introduce explicit workflow object, ADR + migration |

### Repeated "asks" in a session — escalate when

1. **Same clarification loop.** User or agent re-asks how something works, where code lives, or what owns a seam multiple times after answers were given.
2. **Same workaround ritual.** "Always run X before Y" or "ignore that warning" — signals missing UX, docs, or automation.
3. **Same navigation cost.** Finding the right module or policy repeatedly means **discovery failure** (structure, naming, or docs).

**Responses — distinguish gap type:**

| If the gap is… | Prefer… |
|----------------|---------|
| Missing explanation | Doc, README slice, or runbook pointer |
| Ambiguous published surface | Rename, types, or explicit module seam |
| Fragile behavior | Test + small interface guard |
| Repeated defect shape | Investigator trail → shared invariant or seam fix |

**Agent-oriented rule.** After **two** similar user asks on the same topic in one thread, **stop** and propose **one** bundled outcome: short doc stub, checklist, or scope statement — not a third informal answer that diverges from the first.

---

## Tie-in to AADM lanes

- **Defect-shaped repetition.** Investigator leads; execution-control and scope guardrails apply — no premature mega-refactor.
- **Delivery-shaped repetition** ("we keep rebuilding X"). Navigator + patterns such as [`shared-backend-separate-pages.md`](shared-backend-separate-pages.md) when pages duplicate workflow logic.
- **Assurance-shaped repetition** ("we keep debating if we're aligned"). [Sentinel program](sentinel-program.md) + [evidence model](../GOVERNANCE.md) — bind proof to seams, not opinions.

---

## Closing

**Singletons** solve **coordination and lifecycle** problems — not **coupling** problems you want to ignore.

**Repetition** means **pay attention** — classify whether you owe docs, a narrow code invariant, or a bounded refactor — and use the right lane (Investigator vs Navigator vs Sentinel) before widening scope.
