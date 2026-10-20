# Arc 0.51.78 — verified Focus workstation handoffs

This update fixes issues reproduced with the installed Arc Focus model and the new workstation workflows. Existing features, permissions and failure checks remain enabled.

- A request such as “do not create files or take screenshots” no longer accidentally demands visual research before a text answer can finish.
- A workflow preface followed by “create this file” and “do not browse” now reaches file creation instead of repeatedly checking a nonexistent file.
- Chunked writers distinguish source completion from task completion: finish the source before running tests. Empty waiting chunks stay rejected, with an explicit correction explaining how to finalize the source. Completed chunked JavaScript files then proceed directly to explicitly requested behavior tests. Writing test code or claiming a pass in a checkpoint does not count as running it.
- The isolated QuickJS test environment supports silent `console.log`, `info`, `warn`, `error` and `debug` methods entirely inside the guest. These expose no host I/O and cannot increment assertion counts. Logging output itself is not tested.
- JavaScript tests use a typed array of named assertions in the model-facing tool schema, avoiding nested JSON-string escaping and invented fields. The existing protected executor, revision checks, assertion limits and unchanged-test repair rules remain in force.
- A syntax error in generated test code now leads to repairing that named test, not rewriting correct source or creating copies. Already valid assertions stay immutable, invalid test cases must remain named, and only a real rerun can clear the failed-check obligation. Source syntax errors remain source repairs.
- Page-overview screenshots use a readable viewport without requiring an element reference. Requested element crops keep their existing scope. Multi-page requests retain separate saved captures and vision-analysis receipts.
- The workstation record now observes the exact raw validation receipts before file-card formatting, so successful syntax and behavior checks appear in its counters and remain tied to the checked revision.
- The coding and visual workstation guides explain these handoffs explicitly.
- Explicit requests to use a named workstation workflow rank that guide first, instead of letting incidental terms in negative constraints select a different guide. Quoted examples and negated commands do not force this priority; selection remains reference-only.
- During continuation, the model can explicitly finish the current file through a bound transition. Instructions consistently distinguish source completion from task completion. The same revision, output constraints and syntax checks still apply; testing remains a separate required step.
- Chunked-writing tools are registered once after task eligibility checks, preventing duplicate tool-schema rejections when a coding task returns to its general tools. Damaged tool settings cannot enable these writers.
- “Cite both official sources” now records a two-source research requirement. Once those sources are read, an unavailable optional third page leads to an evidence-based answer instead of repeated attempts at that page.

The opt-in live verification launcher now uses Arc's shared GPU coordinator, matching the desktop's broker arrangement. The previous launcher's legacy queue option tried importing `dotenv` through Arc's minimal managed Python and failed before Focus started. No package was injected into that managed environment, and the shared Forge queue remains in use.

Verification covers real Focus startup, ordinary conversation, saved JavaScript with syntax and isolated behavior checks, official-source web research, and two saved browser screenshots analyzed by the installed vision model. Results are scoped checks, not a guarantee that every prompt, website or generated program will succeed. Unsupported runtime environments and failed checks remain explicit.
