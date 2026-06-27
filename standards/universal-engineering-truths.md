# Universal engineering truths

> Twenty stack-agnostic truths that hold across languages, frameworks, and team sizes — for anyone shipping software, including mixed human–agent teams. Complement [`universal-coding-principles.md`](universal-coding-principles.md), [`engineering-doctrine-top-10.md`](engineering-doctrine-top-10.md), and [`ai-coder-hard-stops.md`](ai-coder-hard-stops.md).

These are not rules of the language or framework. They are rules of the work itself.

---

## Orientation

1. **The implementation context is authoritative.** Models and assistants infer from text; your repository, runtime, and automated checks ground what is true. Treat generated or suggested changes as drafts until something in your project verifies them.

2. **Clear intent beats volume.** Before large edits: user or system outcome, definition of done, explicit non-goals. Ambiguous goals produce confident, wrong changes.

3. **Prefer small, verifiable steps.** The smallest change that could satisfy the next checkpoint, then measure (tests, build, manual check). Large single-step edits obscure failure attribution.

4. **Facts before mutation.** When behavior diverges from expectations: capture what was run, exact errors, and last known good. Paraphrased symptoms waste cycles.

5. **Understand, then change.** Widening scope across many files to silence an error without a rooted cause duplicates logic and hides defects. Classify defect-shaped work before broad refactors.

6. **Compilers, linters, and tests are precise signals.** When the project provides them, run them. Tool output is usually actionable; debating narrative instead of reading output compounds delay.

7. **One source of truth for consequential rules.** If the same rule would be edited in two places (multiple UIs, duplicated modules), expect drift. Important behavior should live once behind a clear boundary; multi-client truth and authority typically belong server-side.

8. **Presentation vs authority.** Interfaces optimize clarity and flow. Permissions, money, commitments, and final authoritative outcomes belong in enforced backend (or equivalent) paths — not only in the client.

9. **Secrets are not ambient text.** Credentials belong in controlled configuration (environment, secret stores), not in chat logs, tickets, or committed files. Assume leakage if mishandled; rotate when exposed.

10. **Naming and structure are load-bearing.** Consistent names and boundaries determine whether work is discoverable and reviewable. Ad hoc sprawl repeats the same integration failures.

11. **Version control is a safety contract.** Small commits, meaningful messages, branches for exploration. You should be able to return to a known good state without heroic recovery.

12. **Ownership of merged change stays human.** Review suggested edits before merge. Integration is a deliberate act; automation does not remove accountability.

13. **Reproducibility beyond one machine.** "Works locally" is an intermediate step. Same commands and configuration should behave in CI or a clean environment when that is your bar for integration.

14. **Failures should instruct.** Errors and edge states should be visible, attributable, and actionable for the next operator — including your future self.

15. **Performance is measured.** "Faster" requires *what* is slow (operation, latency, resource). Optimize one bottleneck with evidence, not speculative rewrites.

16. **Done includes usability and access.** Task completion includes whether intended users can finish the job (flows, assistive use, clarity). Fragile UX is incomplete delivery.

17. **Dependencies are your supply chain.** Third-party packages change and fail. Pin intentionally, read release notes, avoid unexplained dependency growth. See [`reuse-before-build.md`](reuse-before-build.md).

18. **Narrow the problem when stuck.** One command, one boundary, one hypothesis per narrowing step. Global "fix everything" attempts diffuse attention and quality.

19. **Match the lane to the question.** Planned delivery, broken-vs-expected, and prove-alignment assurance are different modes — different urgency, evidence, and tools. Mixing them wastes effort. See [`three-lanes.md`](three-lanes.md).

20. **Repeatable explanation beats repeated chat.** If the same explanation was needed twice, it likely belongs in documentation at the seam (README, runbook, ADR). That reduces repeated defects and repeated clarification.

---

## Through-line

**Truth lives in the repository and runtime; proposals require verification; scope stays proportionate; authority and serious rules stay consolidated; evidence precedes large change.**

---

## Precedence

Local team standards and regulated control frameworks **override** this list when they conflict — after the conflict is named explicitly. This file is a compass, not a substitute for project proof.
