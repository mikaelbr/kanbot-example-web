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

Needs a kanbot release with `capabilities` (newer than 0.3.0). See kanbot's [getting started](https://github.com/mikaelbr/kanbot/blob/main/docs/01-getting-started.md) for requirements (`gh` with the `project` scope, `claude` or `codex`).

### 1. Copy the files

From your web app's root:

```sh
git clone --depth 1 https://github.com/mikaelbr/kanbot-example-web /tmp/kanbot-example-web
mkdir -p .agents/skills .kanbot/agents
cp -R /tmp/kanbot-example-web/.agents/skills/. .agents/skills/
cp /tmp/kanbot-example-web/.kanbot/agents/*.md .kanbot/agents/
```

### 2. Set up kanbot

```sh
kanbot init --provider claude   # or codex
```

`init` adds kanbot's own agents and skills next to yours, writes `.kanbot/config.yml`, and generates `.claude/` and `.codex/` from `.kanbot/agents`.

Already have kanbot? Skip `init` and run `kanbot sync` to generate the copied agents.

### 3. Map the capabilities

Add the `capabilities` block from this repo's `.kanbot/config.yml` to yours. Keep your own `repo`.

```yaml
capabilities:
  verify:
    use: verify-ui
    when: Changes to UI users see
  review:
    use: code-review
  accessibility:
    use: accessibility
    when: Changes to UI users see
  ux-review:
    use: ux-review
    when: Changes to UI users see
```

Edit or drop what doesn't fit your stack. Unmapped skills still work: agents pick them by their description.

### 4. Tell agents about your app

The skills read facts from your repo. Fill in `.agents/skills/project-conventions/SKILL.md` and `AGENTS.md` with your dev command, port, and test, lint and typecheck commands. `verify-ui` can't start the app without them.

### 5. Board, push, check

```sh
kanbot board create
git add -A && git commit -m "chore: add kanbot" && git push
kanbot doctor
```

`doctor` should list each capability with a check:

```
✔ capability verify → verify-ui
✔ capability review → code-review
✔ capability accessibility → accessibility
✔ capability ux-review → ux-review
! capability suggestion — no `preview`: map a skill that gives a human something to try …
```

The `preview` warning is fine until you add a preview skill.

### 6. Run

```sh
kanbot run --once --dry-run   # what it would do
kanbot run                    # start the daemon
```

Add an issue that changes some UI and move it to **Ready**. What happens:

- **implement** proves the change with `verify-ui` (mapped). It picks unmapped skills like `tdd` and `frontend-design` when they fit.
- **review pass 1** uses `code-review`.
- **review pass 2** checks the acceptance criteria and may run `verify-ui` and `ux-review` itself.
- The PR body's **Validation** line says what was used and what it showed.

## Use without kanbot

The skills don't need kanbot. Copy any of them into your repo's `.agents/skills/`, then ask for them by name or let the agent pick them by description, e.g. "verify the settings page with verify-ui".

They read facts from your repo (`AGENTS.md`, its scripts and conventions), so keep those up to date.

## License

`frontend-design` is Apache-2.0; see its `LICENSE.txt`.
