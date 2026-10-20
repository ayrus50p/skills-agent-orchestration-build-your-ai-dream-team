# Project Pulse implementation plan

## Summary

Build Mona's Project Pulse dashboard as a lightweight static app using native HTML, CSS, and JavaScript, with no framework or runtime dependencies. The page will load project records from a JSON file and present them in accessible, responsive project cards. The plan separates visual styling from page/data/launch implementation, with a shared contract agreed before those disjoint implementation tasks begin.

The scope is the dashboard, its illustrative local data, and its launch configuration. Do not add filters, a backend, or other unrequested functionality.

## Repository findings

- `.github/project-pulse-brief.md` specifies `app/index.html`, `app/styles.css`, `app/project-data.json`, and `.vscode/launch.json`. The data must have a top-level `projects` array, and each project must include `name`, `owner`, `status`, `recentActivity`, and `priority`.
- `.github/steps/3-step.md` and `.github/workflows/3-step.yml` expect the title **Project Pulse**, references to the CSS and JSON, visible `.project-card` elements, `.dashboard` and `.project-card` CSS selectors, responsive styling, strict JSON, and a launch configuration named **Run Project Pulse Dashboard**.
- The Designer's expectations are polished, responsive, accessible styling and stable hooks for the page.
- The Coder's expectations include strict JSON, a launch working directory of `${workspaceFolder}/app`, and opening `index.html`.
- The Orchestrator requires explicit, non-overlapping implementation scopes. `.vscode/tasks.json` is for the Copilot CLI terminal and must not be changed.
- `scripts/validate-exercise.sh` checks exercise/template conventions; it does not verify browser rendering.
- There is no framework, package manifest, app code, or app-specific test suite. Use native HTML/CSS/JavaScript and no dependencies.
- Codespaces does not explicitly configure Python or browser-debugger extensions. Confirm launch support rather than assuming it.

## Responsibilities and file ownership

| Role | Responsibility | Exclusive file assignment |
| --- | --- | --- |
| Planner | Research and maintain this plan only. | `docs/project-pulse-plan.md` |
| Designer | Define the visual system and implement polished, accessible, responsive styling. Use the agreed markup hooks and ensure the required selectors exist. | `app/styles.css` |
| Coder | Implement the semantic page, load and render the JSON, handle loading/error/empty states, and provide the launch configuration. | `app/index.html`, `app/project-data.json`, `.vscode/launch.json` |
| Orchestrator | Coordinate phases, settle the shared contract, manage integration, and ensure validation follows integration. Do not implement files. | No implementation files |

No implementation file is assigned to more than one role. Do not modify `.vscode/tasks.json`.

## Ordered phases

### 1. Coordinate and agree on the shared contract

Before implementation begins, the Orchestrator aligns the Designer and Coder on the page structure, CSS hooks, and data shape. Record or communicate the contract so both parallel tasks can proceed independently.

- The document title and visible page heading identify the dashboard as **Project Pulse**.
- The page links to `styles.css` and loads `project-data.json` using a relative URL that works when served from `app/`.
- The dashboard uses a `.dashboard` container and each rendered project uses a `.project-card` element. Agree on any additional class names or semantic elements before styling starts.
- JSON is a strict object with a top-level `projects` array. Every project contains `name`, `owner`, `status`, `recentActivity`, and `priority`. Include a `summary` field to provide a contributor-friendly project summary.
- `summary` supplements, rather than replaces, any of the five required fields. Sample projects are illustrative only. Real project records and the allowed `status` and `priority` values/enums have not been specified; do not present illustrative values as authoritative.
- Agree on how the page treats absent or malformed values as part of its error/empty-state design, without expanding scope into filters or backend behavior.

### 2. Implement in parallel after the contract is agreed

These assignments are disjoint and may proceed concurrently:

- **Designer:** implement `app/styles.css`, including `.dashboard` and `.project-card`, responsive layouts, legible typography and contrast, visible focus treatment, and styling that supports the agreed loading, error, and empty states.
- **Coder:** implement `app/index.html`, `app/project-data.json`, and `.vscode/launch.json`. Use semantic markup and accessible status/error feedback; fetch and render project data; handle loading, fetch/parse errors, and an empty projects list; include illustrative records with all required fields and `summary`; and configure the specified launch behavior.

