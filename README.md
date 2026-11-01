# kanbot-example-web

Example skills and agents for a web project, wired into [kanbot](https://github.com/mikaelbr/kanbot) through a `capabilities` map.

kanbot runs the process: board, plans, review rounds, PRs. Your repo decides how code gets built. This repo shows one way to do that for a web app.

## What's inside

Skills in `.agents/skills/` (Claude Code and Codex both read them):

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

Agents in `.kanbot/agents/`:

- `ui-verifier`: runs the app and screenshots the states you name.
- `test-writer`: writes failing tests from acceptance criteria.

`.kanbot/config.yml` maps kanbot's capabilities to them:

| Capability | Uses | When |
|---|---|---|
| `verify` | `verify-ui` | Changes to UI users see |
| `review` | `code-review` | Always |
| `accessibility` | `accessibility` | Changes to UI users see |
| `ux-review` | `ux-review` | Changes to UI users see |
| `preview` | (commented example) | Any PR |

## Use with kanbot

In a repo where you ran `kanbot init`:

1. Copy `.agents/skills/*` into your `.agents/skills/`.
2. Copy `.kanbot/agents/ui-verifier.md` and `test-writer.md` into your `.kanbot/agents/`.
3. Merge the `capabilities` block from `.kanbot/config.yml` into yours. Keep your own `repo`.
4. Run `kanbot doctor`.

Edit or drop what doesn't fit your stack. Unmapped skills still work: agents pick them by their description.

## Use without kanbot

The skills don't need kanbot. Copy any of them into your repo's `.agents/skills/`, then ask for them by name or let the agent pick them by description, e.g. "verify the settings page with verify-ui".

They read facts from your repo (`AGENTS.md`, its scripts and conventions), so keep those up to date.

## License

`frontend-design` is Apache-2.0; see its `LICENSE.txt`.
