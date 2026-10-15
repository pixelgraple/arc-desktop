# Relevant recall from past conversations

Arc can use relevant parts of earlier saved chats when answering a new written-chat request. This helps carry project decisions, earlier work and corrections into a different conversation without asking you to paste the entire history again. It adds retrieved reference material to the selected model's working context; it does not train the model or make the context window unlimited.

## Controls

Open **Settings → Memories → Past conversations → Use past conversations**. The preference is on by default, including when upgrading older settings that did not contain it. An explicit off choice stays off through restarts and unrelated settings changes. The toggle saves immediately, separately from the manual notes editor, and is unavailable while a reply, voice session or settings save is in progress.

The existing **Use saved memories** master setting also needs to be saved as on. Turning that master off pauses automatic local recall; the past-conversation preference remains visible and independently editable. Changing the master switch requires an idle chat with voice closed and no settings save pending, so the recall policy cannot change partway through a reply. The manual editor remains available; note saves that keep the existing master choice are still allowed. If saved-memory settings cannot be read, recall stays paused rather than assuming permission. Manual notes and **Details you've shared** retain their separate editing and capture controls.

For one request, say **“Use this chat only”** or **“Don't use past conversations for this answer.”** These direct instructions exclude cross-chat recall for that request without changing the saved preference. They do not erase existing conversations or remove the current chat's own context. Quoted examples and source material are not treated as new recall instructions.

## How a request uses saved work

1. Arc keeps saved conversation messages in its local searchable archive. CPU indexing prepares relevant text for retrieval in the background. Existing keyword and semantic search facilities are reused; no additional GPU model or model-weight download is required by this feature.
2. The current request supplies the search query. Arc looks for relevant messages in other saved conversations, then checks selected matches against the current saved originals. Deleted, changed or incomplete messages are not accepted as unchanged evidence.
3. A bounded selection carries source identifiers, conversation titles, message roles and available dates. When a matched discussion contains a recognized later user correction, the related context travels with it so the older statement is not recalled alone. Correction recognition is conservative and does not establish that every later statement applies to the new task.
4. The context planner fits useful excerpts around the current instruction, normal working history and answer reserve. Only excerpts that survive that selection are reported as used. Old transcripts are not copied wholesale into each prompt.
5. The selected local model uses those excerpts as historical reference. Current instructions take priority. A past assistant reply remains an unverified claim, not proof that an action succeeded or that an online fact is still current.

The multi-file project writer receives the selected historical context explicitly. It keeps related corrections and topics together, and drops optional history if project schemas, current code and output space leave insufficient room. Its current request still determines file permissions; an old deployment instruction cannot authorize publishing the new project.

For example, after an earlier chat established “Use PostgreSQL for this project,” a later written request about that project's database can recover the decision. If the earlier discussion later changed to SQLite, Arc can include the correction with the matched context. If several projects have similar names or the correction is ambiguous, identifying the project or clarifying the decision may still be necessary.

## Sources, freshness and permissions

**Settings → Context & memory** shows **Past conversations used for this reply** when excerpts were actually included. Expand it to see conversation titles and selected-message counts. Existing source lists remain available separately. No-match, incomplete indexing and unavailable-recall states distinguish an empty result from a completed retrieval.

Remembered sources do not replace fresh research. Prices, software behavior, service availability and other changing online facts must be checked against current evidence when relevant. Old links are research leads; an earlier assistant summary is not a freshly verified answer. A saved assertion that code passed a test is not a new test result.

Historical text cannot grant permission to use tools, run commands, publish, access accounts or change files. Existing tool permissions and action checks remain in place. Current instructions and explicit corrections govern the new task; contradictory or uncertain history should be surfaced rather than silently treated as authoritative.

Automatic recall checks matches against live saved messages. The separate `search_saved_context` and `read_context_original` tools can still inspect immutable archived revisions when enabled; an exact historical original is not necessarily the latest decision. Turning off past-conversation recall also prevents those tools from searching or opening other conversation-message originals for the request. Current-chat context and separately stored file/document tools remain available.

## Storage and limits

- Recall searches local saved text. It does not send the archive to a new service, enable BrainAPI or create a new remote memory account. Existing separately enabled research or remote-memory features keep their own controls.
- The existing conversation and context databases are local disk storage. This feature does not add encryption to those databases. Manual notes and automatic personal facts retain their separate encrypted storage behavior.
- Automatic cross-chat recall is used in written text chat, not the live voice or direct image-analysis path. Temporary voice sessions are excluded. Raw screen/audio buffers are not independently indexed; text explicitly saved in a conversation can later be recalled like other saved chat text.
- Deleting a conversation excludes it from future recall, including stale index matches. This is not forensic erasure of device backups, exported files, or text already preserved in another saved answer.
- Retrieval is a bounded relevant subset. A large SSD archive does not enlarge the model's physical context window, guarantee exact recall, or allow every previous conversation to be understood at once.
- Indexing can still be in progress after launch. Missing semantic support, search failures, insufficient prompt room or a weak match can leave a reply without earlier excerpts. Arc can continue with current context and report that limitation.
- Short or ambiguous follow-ups may not identify the intended old project. Name the project, file or decision, or clarify which conversation you mean. Broad greetings and bare “continue” requests do not need a general cross-chat search.
- CPU indexing and disk reads have a cost, and retrieved text uses some prompt space. This release makes no new claims about token speed, VRAM savings or real-model recall accuracy.

## Implementation and verification

`conversation-recall.cjs` selects and validates historical excerpts. `context-manager.cjs` combines them with the existing archive and context budgets, and preserves provenance only for included evidence. The main process applies the persisted preference, saved-memory master setting, written-chat boundaries and request-level exclusions. The Memories and Context interfaces show the preference, pause states and selected sources.

Automated coverage uses synthetic conversations and isolated storage for matching, corrections, stale/deleted messages, budgets, settings migration and persisted opt-out. UI fixtures exercise keyboard use, pending and failed saves, master-off/unavailable states, manual-draft preservation and source reporting at desktop and mobile widths. These checks establish integration behavior; they do not measure real-model factual accuracy or guarantee that every question retrieves the intended prior conversation. No new runtime dependency is added.
