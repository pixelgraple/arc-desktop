# Coordinated helper teams

Arc can delegate up to 24 independent subtasks during one parent turn, with at most five helpers admitted at once. Each helper runs an actual bounded LangGraph `StateGraph` loop: `model → tools → model`, finishing when it produces a report. These are separate model/tool conversations, not workflow labels, extra model installations or operating-system processes. The parent remains responsible for the final answer, saved files and actions.

Delegation is useful for substantial independent research, coding proposals and review. It is optional for simple questions and small edits, and the model should use only the helper slots it needs. Quality depends on the selected local model's instruction following and tool calling. More helper calls can increase latency; the framework does not improve the underlying model by itself.

## Automatic task cycle

With helper tasks enabled, substantial High/Ultra work can request a validated five-assignment team. The planner gives each assignment a distinct task-specific question: requirements, research, approach, review, and verification. The first three run before the parent works; the final two examine the parent's actual draft, file identities, bounded source excerpts and available check receipts. They are five assignments across the task, not five copies of the whole task. Simple requests, fast voice and story writing skip automatic team overhead. An invalid or unavailable planner falls back to direct parent work.

The ordinary tool loop feeds the reviews back for a bounded correction and final answer. The project writer also participates: after its files and reference checks finish, the parent may reopen one existing file for a supported correction using the same exact-source/hash checks and validation loop. A review cannot authorize an unknown path or turn model opinion into a passing test. Runtime behavior remains untested unless an actual supported execution check was performed.

Explicit helper use can add further independent assignments up to the same 24-slot limit. Automatic teams reserve the final two slots for result review until that batch runs. A non-sealing join permits later review batches after the first research batch finishes. The settings toggle, tool enablement, no-web policy, cancellation and parent permission limits still apply.

## Scheduling and bounds

- 24 helpers **total** per parent turn, including failed and cancelled helpers. Exact normalized duplicate requests replay the original helper ID. Five active slots admit helpers in queue order; cancelling a queued task does not start its model or tools.
- Four ordinary model calls, at most one short evidence-recovery call, six attempted tool calls and a four-minute deadline per admitted helper. Waiting for an active slot does not consume that deadline. Final and recovery calls have no tools. Nonempty output marked truncated by the runtime, including voice word limits, is an incomplete **failed** report.
- Short plan-only promises such as “I'll verify that before reporting” receive a bounded continuation tied to the actual task. Repeated promises fail at the model-call limit. Useful findings followed by next steps remain valid reports; explicitly requested plans are allowed.
- Each helper receives only its task, explicitly supplied context, and approved file IDs. Parent conversation history, other helper histories and private tools are not inherited.
- Helper model requests share the existing parent `chat` callback and its local runtime/GPU lease. The service also serializes its own model calls. Read-only tools from different helpers can overlap; Arc does not load one model copy per helper. Reports and task records consume bounded ordinary RAM and local storage. Individual inference requests still consume context/KV memory and compute time; extra helpers are not a free speedup.
- Helpers are asked to target 150 words, at most 400 unless a code proposal requires more space. Reports are capped at 48,000 characters; model input and tool evidence are bounded excerpts with omission notices. These character bounds supplement the existing runtime's context fitting; they are not a claim of exact token accounting for every tokenizer.
- Context fitting preserves the newest complete assistant/tool exchange and its original call/result pairs. It retires older complete exchanges with a notice, trims shared background, then fairly excerpts fresh source results using their JSON-escaped size. If the task, protocol and source prefixes cannot fit safely, the helper fails explicitly instead of silently losing the evidence it just read.

## Permissions and evidence

Helpers can use only `web_search`, `read_webpage`, and `read_created_file`. Web tools must be supplied by the parent; a parent no-web policy passes `web: undefined`. Plugin enablement is checked when constructing each model request and immediately before every tool execution, including cached duplicate reads. The public reader remains responsible for DNS pinning and network policy; the helper service additionally rejects private, credential-bearing and otherwise invalid URLs before calling the reader.

`fileIds` is a JSON string containing at most six unique canonical Created File IDs. It is an explicit whitelist, not a filesystem path list. The existing Created Files service validates and reads each file. Helpers cannot list other files, save/revise files, execute/test code, access browser sessions, SSH, PC tools or saved memory, discover global capabilities, or delegate recursively.

Reports, source excerpts and proposed code remain untrusted. A helper's `complete` state means its report loop finished; it is not a verified factual, syntax, behavioral-test or security result. Do not convert model statements such as “tests passed” into application test receipts. The parent must inspect evidence, perform permitted checks and disclose uncertainty.

## Main-process contract

`createDelegationRun({ runId, conversationId, rootDirectory, persistReports = true, signal, chat, web, files, plugins, onSnapshot, contextTokens })` is synchronous. `runId` and `conversationId` are lowercase UUIDs. `rootDirectory` is the main-process-owned `appData/delegates` directory, never a model argument.

The returned manager exposes:

