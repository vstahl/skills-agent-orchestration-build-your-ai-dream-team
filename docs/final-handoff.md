# Project Pulse final handoff

Mona's Project Pulse dashboard was built with GitHub Copilot CLI in a Codespace, orchestrating a team of custom agents defined in `.github/agents/`.

## Agent team

| Agent | Role in this build |
|-------|--------------------|
| Orchestrator | Coordinated the work, assigned non-overlapping file scopes, and ran Coder and Designer in parallel. |
| Planner | Produced `docs/project-pulse-plan.md` with file ownership, dependencies, and parallel-work decisions. |
| Designer | Owned visual and accessibility decisions and wrote `app/styles.css`. |
| Coder | Implemented `app/index.html`, `app/project-data.json`, and `.vscode/launch.json`. |

## Deliverables

- `app/index.html`: page titled "Project Pulse"; links `styles.css`, fetches `project-data.json`, and renders one `project-card` per project showing status, recentActivity, and priority. Handles empty data, missing fields, unknown values, and fetch failure.
- `app/styles.css`: responsive `.dashboard` grid and `.project-card` styling with `border-radius`, `box-shadow`, status badges, priority indicators that don't rely on color alone, focus styles, and reduced-motion support.
- `app/project-data.json`: top-level `projects` key with five projects, each with `name`, `owner`, `status`, `recentActivity`, and `priority`.
- `.vscode/launch.json`: strict JSON launch file with the configuration "Run Project Pulse Dashboard".

## How to run

Use the launch name "Run Project Pulse Dashboard" from `.vscode/launch.json`. It runs `python3 -m http.server 5500` in `app/` and `serverReadyAction` opens `http://localhost:%s/index.html`, so the dashboard opens rather than a directory listing. In a Codespace, open port 5500 from the Ports tab.

## Validation results

- `app/project-data.json` and `.vscode/launch.json` parse as strict JSON (checked with `python3 -m json.tool`).
- `app/index.html`, `app/styles.css`, and `app/project-data.json` all return HTTP 200 from a server on port 5500.
- Required content confirmed: exact title, `.dashboard` and `.project-card` selectors, `border-radius`, `box-shadow`, a responsive `@media` rule, and all five data fields on every project.
- Review fix: removed a CSS `::before` that produced "Lead: Owner: ..." on cards.
- `scripts/validate-exercise.sh`: dashboard and launch checks pass. Two template-maintainer checks fail (launch file tracked in the template; README story) and are unrelated to the dashboard.
- Not done: visual check in a browser and automated accessibility or contrast testing.

## Notes for handoff

- The Planner, Designer, and Orchestrator models in `.github/agents/` (Claude Opus 4.7, Gemini 3.1 Pro) were unavailable in this environment. The Planner ran on `claude-opus-4.8` and the Designer on `gemini-3.8-flash`.
- Agents did not stage, commit, or push; git was controlled through Copilot CLI prompts.
- Open questions from the plan (status vocabulary, priority scale, sample data) were resolved with the plan's defaults.
