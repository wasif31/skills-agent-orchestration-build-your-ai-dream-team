# Project Pulse Dashboard Implementation Plan

**Plan file:** `docs/project-pulse-plan.md`  
**Goal:** Build Mona’s lightweight static Project Pulse dashboard so contributors can see active projects, owners, status, recent activity, priority or risk, and a short summary. Provide a polished, accessible UI and a VS Code launch configuration that opens the dashboard rather than a directory listing.

This plan follows `.github/project-pulse-brief.md` and the requirements in `.github/steps/2-step.md` and `.github/steps/3-step.md`. The repository has no existing `app/` implementation or app-specific test suite, so the build should stay dependency-light and use the repository’s stated HTML, CSS, JSON, and Python preview approach.

## Ordered implementation steps

### 1. Agree on the interface and file boundaries

- **Orchestrator:** Use this plan to delegate work, make sure the specialists agree on the UI/data contract, and keep file ownership separate.
- **Designer:** Specify the information hierarchy, card layout, responsive behavior, accessibility requirements, and visual direction. Agree on the `.dashboard` and `.project-card` hooks before styling.
- **Coder:** Confirm how the page will load and render `project-data.json` and how the launch configuration will start and open the preview.
- Keep the data keys from the brief: `name`, `owner`, `status`, `recentActivity`, and `priority`. The brief also requests a short contributor-friendly summary; unless Mona specifies otherwise, add an optional `summary` field and display it.

**Dependency:** Complete this shared contract before implementation so the HTML and CSS use matching structure and selectors.

### 2. Build the dashboard files in parallel

**Designer — owns `app/styles.css` only**

- Style the agreed dashboard and project-card structure with clear hierarchy, readable spacing, and responsive layout.
- Include `.dashboard` and `.project-card` selectors, along with visible status/priority treatment, `border-radius`, and `box-shadow`.
- Ensure status and priority remain understandable without color alone; check contrast, keyboard focus visibility where applicable, and narrow-screen layout.
- Report design decisions and any markup hooks the Coder must preserve.

**Coder — owns `app/index.html`, `app/project-data.json`, and `.vscode/launch.json` only**

- **`app/project-data.json`:** Create valid JSON with a top-level `projects` array. Include multiple sample projects, each with the five required fields; include `summary` if following the brief’s summary requirement. Use plausible sample data, and do not imply it is a verified source of Mona’s actual project status.
- **`app/index.html`:** Set the exact page title to **Project Pulse**; reference `styles.css` and `project-data.json`; provide semantic page structure and render one visible `.project-card` per project. Show name, owner, status, `recentActivity`, priority, and summary when present. Since no separate JavaScript file is in scope, use a small inline script or equivalent within the page to load and render the JSON.
- **`.vscode/launch.json`:** Create strict JSON with no comments and a configuration named **Run Project Pulse Dashboard**. Set `cwd` to `${workspaceFolder}/app`, use `python3 -m http.server 5500`, and configure `serverReadyAction` to open `http://localhost:%s/index.html`. Its readiness pattern must match the server’s actual output so the browser opens the page, not the directory root.

**Dependencies:** The Coder and Designer can work in parallel after Step 1 because the data fields and CSS hooks are agreed and the file scopes do not overlap. The page’s rendering logic must follow the agreed JSON contract. The launch file can be authored in parallel with the page, but its runtime check depends on all app files being present.

### 3. Integrate and validate

- **Orchestrator:** Review that the CSS selectors match the HTML, that the page loads the agreed data file, and that each visible card presents the required fields. Resolve integration issues by assigning fixes to the owner of the affected file.
- **Designer:** Address styling or accessibility issues in `app/styles.css` only.
- **Coder:** Address markup, data, rendering, or launch issues in the Coder-owned files only.
- Do not have both specialists edit the same file. Re-run the relevant checks after any fix.

**Dependency:** This step must follow both implementation tasks.

## Responsibilities and file ownership

| Agent | Responsibilities | Files this agent may create or modify |
|---|---|---|
| **Planner** | Research repository context, identify phases, dependencies, risks, and validation; produce this plan. | `docs/project-pulse-plan.md` only |
| **Orchestrator** | Delegate tasks, enforce file boundaries, coordinate integration, and report validation outcomes. Does not implement app files. | No app-file edits; coordinate changes through the assigned specialist |
| **Designer** | Define visual hierarchy, accessibility, responsive behavior, and visual polish; implement the agreed styling. | `app/styles.css` only |
| **Coder** | Implement semantic markup, data loading/rendering, sample data, and runnable preview configuration. | `app/index.html`, `app/project-data.json`, `.vscode/launch.json` only |

