# Shared workstation task lifecycle

Arc 0.51.77 wraps the existing tool agent and project builder in a small `workstation-session.cjs` service. The original executors, tools, permissions, model choices and completion guards remain authoritative. This is an orchestration and continuity improvement, not a replacement inference engine.

## How a task proceeds

1. Preserve the user's actual request, constraints and existing task interpretation.
2. Inspect the relevant originals and choose the next permitted operation.
3. Carry out the existing tool or project workflow. Observe its receipts separately from its progress label.
4. Keep a compact record of source reads, file revisions, captures and checks across model calls.
5. Check the changed result, repair specific failures and rerun affected checks.
6. Deliver the result with actual artifacts, citations and remaining limitations. The existing completion policy decides whether the result is incomplete.

Ordinary conversation is encouraged to answer directly. The shared layer adds no planning-model calls, helper calls, new tool permissions or model reloads. Internal intent interpretation, compaction and helper requests retain their original schemas without injected workstation context.

## Evidence and state

The progress panel exposes preparing, researching, writing, inspecting, checking and answering phases derived from actual activity. Its expandable record separates attached file receipts, completed project files, source reads, saved screenshots, vision analyses, syntax passes and behavior-test passes. A click or running-tool label never establishes a source read, capture or passed test.

The file index keeps identity, name, size, SHA-256 and separate syntax/test states. A new hash clears previous passes; checks identify both the exact file and its revision. Source cards do not increment successful page reads. Captures and semantic vision analyses are independent. Successful members of a partially failed screenshot batch remain counted.

The working index is limited to 256 recent items per category. Model-facing receipt JSON is capped at 1,400 characters for context windows at or below 8K, or 2,400 otherwise, plus fixed task guidance. It drops whole optional records rather than cutting JSON, identifiers or hashes. This uses space inside the selected context window; it does not increase model capacity. Current-request text and structured evidence suffixes are left intact. Native model token fitting remains authoritative.

If the added guidance and record would exceed the executor's housekeeping budget, that optional prompt addition is omitted. Exact instructions and fresh evidence take precedence; the saved record and UI remain available. General workstation recipes use a separate fallback ranking group so they cannot suppress a more specific language or platform recipe. An explicit request for a workstation workflow can prioritize them.

Only typed receipt metadata enters this record: no raw page bodies, tool arguments, credentials or private model reasoning. Filenames and other values remain untrusted data. The context is rebuilt for each parent call instead of being repeatedly appended to history. Historical checkpoints cannot grant permission or establish that current external state is unchanged.

## Integration and compatibility

The wrapper preserves callback return values, original tool exceptions, cancellation, file receipts and every existing incomplete-result flag. The shared progress field is optional, so older chats still load without migration. Checkpoints retain the compact record alongside the existing exact artifact and task state. Progress rendering uses existing Arc theme tokens and a keyboard-accessible disclosure in the current task card.

Eight detailed, searchable workflows are bundled in Settings → Plugins under “Workstation”. They cover conversation, research, code repair, projects, visual evidence, browser tasks, long work and recovery. They are instructions using existing capabilities; they cannot connect accounts, execute arbitrary host code or grant access. The prior guide catalog and user enable/disable choices remain supported.

## Limits

This does not guarantee correct reasoning, remove context limits, make unavailable websites accessible or turn syntax checks into runtime tests. Existing permissions, sandboxing, CAPTCHA handling, no-progress stops and final evidence requirements still apply. The progress index reports recorded scope, not an independent certification that every user requirement is complete.
