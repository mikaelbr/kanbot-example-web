---
name: tdd
description: Test-driven development loop (red, green, refactor) driven by acceptance criteria. Use when implementing a planned change, fixing a bug or review finding, or refactoring; covers which tests to write, running the narrowest test command first, and never weakening tests to make them pass.
---

# TDD

Tests prove that each acceptance criterion holds. Write them first so they test the behaviour you were asked for, not the code you happened to write.

## Loop

1. **Pick one criterion** (or one finding, for fixes). One at a time.
2. **Red.** Write a test through the public interface (rendered UI, HTTP endpoint, CLI, exported function). Run the narrowest command. Confirm it fails **on the assertion**, for the reason you expect. An import error, typo or missing fixture is not red yet; fix the test.
3. **Green.** Write the simplest code that passes. Run the narrow command again.
4. **Refactor.** Remove duplication and clarify names while green. Stay in the code you just touched.
5. **Commit** test and code together (`feat: …`, `fix: …`), then take the next criterion.
6. After all criteria: run the full suite, lint and typecheck once.

## Running tests

- Narrowest first: one file or test name (`vitest run file -t name`, `pytest path::test_name`, `go test ./pkg -run TestName`). Use the commands in `AGENTS.md` or the project's conventions.
- Full suite after the narrow tests pass, and before you finish.
- Slow test because of setup? Still run it; don't swap it for a weaker unit test.
- Read failure output fully. The first error is usually the real one.

## What to test

- Each acceptance criterion: at least one test, named after the behaviour.
- Unhappy paths the task names: invalid input, empty data, failing dependencies, permission denied.
- Bugs: a regression test that fails before the fix.
- Refactors: characterization tests that pass before and after.

Mock only boundaries you don't own (network, clock, randomness, third-party services). Mocking your own modules hides broken integration.

## When the test is hard to write

- Hard setup usually means tight coupling. Add a seam (pass the dependency in) instead of reaching into internals.
- Purely visual criteria (layout, colour) are checked by screenshot (`verify-ui`), not markup snapshots.
- A criterion that can't be tested automatically: say which and why in your report.

## Never

- Weaken an assertion, skip, delete a test, or loosen a type or lint rule to get green.
- Change an expected value to match the output without confirming the test was wrong. If it was, say so in the commit message.
- Leave `only`/`focus` markers or debug output in tests.
- Claim tests pass without running them. Report the command and result.

## If the suite is already red

Run it before you change anything. Note the existing failures, add none, and report them. Don't fix unrelated failures unless asked.
