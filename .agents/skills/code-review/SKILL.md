---
name: code-review
description: Checklist for reviewing a code change for correctness, tests, security, data safety and maintainability. Use when reviewing a branch or diff, or when self-reviewing before you finish.
---

# Code review

Find the problems that would hurt users or the team after merge. A short list of real issues beats a long list of preferences.

## Read before judging

- `git log <base>..HEAD` and `git diff <base>...HEAD --stat` for the shape.
- Read the task's acceptance criteria and steps. The diff should match them.
- For each non-trivial hunk, open the whole function and its callers. Most bugs sit at the edges of the diff.
- Run tests, lint and typecheck. Don't assume CI will.

## Checklist (in priority order)

**Correctness**
- Does it do what the task says for every input: empty, null, zero, very large, duplicate, unicode?
- Off-by-one, wrong comparison, inverted condition, missing `await`, unhandled promise, races.
- State: stale state, cache not invalidated, partial writes without a transaction.
- Changed signatures or behaviour: are all callers updated?

**Tests**
- Does each criterion have a test that fails if the feature is removed?
- Do tests assert outcomes, not just that mocks were called?
- Unhappy paths covered? No skipped, weakened or deleted tests?

**Security**
- Input validated at trust boundaries; output encoded (no raw HTML from user input).
- Parameterised queries; no SQL, shell commands or paths built from user input.
- Authorisation on every new endpoint, action and query (can user A read user B's data?).
- No secrets, tokens or personal data in code, logs, fixtures, screenshots or errors.
- New dependencies: maintained, reasonably sized, licence compatible.

**Data and operations**
- Migrations safe on existing data and reversible, or the irreversibility is stated.
- APIs, events and stored formats stay backward compatible.
- Errors surfaced or logged with context, not swallowed.
- Performance traps: N+1 queries, unbounded lists, work inside render loops.

**Maintainability**
- Follows `AGENTS.md`, the project's conventions and neighbouring code.
- Reuses existing utilities instead of duplicating them.
- Clear names; no dead code, commented-out code or debug output.
- Complexity fits the problem; no speculative abstractions.

## Reporting

Use the caller's format and severity scale if given. Otherwise, per finding: location, what is wrong, a failure scenario, and a suggested fix.

## Avoid

- Style a linter or formatter covers.
- Asking for scope beyond the task. Suggest a follow-up instead.
- Rewriting a working approach to your taste without a concrete defect.
