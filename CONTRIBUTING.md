# Contributing to the AADM Standard

Thank you for helping improve a **public, documentation-first** standard for architecture-aware delivery. This repository is intentionally scoped to **explain**, **template**, and **example**—not to host proprietary runtimes or vendor-specific enforcement engines.

## What belongs here

**In scope**

- Clarifying definitions, boundaries, and adoption guidance  
- Templates for stories, use cases, lightweight specs, and layer maps  
- Worked examples that teach patterns without exposing private IP  
- Standards documents that teams can adopt or fork  

**Out of scope**

- Source code for commercial products  
- Client-specific templates or confidential materials  
- Proprietary scoring, certification engines, or internal orchestration  

If you are unsure, open a discussion first (see below).

## How to contribute

1. **Open an issue** for substantive proposals (new standard sections, terminology changes, breaking clarifications).  
2. **Fork and branch** using the prefixes below.  
3. **Keep changes focused**—one logical improvement per pull request.  
4. **Match tone**: authoritative, practical, and neutral (no marketing claims without evidence).  
5. **Cite sources** when referencing external frameworks or regulations.

## Branch naming

Use these prefixes (examples):

| Prefix      | Use |
|------------|-----|
| `main`     | Release-ready default branch |
| `develop`  | Integration branch (if used by maintainers) |
| `feature/` | New sections, templates, or examples |
| `docs/`    | Documentation-only edits |
| `standard/` | Normative wording in `standards/` |
| `template/` | Changes under `templates/` |
| `example/` | Changes under `examples/` |
| `fix/`     | Corrections, typos, broken links |
| `exp/`     | Experimental drafts (clearly labeled) |

## Style guide for markdown

- Prefer short sections with clear headings.  
- Define acronyms on first use in each document (cross-link to [`docs/glossary.md`](docs/glossary.md)).  
- Use inclusive, precise language; avoid hype.  
- When describing architecture, separate **intent** (what must be true) from **implementation** (how a stack might satisfy it).  
- Do not embed secrets, tokens, or customer data—even in examples.

## Licensing

By contributing, you agree your contributions are licensed under the same terms as this repository (see [`LICENSE`](LICENSE)). If you contribute third-party material, it must be compatible and clearly attributed.

## Community interaction

All contributors must follow the [`CODE_OF_CONDUCT.md`](CODE_OF_CONDUCT.md).

## Maintainer contact

See [`community/maintainers.md`](community/maintainers.md).