| API | Behavior |
| --- | --- |
| `size` | Number of lifetime helper slots used. |
| `invoke(name, args)` | Async tool dispatch; validation and tool failures return safe `{error, code}` JSON. |
| `snapshot()` | Strict bounded UI state, with no report body, filesystem path or hidden reasoning. |
| `join()` | Joins the current helper batch and native model work without sealing future review assignments. |
| `settle()` | Seals further spawning and drains all helpers and the actual native model queue. |
| `close()` | Cancels unfinished helpers and drains before releasing the parent GPU lease; call in `finally`. |
| `cancel(id)` | Immediately emits cancelled state for a queued/running helper and aborts its signal. Does not replace already finalized results. |
| `summaryEvidence(maxCharacters, {ids}?)` | Bounded array of untrusted report excerpts, hashes, truncation indicators and continuation offsets, optionally restricted to one batch. |
| `setPhase(phase, {ids}?)` | Host-only progress hint for running helpers. Does not execute work or change permissions. |

Parent-visible tools are `delegate_task`, `list_delegates`, `wait_delegates`, `read_delegate`, and `cancel_delegate`. Helper IDs have the form `delegate-<UUID>`. `wait_delegates` waits 1–30 seconds; its timeout is still-running status. `read_delegate` returns at most 12,000 characters, with `offset`, `nextOffset`, complete-report character count and SHA-256 of the entire UTF-8 report. Offsets are JavaScript string character offsets. A cancelled state can precede completed cleanup; reading during cleanup returns `pending: true`.

New snapshots contain `{runId, state, helpers, maxHelpers:24, maxActive:5, scheduling:'cooperative'}`. Legacy `maxHelpers:3` snapshots remain valid with their original cap, preserving old conversation artifacts. Each helper has `{id, label, task, state, phase?, summary?, error?}`. The optional phase is `understand`, `research`, `work`, `check`, `fix` or `report`; these describe model/report work, not verified execution. Summaries and errors are at most 800 characters. `onSnapshot` is called synchronously, receives a fresh validated object, and cannot disrupt the run by throwing or rejecting.

Full snapshots remain available to the UI and saved task state. Model-facing checkpoints use compact helper IDs and states rather than copying 24 tasks and reports into every prompt. `summaryEvidence` fits a report index and excerpts within its supplied character budget; use `read_delegate` with the returned ID and offset to read omitted report text. This bounds context usage without removing the selected model's context limit.

The supplied `chat` callback must respect its `signal` and must resolve/reject only after the corresponding native request has terminated. Cancellation races the helper's wait but **does not release the model queue** until that actual callback settles. Thus an incorrectly implemented native adapter that never terminates can intentionally hold `settle/close`; releasing the GPU early would be unsafe. Read-only adapters also receive AbortSignal. A broken reader that ignores it is given a one-second cleanup grace, then its observed promise is abandoned; aborted graph nodes cannot consume its late result or publish a later success. This is a JavaScript adapter limitation, not permission to continue useful background work.

## Persistence and privacy

Persistent reports use `delegates/<conversation UUID>/<run UUID>/delegate-<UUID>.json`, with ownership markers in the conversation and run folders. They are plain JSON reports, not LangGraph checkpoints. There is no graph restore/deserialization API. Reports omit model reasoning fields and tagged reasoning blocks, tool transcripts and private runtime errors. Report and record hashes are verified when reading. Publication uses an exclusive temporary file and atomic no-replace hard link; existing runs and report bytes are not overwritten.

Folder components must be normal owned directories; redirected folders, symbolic links/junctions and hard-linked report files fail closed. `deleteDelegationConversation({rootDirectory, conversationId})` performs complete ownership/name/link preflight before deleting only that conversation's subtree. Unknown files stop deletion. Main must close any active run before deleting its conversation. Missing report folders return `{deleted:false}`.

For voice turns that are not being saved, use `persistReports:false`. Reports then remain only in the manager's turn memory and no delegation folder is created.

Arc has no cloud tracing opt-in. Main bootstrap and the service explicitly disable `LANGSMITH_TRACING`, `LANGSMITH_TRACING_V2`, `LANGCHAIN_TRACING`, `LANGCHAIN_TRACING_V2`, and `LANGCHAIN_VERBOSE` before graph imports/runs. A per-invocation `traceable(...,{tracingEnabled:false})` remains as an additional boundary. Testing found that the wrapper alone did not suppress an internal callback boundary under hostile inherited tracing flags in the pinned dependency versions; therefore it is not the sole privacy control. No API key, cloud account, remote graph server or network checkpoint store is configured.

## Verification

Run `node --test tests/delegation.test.cjs tests/delegation-integration.test.cjs tests/helper-team.test.cjs tests/artifact-metadata.test.cjs`. The tests execute the installed LangGraph engine with fake local model/read-only tools. They cover isolated contexts, overlapping readers, serialized model calls, 24 lifetime slots, five active slots, duplicate replay, permission revocation, private URL rejection, no recursive/private/writing tools, timeout/cancellation, delayed native cancellation, noncooperative readers, truncated and plan-only reports, bounded excerpts, fresh-source retention at small contexts, immutable report hashes, report paging, ownership/link rejection, scoped deletion, memory-only voice and hostile tracing flags with a network stub. Integration tests cover parent joining, final artifacts, cancellation, legacy and expanded snapshots and inherited web restrictions. `node scripts/check-helper-tasks-ui.cjs` checks the isolated queue UI in Chromium without model inference or a user browser profile.

These tests verify orchestration and boundaries. They do not establish the quality or speed of any real local model; native end-to-end model trials are recorded separately by the runtime QA harness.

Dependency pins, upstream links, integrity values and licenses are recorded in `integrations/langgraph/`.
