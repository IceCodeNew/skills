Where a skill's instructions conflict with these rules, these rules win.

## Comments
Default: no new inline comments (docstrings count as comments). Add one only when a future editor needs a verified fact that is hard to infer from the code, and only for one of these:
- a contract or invariant a refactor or cleanup must not break, including external constraints, required ordering, and non-obvious traps;
- a pointer out of the code: a sync tag for code that must change together, or a link to the issue, spec, or ADR behind a decision;
- a durable why that would otherwise be lost, including a rejected alternative, in one line.
Never restate what the code does, describe a plan instead of the result, or invent a rationale. Put conventions in AGENTS.md; put anything enforceable in types, tests, or lint, not in a comment.
Comments carry the same weight as code: write them when the fact is created, and update or delete them in the same change as the code they describe.

## Commits
Make atomic commits: one intent each, structural and behavioral changes separate, each commit buildable and revertible on its own. The message states what changed and why, with the background a future reader cannot recover from the diff.

## Tests
Do not write unit tests during development; add them only when the feature is finalized for release. Running existing tests is always allowed.
Intent tests are different: for each requirement, write a minimal set of black-box end-to-end tests that assert the user-visible outcome, derived from the requirement and never from the implementation, and keep them green throughout development. Change them only when the requirement changes.

## Deliverables
Write every deliverable as a self-contained final-state artifact. Fold feedback into the final wording; do not mention drafts, versions, review rounds, or superseded decisions unless a changelog or decision record is explicitly requested.
