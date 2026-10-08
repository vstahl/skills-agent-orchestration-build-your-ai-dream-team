# Implementation Plan: Mona's Project Pulse Dashboard

Plan produced by the Planner agent (run on `claude-opus-4.8` because Claude Opus 4.7 was unavailable in this environment).

## Summary

Build a static Project Pulse dashboard in `app/` plus a VS Code launch configuration. The dashboard reads project records from `app/project-data.json`, renders them as styled project cards with status badges and priority treatment, and runs through a **Run Project Pulse Dashboard** launch config that serves `app/` and opens `index.html` (never a directory listing).

- **Coder** owns structure, data, logic, and tooling: `app/index.html`, `app/project-data.json`, `.vscode/launch.json`.
- **Designer** owns presentation: `app/styles.css`.

The only shared contract is the CSS class hooks and the data field names.

## File assignments

| File | Owner | Responsibility |
|------|-------|----------------|
| `app/index.html` | Coder | Page markup, `.dashboard` container, JS that fetches data and renders a `.project-card` per project |
| `app/project-data.json` | Coder | Top-level `projects` array of 4-6 sample projects |
| `.vscode/launch.json` | Coder | Strict JSON launch config named `Run Project Pulse Dashboard`, `cwd` = `${workspaceFolder}/app`, opens `index.html` |
| `app/styles.css` | Designer | Responsive layout, card styling, status badges, priority treatment |

No file has two writers; other agents treat files they don't own as read-only.

## Shared contract (lock before implementation)

- **Data fields** (each project): `name`, `owner`, `status`, `recentActivity`, `priority`.
- **CSS hooks**: `.dashboard` (grid wrapper), `.project-card` (per project).
- **Status badge**: `.status-badge` plus modifiers such as `status--active`, `status--blocked`, `status--review`, `status--done`.
- **Priority**: `.priority` plus `priority--high`, `priority--medium`, `priority--low`.

## Responsibilities

### Designer
- Responsive `.dashboard` grid with a `@media` breakpoint so cards reflow on narrow screens.
- `.project-card` with `border-radius`, `box-shadow`, readable spacing, clear typography, and good contrast.
- Distinct status badge styles per modifier, with a neutral fallback for unknown values.
- Clear high/medium/low priority treatment that does not rely on color alone.
- Make the first view clearly look like a Project Pulse dashboard.

### Coder
- Create `app/project-data.json` as strict JSON matching the contract.
- Create `app/index.html`: link `styles.css`, add a Project Pulse heading, render cards from the JSON using the agreed hooks.
- Handle empty data, missing fields, and fetch failure gracefully.
- Create `.vscode/launch.json` (strict JSON, no comments, 2-space indent to match `tasks.json`) that serves `app/` and opens `index.html`.
- Validate the changes before reporting completion.

## Ordered steps

0. **Agree the shared contract** (Planner/Orchestrator). No files written.
1. **Project data** (Coder): `app/project-data.json`.
2. **Markup and render logic** (Coder): `app/index.html`.
3. **Styling** (Designer): `app/styles.css`.
4. **Launch configuration** (Coder): `.vscode/launch.json`.
5. **Integration and validation** (Orchestrator).

## Dependencies

```
Step 0 --+--> Step 1 --+
         +--> Step 2 --+--> Step 4 --> Step 5
         +--> Step 3 --+               ^
                       Steps 1, 3 -----+
```

- Step 0 gates all implementation.
- Steps 1, 2, and 3 are independent by file scope.
- Step 4 needs `app/index.html` (Step 2) to exist.
- Step 5 needs Steps 1-4.

## Parallel work decisions

**Parallel (no file overlap):**
- Coder track: `app/project-data.json` and `app/index.html`.
- Designer track: `app/styles.css`.

Ownership is disjoint, and the shared contract removes any need for one track to wait on the other's content.

**Sequential:**
- Step 0 before any implementation.
- `.vscode/launch.json` after `app/index.html`.
- Integration and validation after all implementation.

## Edge cases

- Empty `projects` array: show a friendly "no projects" state.
- Missing optional field (e.g. `recentActivity`): render a fallback, never `undefined`.
- Unknown status or priority: neutral fallback style.
- Fetch failure or wrong server root: visible error message.
- `file://` vs served: `fetch` needs http, so the launch config must serve `app/`.
- Directory listing: the launch config must open `index.html`.
- Accessibility: text labels and sufficient contrast, not color alone.
- Responsive: no horizontal scroll on narrow viewports.
- Strict JSON: no comments or trailing commas in either JSON file.

## Validation expectations

- `python3 -m json.tool app/project-data.json` and `python3 -m json.tool .vscode/launch.json` both parse.
- `project-data.json` has a top-level `projects` array; each entry has `name`, `owner`, `status`, `recentActivity`, `priority`.
- `index.html` contains `project-card` markup and loads `styles.css` and the JSON.
- `styles.css` contains `.dashboard`, `.project-card`, `border-radius`, `box-shadow`, plus badge and priority rules.
- `launch.json` has a config named `Run Project Pulse Dashboard`, `cwd` of `${workspaceFolder}/app`, and opens `index.html`.
- Manual run: the dashboard shows styled cards, badges, and priority, reflows when narrowed, and shows no directory listing.
- No agent stages, commits, or pushes; git stays with the learner.

## Open questions

1. Launch mechanism (default: a static HTTP server rooted at `app/` that opens `index.html`).
2. Sample data volume and theme (default: 4-6 Project Pulse themed entries).
3. Status vocabulary (default: Active, Blocked, In Review, Done).
4. Priority scale (default: High / Medium / Low).
5. Vanilla HTML/CSS/JS with no build step (assumed).
