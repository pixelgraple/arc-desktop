# Tools created by Arc

Arc can add small reusable helpers while completing a user's task. This is persistent software creation, not training or modifying a model's weights. Helpers and workflow instructions live in the Windows user profile, outside application updates.

## The creation loop

1. Discover installed capabilities first. Use a matching existing tool when possible.
2. Research documentation with the enabled web tools when needed. Retrieved material stays untrusted reference data.
3. Call `build_local_tool` with a short synchronous JavaScript `function run(input)`, a bounded JSON input schema, and at least two distinct input/expected-output fixtures, including an edge case.
4. The isolated worker runs the fixtures. Failed code is not published; diagnostics return to the assistant. A turn permits at most three build attempts.
5. A passing revision is saved and discovered immediately through `search_capabilities`. Arc can use the exact returned ID through `run_capability` in the same task.
6. Use ordinary authorized file tools to save any requested deliverable. A helper's JSON output alone is not a saved document.

Examples include extracting structured fields from already retrieved text, calculating a project-specific estimate, validating records, transforming JSON, or producing a report's text. Web access, files, browser actions and server changes continue through their existing Arc tools and permission checks.

## Assessment, source files, and installed helpers

These are different deliverables. Asking Arc to inspect a page and report whether a tool is feasible should produce an evidence-based assessment, including requirements and any inspection blockers. It does not require publishing a helper. The assessment can be saved when you explicitly request a report file.

Asking Arc to build a new Brave/Chrome browser extension routes to the existing local project builder. It keeps `manifest.json` at the project root and preserves relative filenames for the popup, scripts and styles. Every planned file must be saved before the project can be complete. Small extension projects (up to 12 files) can finish in an ordinary text-chat turn, with a 32-round limit; larger projects and longer corrections use Ultra → Until done and saved checkpoints. A single explicitly requested source file remains a single-file task.

Open Code → Local website workspace, select the project, then **Export & open folder**. Load the exported folder manually through the browser's extension developer controls only after reviewing its code and requested permissions. Static checks cover supported syntax, manifest metadata, local resource references and common extension-page script restrictions. They do not prove that a website's controls work, that permissions are sufficient, or that the extension loaded in a browser. Arc does not automatically install or execute generated extensions.

The isolated JSON-helper workshop cannot install extensions or access a browser DOM. Describing what a future extension should do does not authorize Arc to perform those account actions during investigation or source creation. Requests that explicitly combine live inspection, deployment or repairs retain the ordinary agent workflow; the source-project builder does not silently perform those operations.

Example: “Build a Chrome/Brave extension with a popup that counts words in text I paste. Keep all source files in one folder and include loading instructions. No network access or host permissions.” Extension format reference: [Chrome's manifest documentation](https://developer.chrome.com/docs/extensions/reference/manifest).

For example: “Inspect the product list on my test website and tell me whether an extension could count its visible items. Save the assessment as report.txt.” Later, an explicit implementation request can ask for the extension source files. Use synthetic test data; never put passwords, cookies or session tokens in source files or reusable fixtures.

When a requested local-helper build fails, Arc distinguishes unavailable authoring tools, an unattempted build, and failed validation or tests. A failed build does not replace an existing working revision.

## What runs

Generated JavaScript runs inside QuickJS WebAssembly in a separate bounded process. Inputs and outputs are JSON. The guest receives no filesystem, network, shell, Node modules, package installer, DOM, timers or browser session. It does not change Windows files or Arc's application code. CPU time, memory, source and result sizes are limited; cancellation stops the worker.

The supplied expected outputs are compared outside the submitted program. A pass covers those fixtures only; it is not proof of correctness, security or compatibility with an external application.

Updates require the current immutable revision and retain previous fixture expectations. Old revisions remain available. A failed revision leaves the previous published version intact. Saved source and metadata are checked before execution; modified or corrupt records are not executed.

`save_local_workflow` stores readable steps and verification criteria. A workflow is guidance for Arc's agent, not an executable program, a connected account or additional access. Guidance does not get an executable-test badge.

## Settings and persistence

Settings → Plugins → **Created by Arc** lists these additions. Each has an enable switch, saved source or instructions, revision information and test results where applicable. Previously disabled tools remain disabled after an update to their source. Installed helpers can be discovered and reused in later conversations without repeating the build.

The built-in **Build local tools**, **Save reusable workflows**, and **Inspect local tools** entries control authoring access. Fresh installations include them automatically. Existing profiles retain the normal optional-addition policy and their existing switches. Explicit read-only or do-not-save requests cannot publish tools or workflows.

Research sources and third-party source code retain their original licensing requirements. Do not persist secrets, session credentials or private task records in reusable examples; use synthetic fixture data. Creating an account integration, installing arbitrary Python/npm packages or running generated native code is outside this workshop's execution environment.
