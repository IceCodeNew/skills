---
name: testing-discipline
description: How to write tests worth keeping. Use when writing or reviewing any test: an intent (end-to-end) test during development, a regression test for a bug fix, or unit tests at release. Covers which tests are necessary at each stage, where they go, and the red-green loop.
---

# Testing Discipline

The global prompt decides *when* each kind of test is written; this skill decides *how*, and which tests are worth writing at all.

## Which stage, which tests

- During development: intent tests only. For each requirement, a minimal set of black-box end-to-end tests asserting the user-visible outcome. No unit tests.
- Bug fixes during development: prove the bug and the fix with a repro or an intent-level regression test (behavior through the public interface). The unit regression test for the suite is deferred to release, like other unit tests.
- At release, when the feature is finalized: add the unit tests that pin behavior worth pinning, selected by the seam rules below — not blanket coverage.

## What a good test is

Tests verify behavior through public interfaces, not implementation details. Code can change entirely; tests shouldn't. A good test reads like a specification: "user can checkout with valid cart" tells you exactly what capability exists, and it survives refactors because it doesn't care about internal structure. One logical assertion per test. Test names describe WHAT, not HOW.

## Seams: where tests go

A seam is the public boundary you test at: the interface where behavior is observed without reaching inside. Tests live at seams, never against internals.

You can't test everything. Choose seams on the critical paths and the complex logic, not every edge case. Prefer, in order: an integration seam (proves two modules cooperate) over a high fan-in symbol over a leaf function. For each proposed seam, note in one line what it catches and what it misses. When fixing a bug, draw the contract from the outside: read the callers to learn the preconditions and argument shapes they actually rely on, and write the test against that observed contract.

## Expected values come from an independent source

Never compute the expected value the way the code computes it (`expect(add(a, b)).toBe(a + b)`, an expected total built with the same reduce). Such a test passes by construction and can never disagree with the code. Expected values come from the spec, a worked example, or a known-good literal.

## Rules of the loop

- Red before green. Write the failing test first and watch it fail, then write only enough code to pass it. Don't anticipate future tests or add speculative features.
- One slice at a time. One seam, one test, one minimal implementation per cycle. Work in vertical slices: each test is a tracer bullet that responds to what the last cycle taught you. Never write all the tests first and then all the implementation — bulk tests verify imagined behavior and commit to a test structure before the implementation is understood.
- Refactoring is not part of the loop. It belongs to the review stage, not the red-green cycle.
- Red-green must be run, not assumed. For a regression test, run it against the unfixed code in an isolated temporary checkout or fixture, then against the fix. A regression test that has only ever been observed passing pins nothing.
- Negative assertions need a paired positive case. "Output must not contain X" passes forever if X was never emitted under any code version; pair it with a case proving the assertion can fail at all. The same caution applies to any framework where a failing assertion mid-test may not fail the test — confirm with a minimal repro rather than reasoning about it.

## Coverage is a diagnostic, not a quota

Only after the rules above hold, look at branch coverage — as a question, not a target. The useful output is the list of what is uncovered and why, failure paths first. Never write a test solely to raise the number; a padded suite is worse than an honest gap.

Where any rule in this skill can be enforced mechanically, encode it as a lint or CI check instead of relying on discipline.

## Anti-patterns

- Implementation-coupled: mocks internal collaborators, tests private methods, asserts on call counts or order, or verifies through a side channel (querying the database instead of using the interface). The tell: the test breaks when you refactor but behavior hasn't changed.
- Tautological: the assertion recomputes the expected value the way the code does, or asserts a constant equal to itself.
- Hollow green: a test that passes without exercising the real path — skipped cases, early returns, assertions that are true on empty output.

## Mocking

Mock as little as possible, and only at system boundaries. Never mock your own classes, modules, or internal collaborators — anything you control.

At a boundary, prefer in order: the real dependency (a real Redis, a real PostgreSQL — a test instance, not a mock of one); then an injected fake, provided the fake's contract with the real system is itself verified; then a mock. For time logic use a fake timer — never sleep and hope. Casual mocking is the default failure mode: a mock of an internal collaborator tests the mock, not the system.

Design boundaries for this: pass external dependencies in rather than creating them internally (dependency injection), and prefer SDK-style interfaces — one specific function per external operation — over one generic fetcher with conditional logic. Each fake or mock then returns one specific shape, with no conditional logic in test setup.

## Intent tests, specifically

- Derive them from the requirement, never from the implementation. An intent test written from the agent's own reading of the code is circular: it locks the agent's misunderstanding in as the target.
- Keep them few: the main path of the requirement, plus at most the critical failure paths. Do not chase coverage with them, and do not grow them as the implementation grows.
- Change them only when the requirement changes. An intent test that fails after a pure refactor is testing the wrong level.
- Write them as acceptance scenarios before the code: name them for the user-visible capability, in Given / When / Then form where the framework allows, so a user or PM can read them. Never write the scenario after the code by reading the implementation back — that proves the code matches itself.
- Run them in tiers: on each change, run the tests the change and its dependencies actually touch; run the full set periodically and at milestones. A two-line change must not wait on the whole suite.
