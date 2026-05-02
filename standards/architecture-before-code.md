# Architecture before code

## Statement

When ambiguity is high, teams SHOULD stabilize **architecture intent**—boundaries, contracts, AUTH posture, and verification strategy—**before** large-scale implementation.

## Rationale

Premature coding under time pressure (including agent-assisted coding) amplifies rework: contracts harden incorrectly, implicit assumptions spread, and fixes scatter across layers.

## Practical interpretation

“Architecture before code” does **not** forbid spikes or prototypes. It requires that spikes are **labeled**, **time-boxed**, and **disposable** unless promoted through review.

## Minimum artifacts before scaling implementation

- Problem and outcome clarity ([`docs/build-intent-specification.md`](../docs/build-intent-specification.md))  
- Layer map for the change ([`templates/layer-map.md`](../templates/layer-map.md))  
- Contract sketches at exposed boundaries  
- AUTH-sensitive operations identified  

## Promotion criteria

Promote prototypes when contracts, tests, and operational concerns align—not merely when demos succeed.