The Coder owns all three assigned files, and the Designer owns only the stylesheet. Neither should edit the other's assigned files.

### 3. Integrate and validate

After both implementation tasks are complete, the Orchestrator checks that the page markup, class hooks, and loading/error/empty states match the shared contract and that all assigned files are present. Run validation only after integration. Fix issues within the owning role's assigned files; if the contract needs a change, coordinate it before either role edits outside its scope.

## Launch behavior

Configure **Run Project Pulse Dashboard** to run:

- Command: `python3 -m http.server 5500`
- Working directory: `${workspaceFolder}/app`
- Browser URL: `http://localhost:%s/index.html`, opened through `serverReadyAction`

The readiness pattern must match the actual startup output produced by the chosen Python HTTP server invocation so the port captured by the pattern is passed to the URL. Confirm the output/pattern pairing and that the launch mechanism is supported in the target Codespaces environment. Do not assume the presence of Python or browser-debugger extensions; resolve or document any prerequisite before relying on the launch configuration. Serving from `app/` and opening `index.html` avoids file-URL fetch restrictions and accidental directory listings.

## Dependencies and parallel work decisions

- The shared markup/CSS/data contract is a prerequisite for both implementation assignments.
- After that contract is agreed, Designer and Coder can work in parallel because their file assignments do not overlap.
- Integration and validation depend on both parallel assignments being complete.
- The Planner's work is limited to this document. The Orchestrator coordinates and integrates but has no implementation-file assignment.
- Keep this a static dashboard. Do not add filtering, persistence, API/backend integration, package tooling, or other functionality beyond the agreed requirements.

## Risks and edge cases

- **`file://` fetch failure:** browsers commonly restrict fetching local JSON from a file URL. Launch over the configured local HTTP server.
- **Wrong working directory or URL:** serving from the repository root or opening the server root may show a directory listing instead of the dashboard. Use the `app/` working directory and open `/index.html`.
- **Startup-output differences:** Python/server versions or environment wrappers may emit different readiness output. An unmatched `serverReadyAction` pattern will prevent automatic opening.
- **Port conflicts:** port 5500 may already be in use. If it is, coordinate a port change and keep the launch command and URL/readiness pattern consistent.
- **Malformed or empty data:** JSON parse/fetch failures and an empty `projects` array should produce understandable, accessible UI states rather than a blank page or uncaught failure.
- **Unspecified data conventions:** real projects and status/priority enums are unknown. Treat sample values as illustrative and avoid implying validation against an authoritative enum.
- **Launch-support uncertainty:** Python and browser-debugger launch support are not explicitly configured in Codespaces. Verify prerequisites and behavior in the target environment.
- **Validation coverage:** exercise workflow checks do not prove that the app renders in a browser or that launch/debugger integration works.

## Validation expectations

Run these checks after integration:

1. Validate project data as strict JSON: `python3 -m json.tool app/project-data.json`
2. Validate launch configuration as strict JSON: `python3 -m json.tool .vscode/launch.json`
3. Run repository exercise/template checks: `bash scripts/validate-exercise.sh`
4. Manually launch **Run Project Pulse Dashboard**. Confirm the server starts from `app/`, the readiness action opens `http://localhost:<port>/index.html`, and the dashboard appears rather than a directory listing.
5. Verify the rendered page title, visible project cards, and display of `name`, `owner`, `status`, `recentActivity`, `priority`, and `summary` for each illustrative record.
6. Check responsive behavior at narrow and wide viewport sizes, keyboard navigation and visible focus, semantic/accessibility cues, and readable contrast.
7. Exercise loading, fetch/parse error, and empty-project states. Confirm each is understandable and accessible.
8. Confirm the launch readiness pattern works with actual startup output in the target environment. The exercise workflow and JSON checks alone do not prove browser rendering or launch support.

## Open questions

- What are the authoritative project records and permitted `status` and `priority` values? Until specified, use clearly illustrative sample data.
- Which Python/browser-debugger extensions or launch capabilities are available in the target Codespaces environment?
- Does the actual `python3 -m http.server 5500` startup output match the proposed `serverReadyAction` pattern in that environment?
