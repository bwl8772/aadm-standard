# Reuse before build — stdlib, native platform, installed dependencies

> Before writing custom code or adding a new dependency, exhaust what you already have. Most "small helpers" — date math, id generation, retries, debounce, deep-clone, format parsing — already exist in the language standard library, the runtime/platform, or a dependency the repo already declares. Reinventing them adds bugs, surface area, and maintenance for no benefit. Stack-agnostic.

## The reuse hierarchy

Prefer options higher in this list; drop down only with a written reason.

1. **Language standard library** — already shipped, versioned with the runtime, no new supply-chain surface.
2. **Native platform / runtime feature** — browser, OS, mobile SDK, or engine capability that exists without any library.
3. **An already-declared dependency** — something the manifest already pulls in that covers this need.
4. **Small, local custom code** — a focused helper you own, with tests, when nothing above fits.
5. **A new third-party dependency** — last resort, and a *separate* decision (see below).

> Good engineering is not maximal engineering. The cheapest code to maintain is the code you did not write.

---

## New dependencies are an architecture decision, not a feature detail

Adding a dependency is a supply-chain, maintenance, license, and build-weight commitment. Treat it as its own reviewable decision — not something smuggled into a feature diff.

- Confirm the stdlib / native platform / an existing dependency cannot do it first.
- Justify the new dependency explicitly: what it gives you that you cannot cheaply build or already have.
- Pin intentionally and read release notes; avoid unexplained dependency growth.
- Application-repo rule of thumb: **do not add dependencies that are not already declared in the manifest without separate architecture review.**

---

## "What's native" cheat-table (verify against your runtime version)

| Need | Reach for native / stdlib first | Common over-reach |
|------|----------------------------------|-------------------|
| **JS / TS** | `Intl` / `Temporal`, `structuredClone`, `crypto.randomUUID()`, `fetch`, modern Array/Object/Map methods, `padStart` | moment / date-fns, lodash/underscore, the `uuid` package, axios, left-pad |
| **Python** | `datetime` / `zoneinfo`, `uuid`, `pathlib`, `json` / `csv`, `dataclasses` / `enum`, `functools` | hand-rolled date parsing, `os.path` string surgery, custom config parsers |
| **Go** | `time`, `crypto/rand`, `encoding/json` + `encoding/csv`, `slices` / `maps`, `errors` wrapping | custom slice utils, bespoke retry loops without context |
| **Rust** | `std::time`, `std::collections`, iterators, `?` error propagation | crates for things iterators/std already do well |
| **Java / Kotlin** | `java.time`, `java.util.UUID`, `java.util.concurrent`, streams | Joda-Time on modern JDKs, hand-rolled thread pools |
| **Swift** | `Foundation` `Date` / `UUID` / `URLSession`, `Codable` | custom JSON coding, manual date formatting |
| **Game engines** | Engine services and built-in APIs before community modules | re-implementing engine-provided networking/state |

This table is a prompt, not gospel — availability depends on language and runtime **version**. Name the specific API you checked.

---

## Reinvention smells

Each is a *hint* to pause and check the hierarchy — never a verdict.

- **Reinvented date/time.** Custom date/time parsing, formatting, or math. DST, locales, and leap cases bite custom code.
- **Reinvented UUID.** Hand-rolled unique/random id generation. Prefer platform UUID/RNG.
- **Reinvented retry.** Bespoke retry/backoff loops. Miss jitter, caps, and idempotency (confirm AUTH-25/26 before retrying at all).
- **Reinvented debounce.** Custom debounce/throttle. Edge cases: leading/trailing, cancel, flush.
- **Reinvented deep clone.** Recursive copy helpers. Mishandle cycles, Maps/Sets, Dates, typed arrays.
- **Reinvented parser.** Hand-written or regex CSV/JSON/YAML/XML parsing. Breaks on quoting, escaping, encoding.
- **New dependency unjustified.** A new dependency proposed; confirm the hierarchy first and review it as a separate decision.

---

## Closing

When this document conflicts with local, documented team standards, **local standards win** — after you reconcile the gap explicitly. The goal is not "never add code or dependencies"; it is "do not reinvent what you already have without a reason you wrote down."
