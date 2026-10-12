# Coding tools

Arc delivers requested code as real files in Documents/Arc/Created Files, or the selected Arc workspace. File cards provide **Save a copy** through a native file chooser and **Open folder** in Explorer, in both chat and voice. Neither action executes the file. Save a copy verifies the saved version and exact bytes, preserves existing destination files, and reports a changed original instead of silently exporting different code. Cancelling creates nothing. Repairs save a new numbered file and retain the original. Existing local attachments can be reviewed and saved as corrected copies; Arc does not silently overwrite the attached original.

## Create, append and fix specific lines

Ask in Code or chat, for example:

- “Create a PHP contact form and save it as contact.php.”
- “Append a logging helper to app.js. Keep everything already in the file.”
- “Read lines 40–80 of app.js, fix the problem on lines 52–58, then check the result.”

`create_file` saves a new file without overwriting an existing one. `append_created_file` adds only new content to the end of the exact existing file, including files saved with Arc's older folder layout. Arc reads its current SHA-256 before appending and checks supported syntax afterward when automatic checks are enabled. Append steps preserve the existing source in the model's working context; they do not switch to a new-file generation prompt.

`read_created_file` supports one-based `startLine` and inclusive `endLine`. Reads are bounded to whole lines that fit the context; `nextStartLine` identifies where to continue. `revise_created_file` accepts the same range and replacement `content`, so Arc can fix a small section of a large file without reconstructing unseen code. It must have read the entire affected range in the current turn. The current hash is checked again; changed files, guessed ranges and out-of-bounds edits are rejected. Empty replacement content deletes the selected lines. Untouched bytes, the source BOM, and existing line endings are preserved. The corrected file is saved as a numbered revision; the original remains available. Read the new revision before another edit because its hash and line numbers may differ.

Code → Local PHP workspace also supports `append` and `replace_lines` through its existing `coding_workspace` tool. These change the local staged copy only. Syntax checks and preview need to be refreshed; publishing remains a separate reviewed user action. No generated code is executed merely by creating, appending or editing a file.

Created Files remain limited to 2 MiB, append chunks to 24 KiB, and a read request to 400 lines (usually fewer to fit the active context). Local PHP workspace files remain limited to 512 KiB. A single line larger than the available read budget needs a smaller source representation or the workspace's exact-text replacement tool. Chunking improves file handling; it does not make the model's context unlimited.

File tasks keep code and tool arguments out of the chat stream throughout creation and checking. The final response uses actual saved-file receipts, with checks shown separately on the cards. An explicit request to show or explain code can still receive that content. Malformed tool syntax is never interpreted as a successful save. Narrow follow-ups such as “PHP script, login page, as described above” can continue a recent user file request; retrieved sources and model replies cannot authorize a write.

The following additions are optional offers on upgrades. Installing one enables its switch; later updates preserve that choice. The existing 250 baseline capabilities remain unchanged.

| Switch | Actual behavior |
| --- | --- |
| Read saved code | Reads a bounded UTF-8 snapshot or exact line range and SHA-256 from Created Files. |
| Automatic code syntax checks | Checks saved HTML, CSS, PHP, JavaScript, Python and JSON after creation or revision. Also exposes an explicit check tool and a Recheck action on saved file cards. |
| Edit code · full file or line range | Uses an observed hash, saves a corrected copy with a full-file or targeted line edit, checks it again, and preserves existing behavior-test assertions. Up to three revisions per file chain and the overall turn limit. |
| Aider code map | Runs Aider 0.86.0's repository-map component on selected saved-code snapshots. Includes PHP, JS/TS and Python symbols and relationships without sending the whole library to the model. |
| Isolated JavaScript tests | Runs explicit synchronous assertions against the saved plain-JavaScript source in QuickJS WebAssembly, not the host JavaScript engine. |
| Isolated Python execution | Runs a saved `.py` file only after a direct request, inside a fresh Pyodide CPython WebAssembly worker in a sandboxed Electron renderer. No host filesystem, external network, process, shell, DOM, credentials, system areas, or elevation. Selected Created Files text inputs and bounded text outputs only. |

## Checks and limits

### Test, debug and correct an existing file

Use Code or chat with a saved Created File, or first copy your own file using **Choose input file** in Plugins. For example:

- “Check config.json. If any errors are found, correct them.”
- “Debug sum.js. It should add two numbers; test normal, zero and negative inputs.”
- “Look for errors in login.php and correct them.”
- “Check app.js, syntax-only. Do not execute or change it.”

Arc reads the exact saved file and runs its enabled syntax checker. A failed check feeds concrete diagnostics into the existing repair loop, which saves a numbered corrected copy, retains the original and checks the corrected bytes again. For large files it can read and replace the affected line range. JavaScript test/debug requests also require actual isolated assertion results; a syntax pass cannot stand in for these tests. Repairs rerun the original assertions without weakening them. Clean files can remain unchanged without creating an unnecessary revision.

Checks are reused only for the same saved hash within a turn. Missing originals, changed bytes, unavailable runtimes and disabled checkers prevent a false success report. A check-only request does not grant editing permission. Syntax-only or “do not execute” requests do not offer JavaScript execution. The bounded repair limit remains three revisions per file chain, preserving partial work if it cannot finish.

