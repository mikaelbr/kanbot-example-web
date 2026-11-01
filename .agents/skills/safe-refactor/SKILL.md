---
name: safe-refactor
description: Change code, tooling, dependencies, CI or infrastructure without changing behaviour - baseline first, characterization tests, small mechanical commits, dry runs. Use for refactors, renames, moves, dependency upgrades, build and CI changes.
---

# Safe refactor

The default contract: behaviour stays the same; the code or pipeline gets better as the task describes.

## Process

1. **Map.** Find every call site, config file and script affected. For renames and moves, include strings, docs and CI files.
2. **Baseline.** Run the full test suite, lint, typecheck and build before you change anything. Record the result. If it is already red, note which failures exist so you don't hide new ones.
3. **Cover.** Make sure the behaviour you keep is tested. Add characterization tests that pass on the current code where coverage is missing (`tdd`).
4. **Change in small steps.** Commit each step on its own (`refactor: …`, `build: …`, `ci: …`, `chore(deps): …`). Keep mechanical moves and behaviour edits in separate commits.
5. **CI and infra.** Validate what you can't run: YAML or schema lint, `--dry-run`, `plan`, `act`. Report what stays unverified.
6. **Dependencies.** Read the changelog for breaking changes between versions. Update the lockfile with the project's package manager only.
7. **Compare.** Re-run the baseline checks. They must be at least as green.

## Never

- Change user-visible behaviour unless the task asks.
- Weaken, skip or delete a test, lint rule or CI check to get green.
- Commit secrets. Refer to them by name (`secrets.DEPLOY_TOKEN`).

## Report

Baseline and after results. Before/after numbers when the goal is measurable (build time, bundle size, warnings). Rollout and rollback steps for infra. Unverified paths.
