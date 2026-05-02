# UDALI layer boundaries

Layers organize **accountability**. These boundary rules reduce accidental coupling—especially under agent-assisted delivery.

## Rules

1. **A fix belongs to the layer where the invariant lives.** Do not patch symptoms in a distant layer without addressing the owning concern.  
2. **Cross-layer changes declare all touched layers** in the layer map for the change.  
3. **Contracts are the narrowest stable surfaces** between layers and teams. Prefer evolving contracts deliberately.  
4. **Presentation layers do not silently reinterpret authorization.** UI convenience must not replace server-side enforcement.  
5. **Integration code isolates foreign models** behind explicit adapters—do not spread provider idioms through domain cores.  
6. **Persistence choices do not leak across domains** without an articulated mapping and migration strategy.  
7. **Operational mechanics** (scaling, retries) must not obscure business outcomes—traceability remains visible.

## Anti-patterns

- “Quick fix” validation only at the edge while domain operations remain unchecked  
- Agents generating database access scattered across unrelated modules  
- Shared DTOs that merge unrelated contexts into one unmaintainable type  

## Reference

[`docs/udali-22-layer-model.md`](../docs/udali-22-layer-model.md)
