# Long output and task memory

Arc 0.51.61 improves work that cannot fit in one generation. Select **Think deeply → Ultra → Until done**, and ask for the complete deliverable. For large documents or code, request a file. Open the conversation's **Context** panel to pin requirements and inspect its saved task.

## What changes

- The native working window stays at the model's verified setting. At 8K context, file sections are limited to 2,400 characters with a 600-character progress note. At 16K they use 4,000 and 1,000; larger windows use 6,000 and 1,000. The native tokenizer still checks each request, tools and output allowance.
- The required file-writing phase reserves 3,072 output tokens at 8K, or 4,096 at larger windows. A crowded call can borrow output headroom down to 1,536 tokens; it cannot overflow the native window. Ordinary full-file operations keep their existing policy.
- Structured/code sections use the existing precise whole-file sampling settings. Story and ordinary prose sections retain their normal sampling style. Oversized sections retry with a smaller section target; earlier accepted sections are not regenerated.
- Each successful section saves its current hash, exact ending and a compact structure index. Arc binds subsequent sections to that exact file and revision, so the model only supplies the next content and progress note. It rechecks the revision before writing. External edits or missing files stop automatic append rather than overwriting changed work.
- Explicit numeric paragraph, function and JSON-entry requirements receive mechanical minimum-count checks. Explicit numbered paragraphs cannot restart their numbering; an exact paragraph count also caps the total before a new section is saved. These are partial completion checks, not a semantic proof that every instruction was followed. Syntax and requested behavior checks remain separate.
- Substantial repeated prose is rejected before appending. Reply continuations receive bounded rewrite attempts without feeding the rejected draft back into the model.
- Each JSON section is checked before saving, including unfinished prefixes. Missing commas, broken escapes and other syntax errors are rejected immediately with correction feedback; the final document must parse completely. Earlier sections remain intact. An already complete saved file can be finalized and checked without appending duplicate text.
- JSON files with an explicit numeric entry target can use complete record batches. For an array or a one-array-field object, Arc joins the batches and holds the outer closing delimiters until the target is reached. Exact value text, including large numbers and string escapes, is preserved; the outer formatting may change. Distinct-entry requests reject repeated batches. Ambiguous multi-array shapes and explicitly unchanged/verbatim formatting use the strict text path.
- Length-limited text replies can continue through up to 32 additional generations. Sources remain bounded and citations must come from retained evidence. A natural stop is still a model judgment; Arc does not force every answer to become long.
- Task checkpoints survive app restarts. **Prepare to continue this task** places a continuation request in the composer and enables Ultra/Until done; the user submits it. Arc never silently replays actions when starting up.
- The context panel shows pinned requirements, saved file progress, selected source excerpts and recorded output-token/section counts.
- Project references are archived locally and supplied as task-relevant exact excerpts instead of copying entire documents into each prompt. Omitted source is available through the existing original-source reader. Removing a project/reference also removes its associated recall archive.
- Waiting voice inference takes priority over waiting helper work within the same queued model phase. It cannot interrupt an active generation or jump across a pending model load/unload.

## Hardware and real limits

These changes are based on the active context window and use the existing backend and model eligibility checks. They do not assume NVIDIA, add a second inference engine, change the 32 GiB RAM policy, or allocate a larger GPU cache. Computers with 4, 8, 12 or 24 GB VRAM still need a model/runtime/context combination that actually fits. The feature does not guarantee that every model can run on every memory tier.

SSD storage preserves originals and accepted output; it does not make all earlier tokens simultaneously visible to the model. Selective retrieval and compact checkpoints can omit relevant details. Small models can still make factual, formatting, planning and instruction-following mistakes. More continuation calls can increase total latency well beyond 10–20%; no fixed performance penalty is promised.

Limits remain: 2 MiB per Created File, 512 file sections per run, 32 additional answer generations and 2 Mi characters for the assembled reply. Stop, permission boundaries, sandbox execution rules, stale-revision checks and repeated-failure limits still apply. Work reports incomplete when a limit or unresolved error is reached.

This release does not enable unverified native contexts, new Strata KV modes or a new model. Strata remains supported through its existing backend. Larger native contexts and experimental cache formats need separate hardware/backend validation.

## Persistence

Task memory is stored under `task-memory` in the local Arc user-data directory. It keeps the original request, public progress receipts, accepted reply, compact output notes and up to 2,000 characters of user-pinned requirements. It does not store model chain of thought. Writes use temporary files, fsync and atomic rename; records and the total store have quotas. Deleted conversations remove their task record. File History and source originals remain separate stores with their own deletion controls.

## Verification

Automated tests cover interrupted-file resume, stale hashes, checkpoint deletion, count-based early completion, repeated prose, invalid partial and final JSON, finalization without duplicate bytes, bounded long replies, source excerpts, protected context budgets and voice queue ordering. A deterministic orchestration test assembles a 105 KiB file in 45 sections at an 8K selected window. That test uses scripted model responses and is not an intelligence or GPU benchmark.

`scripts/benchmark-output-continuity.cjs` runs actual installed models through the production tool loop with isolated files. Use `--application-prompt` for Arc's actual base system prompt; the default is a short QA prompt. User guides and project state are not included. It reports content checks separately from the application's completion status and records source hashes. The `--long` case requests 2,000 JSON records; it is deliberately a harder endurance case. Reports must retain failures and timeouts. The script defaults to CPU; an explicitly selected installed CUDA backend must pass the normal trusted-runtime and memory checks. Results from this PC do not establish results for other GPUs. Generated-token totals include all model calls and are not file size or a tokens-per-second benchmark.
