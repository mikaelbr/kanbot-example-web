# kanbot-example-web

A web project set up with [kanbot](https://github.com/mikaelbr/kanbot), where the repo's own skills and agents do the work on code.

kanbot runs the process: board, plans, review rounds, PRs. The repo decides how code gets built, through the `capabilities` map in `.kanbot/config.yml`.

## Run it

Needs a kanbot release with `capabilities` (newer than 0.3.0), `gh` with the `project` scope, and `claude` or `codex`. See kanbot's [getting started](https://github.com/mikaelbr/kanbot/blob/main/docs/01-getting-started.md).

1. Create your own copy (**Use this template** on GitHub, or fork) and clone it.
2. Set `repo:` in `.kanbot/config.yml` to your `owner/name`.
3. Create the board, push, and start:

```sh
kanbot board create
git commit -am "chore: point kanbot at my repo" && git push
kanbot doctor
kanbot run
```

4. Add an issue that changes some UI and move it to **Planning**.

What happens:

- **plan**: kanbot's `planner` writes the plan. You move it to **Ready**.
- **implement**: proves the change with `verify-ui` (mapped as `verify`). Picks `tdd`, `frontend-design` and the rest when they fit.
- **review pass 1**: `code-review` (mapped as `review`).
- **review pass 2**: checks acceptance criteria; may run `verify-ui` and `ux-review` itself.
- The PR body's **Validation** line says what was used and what it showed.

## What's ours, what's kanbot's

`kanbot init` added the rest (planner, reviewers, flow skills, generated `.claude/` and `.codex/`). These are the repo's own:

| Skill | Use it for |
|---|---|
| `frontend-design` | Visual direction for new or reshaped UI. Apache-2.0, from [anthropics/skills](https://github.com/anthropics/skills). |
| `accessibility` | WCAG 2.2 AA checklist for web UI. |
| `verify-ui` | Screenshot UI states at 390px and 1280px with Playwright. |
| `ux-review` | Check loading, empty, error and success states, plus a11y. |
| `code-review` | Review checklist: correctness, tests, security, data, maintainability. |
| `tdd` | Red, green, refactor from acceptance criteria. |
| `backend-practices` | Validation, migrations, compatibility, authorisation. |
| `docs-writing` | Docs that match the code and help the reader finish a task. |
| `safe-refactor` | Refactors, upgrades, CI and infra without behaviour change. |

Agents in `.kanbot/agents/`: `ui-verifier` (screenshots the states you name) and `test-writer` (failing tests from acceptance criteria).

The map in `.kanbot/config.yml`:

| Capability | Uses | When |
|---|---|---|
| `verify` | `verify-ui` | Changes to UI users see |
| `review` | `code-review` | Always |
| `accessibility` | `accessibility` | Changes to UI users see |
| `ux-review` | `ux-review` | Changes to UI users see |
| `preview` | (commented example) | Any PR |

## Reuse in another repo

Copy the skills above into its `.agents/skills/` and the two agents into `.kanbot/agents/`, add the `capabilities` block to its config, and run `kanbot sync`. The skills don't need kanbot: any agent that reads `.agents/skills/` can use them.

## License

`frontend-design` is Apache-2.0; see its `LICENSE.txt`.