## Dependencies and work ordering

- **Must happen first:** Agree on the markup hooks, data schema, and summary-field treatment.
- **Can run in parallel after that:** Designer’s `app/styles.css` work and Coder’s three assigned files. There is no shared file ownership.
- **Must happen after implementation:** Integrated review and runtime validation. The launch configuration can be written earlier, but verifying it requires the completed app.
- **Must remain sequential during fixes:** When an integration issue requires a file change, assign that file to its owner, wait for the fix, then repeat validation. Do not assign overlapping edits to Designer and Coder.

## Edge cases and risks

- A `fetch()` request for `project-data.json` may fail when the HTML is opened directly using `file://`; use the configured local HTTP server for preview.
- Handle an empty or missing `projects` array with a clear empty state rather than a blank dashboard.
- Handle a failed data request or invalid JSON with a readable error message; do not silently show an empty dashboard.
- Avoid assuming every record is complete or that status/priority values are limited to one fixed set. Provide a sensible fallback for missing optional `summary` and unexpected values.
- Insert JSON values as text rather than interpreting them as HTML.
- Check that the launch readiness pattern matches the Python server output in the Codespace. Port `5500` may already be occupied.
- `serverReadyAction` and launch properties are debugger-specific. Confirm the selected launch type is supported in the Codespace; the repository’s declared VS Code extensions do not explicitly include a Python debugger extension.
- The required `localhost` URL should be tested in the Codespace because remote browser and port-forwarding behavior can differ from a local VS Code session.
- Sample project details are assumptions until Mona supplies real project data. The brief does not define allowed status/priority labels or whether a separate summary field is mandatory.

## Validation expectations

### Static checks

1. Confirm all four required files exist:
   - `app/index.html`
   - `app/styles.css`
   - `app/project-data.json`
   - `.vscode/launch.json`
2. Run `python3 -m json.tool app/project-data.json` and `python3 -m json.tool .vscode/launch.json`; both must parse successfully.
3. Review `app/index.html` for the exact title **Project Pulse**, references to `styles.css` and `project-data.json`, the `project-card` class, and visible rendering of `status`, `recentActivity`, and `priority`.
4. Review the JSON to confirm a top-level `projects` array and all five required fields in every project.
5. Review the CSS for `.dashboard`, `.project-card`, `border-radius`, and `box-shadow`, plus responsive rules and readable visual treatment.

### Runtime and UX checks

1. In VS Code Run and Debug, select **Run Project Pulse Dashboard** and start it.
2. Confirm the server starts from `app/` and the browser opens `http://localhost:<port>/index.html`, showing the dashboard rather than a directory listing.
3. Confirm the JSON request succeeds and the rendered card count matches the project count; verify each card displays name, owner, status, recent activity, and priority.
4. Check the browser console and network panel for failed requests or rendering errors.
5. Check empty/error handling, keyboard navigation, status/priority readability without color alone, and layout at narrow and desktop widths.
6. Stop the preview server after testing.

The repository’s `scripts/validate-exercise.sh` checks exercise-template constraints, including the absence of learner output files; it is not a substitute for these app-specific checks and may report failures once learner files are created.

## Documentation references

- Repository requirements: `.github/project-pulse-brief.md`, `.github/steps/2-step.md`, and `.github/steps/3-step.md`
- [VS Code debugging configurations](https://code.visualstudio.com/docs/debugtest/debugging-configuration): workspace `launch.json`, debugger-specific properties, and `serverReadyAction`
- [Python `http.server`](https://docs.python.org/3/library/http.server.html): local static preview server; intended here for development preview, not production hosting

## Open questions

1. What are Mona’s actual project names, owners, statuses, activity updates, and priorities? Until supplied, use clearly illustrative sample data.
2. Should the contributor-friendly summary be a separate `summary` JSON field, or is `recentActivity` intended to serve that purpose?
3. Which status and priority/risk labels should be supported and displayed?
4. Which debugger type is available in the target Codespace for launching the Python command with `serverReadyAction`? Confirm before finalizing the configuration.
5. Does the Codespace open the requested `localhost` URL directly, or does it require a forwarded-port URL? The exercise brief specifies the former, so test it and raise any environment mismatch rather than silently changing the requirement.
