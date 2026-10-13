# Multi-file website builds

Arc can create a local website one file at a time, using its existing coding workspace. It saves the project plan and source to disk instead of sending every completed file back to the model on every turn.

## Use it

1. Select a text model. Arc Focus is supported by the same native tool protocol as the other supported text backends.
2. Choose **Ultra** and enable **Until done**.
3. Ask for a new, explicit multi-file project, for example:

   > Build a complete website for a coffee shop with index.html, menu.html, assets/styles.css and assets/app.js. Use shared navigation, show three menu items with prices, and include an accessible button that changes a status message. Keep everything local.

4. Arc plans the files and their dependencies, then writes and checks them in order. Large files can take several saved sections. With Until done off, this workflow pauses after one completed file.
5. Open **Code → Local website workspace**, choose the project, and click **Export & open folder**. The source files and nested directories are exported together. The attached text report is a manifest, not a replacement for the website files.

If you stop, close Arc, or reach a work limit, reopen the same conversation and ask **“Continue the unfinished task.”** Arc reloads the task checkpoint and project record, verifies the saved source hashes, and continues the unfinished file. It refuses to overwrite source that changed outside the build.

Generation and export do not require Docker. The existing optional PHP preview runs only in its isolated runtime. Creating files does not run them, install packages, access a server, or publish a website.

## What Arc remembers

The existing coding-workspace record contains the original request, shared conventions, file list, purposes, dependencies, interface contracts, completion states, source hashes, syntax-check receipts, and short per-file checkpoints. Every saved section updates source and build state in one atomic record. The task checkpoint contains a pointer to this project rather than a second copy of its source.

For each new model call, Arc supplies the original request, applicable system instructions, compact project details, the current file's outline and exact ending, and dependency summaries. It can read bounded ranges of already saved files to recover exact interfaces. It can repair lines in the current unfinished file only after reading that range and verifying the revision. The host controls filenames, hashes, file order and persistence; the model does not select an arbitrary output directory.

Existing File History also receives source versions when its backup service is available. A backup failure does not replay an edit or discard the authoritative workspace record. Export creates a fresh snapshot, preserves relative directories, and verifies every exported file against its source hash.

## Checks and limits

- 2–80 planned files, 512 KiB per file, 8 MiB of source per project, and the existing limit of 30 local workspaces.
- PHP, HTML, CSS, JavaScript, JSON, TXT, Markdown and SVG source. Hidden files, production configuration, credentials, unsafe paths, linked directories and file/folder collisions are excluded.
- Maximum 256 model rounds in one run, with bounded corrections. Longer work can resume from its checkpoint; it does not silently run forever.
- Generation stays within the selected native context window and output budget. The desired character size is a writing target; a separate bounded storage limit permits valid source blocks that still fit the token allowance.
- Available syntax parsers run before a file is marked complete. Static local HTML/CSS/JS references are checked after all files finish, with bounded repair attempts. Dynamic routes, PHP-generated links, remote assets, design quality and rendered behavior are not proven by these checks.
- A missing dependency, repeated section, malformed tool call, storage error, changed revision, insufficient context, or exhausted correction budget produces an honest incomplete result. Saved progress remains available.

This is disk-backed working memory, not unlimited model context. Arc can produce a combined project larger than one reply because it processes relevant parts separately. Very long original instructions, a very large plan or many dependency contracts can still exceed a small working window. A small model can also produce incorrect code; review and test it in the protected preview before deployment.

The automatic route is deliberately for new, self-contained projects. Requests to research, import, modify an existing project or publish continue through the existing tools, so the dedicated writer does not bypass those workflows.

## Verification

`tests/project-build.test.cjs` covers sequential generation, restarts, partial-section cancellation, changed-source protection, invalid paths, bounded prompts, syntax repair, missing-reference repair, restricted clients, native-protocol recovery and safe folder export. Runtime and Python worker tests check registration and unchanged context capacity. The coding-workspace browser fixture checks desktop and narrow layouts, folder export and unsaved-edit protection.

`node scripts/benchmark-project-build.cjs` runs real Arc Focus in an isolated QA workspace. It pauses after the first completed file, closes its own model worker, starts a fresh worker, resumes from disk, and records file hashes and export receipts. Reports under `work/qa/project-build-*` distinguish failed attempts from completed runs. It does not change the user's installed configuration or run the generated website.