This is not an unrestricted debugger: PHP/Python/HTML/CSS/JSON receive the static checks described below; PHP applications, databases, browser DOM, dependencies and integration behavior are not automatically executed or verified by this workflow. Local PHP workspace preview and explicit protected Python execution remain separate tools with their existing restrictions. Other languages report the missing checker rather than inventing a pass.

PHP uses the bundled PHP 8.3.35 CLI with `-n -l` and source on stdin, so no project php.ini or extensions load and submitted PHP is not executed. This requires the Microsoft Visual C++ x64 runtime normally present on supported PCs. JavaScript syntax uses the bundled Node runtime's `--check`; Python syntax checking uses Arc's configured Python to parse and compile source only, without executing imports or user code. Running Python is a separate explicit action in the isolated WebAssembly runner below. JSON uses a bounded parser process. Other languages honestly report that a checker is unavailable.

HTML and CSS use a bounded, offline parser process with HTML-validate, CSS Tree and Acorn. HTML structural syntax and supported inline CSS, JavaScript and JSON blocks are checked without running scripts or loading project configuration. Linked resources, page appearance and browser behavior are not tested; skipped embedded languages are reported. A syntax pass is not a security audit or functional test. Recheck checks the exact saved revision and updates only that file's validation receipt; it refuses a stale or changed file and cannot run during a reply or voice session.

QuickJS tests support plain synchronous JavaScript globals with `assert(condition, message)` and `assertThrows(callback, message)`. The message is optional. `assertThrows(() => validate(null), 'Reject null')` passes only when its function callback throws an ordinary synchronous exception; a non-function or a callback that returns normally fails. Both helpers use the same trusted 256-assertion budget and are available only while a test runs, not while the source file loads. Resource limits and unsupported operations still fail the check even if an exception is caught.

The tests have no host filesystem, network, Node modules, imports or browser DOM. Acorn 8.18.0 parses both the exact saved source and assertions inside the bounded worker before execution. Async/await, Promise references, modules and dynamic compilation (`eval`, `Function` or constructor access) report unavailable, including unused async functions. This conservative gate avoids falsely passing ignored asynchronous failures; comments, strings and regular expressions mentioning those words remain valid. Computed aliases cannot obtain the native Promise or compiler entry points. The interpreter has a 16 MiB heap limit, a two-second execution deadline, and an independent process deadline. Test code is also untrusted. Existing assertions cannot be removed or changed during a repair chain; new tests may be added. Full project/browser integration tests are not provided by this feature.

## Python execution sandbox

Arc bundles the pinned Pyodide 0.29.5 runtime. A user must directly ask Arc to run a saved Python script; writing, opening, or syntax-checking a `.py` file never runs it. Before execution Arc requires the exact current file receipt and hash from a read or create action. Optional inputs must also be read from Created Files in the current turn and passed as IDs plus hashes, never filesystem paths.

Each run gets a new hidden Electron Chromium renderer with Electron's sandbox enabled, Node integration disabled, context isolation enabled, an in-memory nonpersistent session, all permission requests denied, and a strict content-security policy. The page can load only Arc's fixed runner and Pyodide runtime assets from a loopback server that exposes no write routes. The Python interpreter runs inside WebAssembly with a fresh virtual filesystem, separate `/workspace/project`, `/workspace/input`, `/workspace/output`, `/workspace/tmp` and `/workspace/home` folders, and no native process, shell or operating-system filesystem API. Network requests are blocked by CSP; no package installation or pip is provided. The only bridge to Arc is bounded text input/output. The renderer is destroyed after each run, and session data is cleared. Text results are saved as new files under Arc Created Files; Arc never launches those outputs.

Limits are 1 MiB of source plus selected inputs, 8 inputs, 15 seconds of execution, 16 KiB of stdout/stderr, 10 output files, 128 KiB per output and 512 KiB total. Non-text outputs, unsafe names and unsupported extensions are discarded. This is a meaningful capability boundary, not a promise of a full operating-system security boundary: Arc does not impose a hard WebAssembly memory cap, and Chromium, Pyodide or OS vulnerabilities and memory exhaustion remain possible. Only run code you trust. Existing MCP servers and explicit SSH commands are separate integrations with their own access and are not converted into this local sandbox.

Syntax passes do not prove behavior, security, dependencies or compatibility with a deployment's runtime. Passing explicit assertions covers only those assertions. The UI keeps syntax and behavior results distinct, tied to the saved file hash. Later external edits require another check.

## Aider component

The supplied [developer-tool catalog](https://github.com/M1n9X/llm_agents_devtools) lists [Aider](https://github.com/Aider-AI/aider). Arc includes the actual repository-map component at commit `a4be6ccd87ebaa59b361f3f028d116ce1761b626`, with upstream Apache-2.0 license and dependency notices. Arc does not invoke Aider's autonomous CLI, shell or editing agent.

Installing its switch creates an isolated Python environment under the user's Arc data directory from bundled, hash-checked offline wheels. It needs Python 3.12 x64. No API key, cloud model, GPU or network request is used by mapping. The offline wheels take about 76 MB; their installed environment requires additional disk space. Source is read only from Created Files, up to 100 supported files, 128 KiB per file and 2 MiB total, with bounded output and runtime. The map is selective; read the relevant source before revising it.

The existing Exa-backed web search and webpage reader remain available. The catalog's larger autonomous agents were not installed wholesale because they duplicate Arc's orchestration and introduce broader shell/provider requirements.
