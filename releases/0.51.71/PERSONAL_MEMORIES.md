# Automatic personal memories

Settings → Memories → **Details you’ve shared** shows short personal facts captured from new direct messages. **Automatically remember personal details** is on by default; the existing **Use saved memories** master setting also needs to be saved as on. Turning either off pauses automatic capture and recall. Existing manual notes remain separate and unchanged.

Examples:

| Direct message | Compact memory |
| --- | --- |
| My name is Alex. | Preferred name: Alex. |
| I live in Indiana. | Location: Indiana. |
| I mainly code in PHP. | Preferred coding language: PHP. |
| I prefer concise replies. | Reply style: concise. |

An explicitly headed block also works: `Personal information: Name: Alex; Coding language: PHP; Hobbies: gardening`. The recognizer uses conservative English patterns and known vocabularies for name, coarse location, occupation, spoken/coding languages, hobbies, reply style and interface theme. Unsupported wording is skipped, not guessed. It is not a general semantic profiler or model training.

## Controls and limits

- Edit, remove, or clear automatic facts in Memories. Changes save immediately and never replace unsaved manual notes. A manually edited fact is protected from later automatic replacement; remove it if you want future declarations to recreate that category.
- Repeated facts are deduplicated. A clear positive correction replaces the same category. Ambiguous or negative corrections are skipped; use Edit/Remove for those cases.
- Capture is limited to four facts per message, 180 characters per fact, 40 stored entries and 768 measured memory tokens. The current extractor has eight categories. Full storage retains existing entries rather than silently evicting them.
- Recall selects relevant complete facts in a pack of at most 256 tokens, including provenance. The pack is omitted if the existing prompt lacks room. Manual notes, the current request, reply reserves and tool reserves take priority.
- Direct vision-only answers keep their image analysis isolated; automatic facts alone do not load an extra text-synthesis model for those answers.
- Capture uses CPU-only recognition and the existing token-counting worker. It does not load a model, allocate GPU memory, call an external service or scan past conversations. Saving runs outside the reply path. Actual latency depends on the PC and storage; no speed benchmark is claimed.

## Source and privacy boundaries

Only newly accepted direct user text is considered. Saved microphone conversations can contribute their direct user question; temporary voice conversations cannot. Assistant replies, tools, web pages, attachments, retrieved history, expanded prompt commands and screen/shared-audio observations are not extraction sources. Clearly marked quotations, pasted/imported examples, fiction and transcripts are rejected. Unmarked pasted text cannot be distinguished reliably from a user's own words, so review the saved list.

The automatic recognizer skips secrets, contact details, precise addresses and sensitive declarations. These filters are conservative rather than a guarantee that text can never be misclassified. For other information you deliberately want to keep, the existing manual notes remain available.

Facts live in an independent encrypted `personal-memories` directory under Arc's user-data folder, protected with Electron safeStorage. There is no plaintext fallback. The original message is not copied into this store; normal chat retention still applies separately. Deletion/clear/disable refresh both encrypted copies, and queued captures are invalidated to prevent a late save from restoring removed data. These operations are not forensic deletion from the device, backups or chat history.

Automatic capture does not upload its facts to BrainAPI; that feature retains its own explicit controls. Facts supplied to the model are marked as fallible reference data, never current tool authorization. Current corrections and manual notes take precedence.

## Implementation and verification

`personal-memory-facts.cjs` performs bounded extraction; `personal-memories.cjs` reuses the encrypted atomic storage with independent revisions; the main-process accepted-message boundary starts capture. `permanent-memory-context.cjs` selects the small recall pack. The Memories UI subscribes to changes without overwriting the manual draft.

Tests cover recognition and exclusions, deduplication/corrections, protected edits, capacity, encryption/restart, recovery-copy deletion, stale revisions, clearing/disabling during a write, main-process source boundaries, bounded recall and responsive editing. They use synthetic personal facts and isolated storage; they do not mine existing user conversations.
