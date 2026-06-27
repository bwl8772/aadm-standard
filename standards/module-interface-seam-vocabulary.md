# Module–Interface–Seam vocabulary

> Shared vocabulary for architecture review. Use these terms **exactly** in suggestions — do not substitute "component," "service," "interface" (narrow sense), "API," or "boundary" when naming design concepts. Consistent language is the whole point. Stack-agnostic.

## Why a fixed vocabulary

Architecture review breaks down when the same English word means different things to different reviewers — "interface" means a TypeScript keyword to one reviewer and a contract surface to another. This document fixes the words.

---

## Terms

### Module

Anything with an **Interface** and an **Implementation**. Deliberately scale-agnostic — applies equally to a function, class, package, or tier-spanning slice.

**Avoid.** "Unit," "component," "service" as generic substitutes for "thing we're reviewing."

### Interface

Everything a **caller** must know to use the **Module** correctly. Includes the type-level surface, but also **invariants**, **ordering constraints**, **error modes**, **required configuration**, and **performance characteristics** callers rely on.

**Avoid.** "API," "signature" — too narrow; those refer only to the type-level surface.

### Implementation

What is inside a **Module** — its body of code. Distinct from **Adapter**: a thing can be a small **Adapter** with a large **Implementation** (a Postgres-backed repository) or a large **Adapter** with a small **Implementation** (an in-memory fake). Reach for **adapter** when the **seam** is the topic; **implementation** otherwise.

### Depth

**Leverage** at the **Interface** — the amount of behavior a caller (or test) can exercise per unit of **Interface** they have to learn.

A **Module** is **deep** when a large amount of behavior sits behind a small **Interface**. A **Module** is **shallow** when the **Interface** is nearly as complex as the **Implementation**.

**Rejected framing.** Depth as a ratio of implementation lines to interface lines — that rewards padding the **Implementation**. This standard uses **depth-as-leverage** only.

### Seam (Michael Feathers)

A place where you can **alter behavior without editing in that place**. The location at which a **Module**'s **Interface** lives. Choosing **where** to put the **seam** is its own design decision, distinct from what goes behind it.

**Avoid.** "Boundary" (overloaded with DDD bounded context and other uses).

### Adapter

A **concrete** thing that satisfies an **Interface** at a **seam**. Describes role (what slot it fills), not substance (what is inside).

**Important — name collision with UDALI.** UDALI's "Adapter band" refers to L9–L12 (repository ports, persistence, auth orchestration, cache). When discussing band placement, say **Adapter band** or **UDALI Adapter (L9–L12)**. When discussing a concrete implementation at a seam, say **adapter** (lowercase, plain text).

### Leverage

What **callers** get from **depth**. More capability per unit of **Interface** they have to learn. One **Implementation** pays back across N call sites and M tests.

### Locality

What **maintainers** get from **depth**. Change, bugs, knowledge, and verification concentrate in **one** place rather than spreading across callers. Fix once, fixed everywhere.

---

## Principles

1. **Depth is a property of the Interface, not the Implementation.** A deep **Module** can be internally composed of small, mockable, swappable parts — they are not part of the **Interface**. A **Module** can have **internal seams** (private to its **Implementation**, used by its own tests) as well as the **external seam** at its **Interface**.

2. **The deletion test.** Imagine deleting the **Module**. If complexity vanishes, the **Module** was not hiding anything (it was a pass-through). If complexity reappears across N callers, the **Module** was earning its keep.

3. **The Interface is the test surface.** Callers and tests cross the same seam. If you want to test past the **Interface**, the **Module** is probably the wrong shape.

4. **One adapter means a hypothetical seam. Two adapters means a real one.** Do not introduce a **seam** unless something actually varies across it.

---

## Relationships (canonical)

- A **Module** has exactly one **Interface** (the surface it presents to callers and tests).
- **Depth** is a property of a **Module**, measured against its **Interface**.
- A **Seam** is where a **Module**'s **Interface** lives.
- An **Adapter** sits at a **Seam** and satisfies the **Interface**.
- **Depth** produces **Leverage** for callers and **Locality** for maintainers.

---

## Rejected framings (recap)

- **Depth** as a line-count ratio — use **depth-as-leverage** (**Leverage** and **Locality** at the **Interface**) instead.
- **Interface** as only the TypeScript `interface` keyword or a class's public method list — too narrow.
- **Boundary** for this vocabulary — say **seam** or **Interface** (full contract).

---

## Coexistence with UDALI, AUTH, and SEAM

| Vocabulary | What it names |
|------------|---------------|
| **Module / Interface / Seam / Adapter / Depth / Leverage / Locality** (this doc) | Design vocabulary for one module under review. |
| **UDALI L1–L22** | Placement — *where* in the architecture a module belongs. |
| **AUTH-NN** | Obligations — *what* must be enforced. |
| **SEAM-NN** (catalog) | Auditable handoff archetypes — *which crossings to scope.* |
| **VERT_** / **HORI_** | Sentinel attestation geometry. |

These vocabularies coexist. A design review may legitimately reference all of them: "The Module's Interface lives at a SEAM-03 handoff in the UDALI Adapter band (L9–L10), bound by AUTH-21."

---

## When describing HTTP ingress or routes

Use **Interface at a Seam** or UDALI layer ids (**L19**, **L20**) — not "API" as the generic word for the contract.

---

## Related

- [`seam-catalog.md`](seam-catalog.md) — SEAM-NN archetype catalog.
- [`udali-auth-enforcement-mapping.md`](udali-auth-enforcement-mapping.md) — UDALI ↔ AUTH default placement.
- [UDALI personas](../docs/udali-personas.md) — the five bands.
- [`operational-design-heuristics.md`](operational-design-heuristics.md) — when adding a singleton is justified.
