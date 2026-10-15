# Arc version history — cumulative release 0.51.75

Published October 15, 2026. Changes since public 0.51.71: 0.51.72–0.51.75. Later versions supersede earlier behavior, including the retired Reddit interval queue. Test counts retain their original scope and overlap.

[Feature overview](README.md) · [Release notes](releases/0.51.75.md)

## Arc 0.51.75 — email, Reddit and timed-work guides

This Windows alpha release includes 360 substantial new guides: 120 for email assistance, 120 for Reddit posting and 120 for timed work. It also brings the changes from 0.51.72–0.51.74 to the public download channel.

### New guide library

- Find the guides in Settings → Plugins under Guides. Each includes preparation, ordered steps, a worked example, verification, recovery and completion criteria.
- Guides cover email categories, reply tone, sender filters, inbox progress and duplicate-send recovery; Reddit drafts, exact approval, browser state and one-time scheduling; and clock, timezone, reminders, missed times and task handoffs.
- Automatic selection sends a compact task-relevant core to the model. Full instructions are available through the existing paged reader instead of filling the context window with the entire library.
- Fixed guide selection so pause, cancel and status requests do not select Reddit post-creation guidance. Timed-work guidance requires a matching timed intent.
- The bundled catalog now contains 21,123 workflows, including 20,887 authored guides and 236 retained upstream workflows. These are instructions for existing capabilities, not 360 new executable tools.
- No new dependencies or model downloads.

### Also new since public release 0.51.71

**Saved-chat recall (0.51.72).** Written-chat requests can retrieve a bounded selection of relevant earlier conversations and recognized corrections. Settings → Memories includes Use past conversations; a request can use this chat only. Retrieval uses local CPU indexing, not another GPU model or training. Current instructions take priority and changing online facts still need fresh sources.

**One approved Reddit post, now or later (0.51.73).** Review the exact account, community, title, body and resolved time in Settings → Reddit posts. Approve once in advance, then Arc can submit that one post through the connected browser while open and awake. This replaces interval batches. Changed drafts require new approval; missed times and uncertain submissions require review instead of duplicate retries.

**Questionnaire-based email assistance (0.51.74).** Settings → Email assistant lets the user choose the observed mailbox, email categories, reply instructions/examples, sender filters and pace. One review starts one inbox pass. Messages are handled sequentially with fresh local-model context. Recipient/body checks, delivery records, pause/stop and uncertain-send handling help preserve control.

### Scope and limits

- Email adapters initially support fixed English, paginated Gmail, Outlook and Proton layouts. Unknown layouts stop for review. Synthetic mailbox tests do not establish compatibility with every real account; live sending still needs a supervised provider trial.
- Reddit scheduling is one explicitly approved text post. Arc must remain running and awake. Login and CAPTCHA remain manual. There is no interval posting or bulk-posting capability in this release.
- Other timed-work guides support reminders, planning and handoff to available tools. They do not add a general background scheduler for research, file creation or recurring email polling.
- Guides cannot grant permissions, expand model context, promise task completion or override tool checks. No real email or Reddit post was sent during automated verification.
- Windows alpha; the installer is not Authenticode-signed. Signed updater metadata and download hashes are separate integrity checks. No new VirusTotal result is claimed.

### Validation

The new library passed catalog integrity and reproducibility checks, routing tests for all 360 natural-language examples, full-reference hash/paging checks, and focused regression tests. The earlier email implementation passed 3,791 tests with 10 skips and zero failures. This release additionally runs the production UI build and packaged runtime checks, including the real reader over all bundled workflow references. These checks do not constitute live-account testing or a new model speed/quality benchmark.

See [email setup](releases/0.51.75/EMAIL_ASSISTANT.md), [Reddit setup](releases/0.51.75/REDDIT_POSTS.md), [saved-chat recall](releases/0.51.75/CONVERSATION_RECALL.md), and the [360-guide index](releases/0.51.75/EMAIL_REDDIT_TIMED_GUIDES.md).


## Arc 0.51.74 — questionnaire-based email assistance

- Added Settings → Email assistant and a Plugins entry for browser-connected Gmail, Outlook and Proton Mail adapters.
- Added a questionnaire for the observed mailbox, nine optional categories, per-category instructions/examples, general tone, allowed/excluded senders and a 30–3,600-second pace. One explicit review starts one inbox pass.
- Added sequential local-model classification/reply drafting with a fresh context per message, no model tools, no cross-email history and bounded input/output.
- Added persistent delivery identities, atomic submit-intent recording, exact recipient/body checks and sent-message verification. Uncertain attempts are held without automatic retries; restart requires review.
- Added current-inbox progress, pause/stop, competing-work gates, login/CAPTCHA handling and existing-draft preservation.
- Added four detailed email/Reddit workflows, including timed background posts and duplicate recovery. Chat can check or pause email work, but cannot approve a run.
- No new dependencies. Existing Reddit posting approval remains unchanged.

Initial browser compatibility uses fixed English DOM patterns and supported paginated inbox views. Unknown layouts fail closed. Tests use synthetic mailboxes; no real email was sent. Live account compatibility and delivery need a supervised trial on the user's actual provider. See [Email assistant](releases/0.51.75/EMAIL_ASSISTANT.md) for setup, retained state and limits.


## Arc 0.51.73 — One approved Reddit post, now or later

Arc can prepare a single Reddit text post and submit it once at a reviewed date and time. Requests such as “make this post in three hours” save a concrete time proposal beside the draft. In **Settings → Reddit posts**, review the full title and body, account, community, timezone and time, then approve in advance. **Post once now** is also available.

### Changes

- Manual title/body entry alongside one-at-a-time drafting with the selected local model.
- One active post, replacing interval batches. Older batch queues are preserved paused for cancel/review; they cannot resume as batches.
- An application timer reads the system clock without keeping a model generating. Valid future schedules survive restart; missed times require fresh approval. Windows lock or suspend pauses work. Arc must remain open and awake.
- Relative delays resolve once when saved. Exact local date/time review handles timezone offsets, daylight-saving gaps and repeated local times. Edits invalidate review; stale draft revisions cannot authorize changed text.
- The connected personal browser is searched for an accessible Reddit tab. Dedicated background selection and creation avoid foreground activation. An unfinished editor is preserved. Initial browser launch, connection permission or sign-in can still require the user.
- Private windows are usable only when exposed by the authorized browser connection. No cookie/password copying or silent profile fallback.
- Reddit editor checks recognize ordinary paragraph and blank-line structure while verifying exact submitted text. User-written mentions of CAPTCHA do not masquerade as a site challenge; actual visible verification controls still stop automation.
- Fresh account, community, content and timing checks precede one durable submit boundary. Lost or ambiguous receipts require reconciliation rather than an automatic retry.
- Updated Plugins explanation and a substantial task guide describe proposal, review, background execution, timing limits and recovery.

### Limits and verification

There is no CAPTCHA bypass, recurring posting or multi-post queue. The feature supports text posts in recognized editors, not every possible Reddit layout or community requirement. An existing connected window stays in the background; the operating system or browser may display first-launch and permission UI.

Tests use synthetic clocks/state and controlled local browser fixtures. They exercise advance approval, exactly one submission, cancelled or changed approvals, timezone handling, restart/missed schedules, wrong accounts, real challenge detection, editor readback, background tab behavior, duplicate prevention and uncertain receipts. Desktop and mobile review layouts are checked without a live account. The packaged application verifies the actual saved timer/approval/receipt service offline.

No real Reddit post was published by these tests. Live account and community compatibility remain to be tested under user supervision. A verified receipt establishes the matching visible post at that time, not permanent acceptance by moderators.

See [setup, behavior and limits](releases/0.51.75/REDDIT_POSTS.md).


## Arc 0.51.72 — relevant recall across saved chats

New written-chat requests can recover relevant excerpts from earlier saved conversations, including project work and recognized later corrections. Arc searches its local archive, checks selected messages against their current saved originals, and fits a bounded selection into the working context. Deleted or changed messages are excluded from stale results.

**Settings → Memories → Use past conversations** is on by default and saves immediately. The existing **Use saved memories** master switch must also be saved as on; turning it off pauses local recall without clearing saved chats or the separate preference. Explicit opt-out survives restarts and older settings saves. A direct instruction such as “Use this chat only” excludes past conversations for that request.

Context settings now show the titles and selected-message counts of past chats actually included in the reply. Existing source lists remain intact. The interface distinguishes no matching context, ongoing indexing and unavailable recall, and preserves unsaved manual notes when the new preference changes.

Current instructions take priority. Old assistant replies are historical claims, not verified current facts, successful-action receipts or permission to act. Questions involving changing online information still need fresh research. Ambiguous project references or weak matches may require clarification; a saved archive is not a guarantee of perfect memory.

Indexing uses the existing local CPU search facilities. This feature adds no dependency, GPU model or model training, and does not enlarge the selected context window. Only a relevant subset of saved text is loaded. Temporary voice sessions and raw screen/audio buffers are excluded. Text explicitly saved in a conversation can later be recalled like other saved chat text. Automatic cross-chat recall is used in written text chat; the live voice and direct image-analysis paths retain their existing context behavior. Existing conversation/context storage remains local; this change does not add encryption to those databases or alter the separate encrypted manual/personal-memory stores.

Verification: 483 tests passed across the relevant context, recall, memory, settings, project-writing, research/browser/tool and main-process boundary suites, with one existing Windows symlink-privilege skip. An older theme test fixture was updated for the new setting and its failed assertion passed on rerun. Tests include actual bundled CPU retrieval of a cross-chat paraphrase without shared indexed words, live-message validation, topic-specific corrections, atomic correction/excerpt budgeting, cancellation, deletion, privacy controls and unchanged tool permissions. Project-writing checks cover recalled preferences, output-space reserves and recovery that removes optional history when the backend rejects the context size. TypeScript and the production UI build passed. New recall and existing personal-memory component checks passed at 1280px and 390px, covering keyboard access, save races and failures, pause states, preserved drafts and source reporting without external requests. The responsive screenshots were visually inspected.

These checks use synthetic data and isolated fixtures. They validate persistence and interface behavior, not real-model recall quality, inference performance or the correctness of every remembered answer. See [conversation recall behavior and limits](releases/0.51.75/CONVERSATION_RECALL.md).


## Arc 0.51.71 — coordinated helper teams

Hard tasks can split independent preparation and review into focused helper assignments. The existing local delegation engine now allows up to 24 helpers over one parent turn, including failed and cancelled helpers. At most five helpers are admitted at once; additional helpers wait in a queue. Queued time does not consume a helper's execution deadline.

All helpers share the selected local model and its existing GPU queue. Arc does not load 24 copies of the model. Read-only tools can overlap, but inference takes turns; more agents may increase completion time. Helper conversations and reports use bounded working memory and local storage rather than accumulating all transcripts in the parent context.

The intended task cycle is understand the request, gather relevant evidence, perform the authorized work, review actual results, repair supported issues, and return the result with its checks and remaining limitations. Helpers research, propose and review; the main agent retains file writes, browser actions and supported sandbox checks. A completed helper report is not a test receipt or proof that the overall task is complete.

The existing General Settings helper toggle still controls delegation. The task panel shows queued, working and completed helpers, including later review batches. Existing three-helper snapshots remain valid so earlier conversation files and result cards are preserved.

No new model, runtime or dependency is installed by this feature. The 24-slot limit is an orchestration limit, not a promise of 24-way inference or a measured speed improvement.

Verification for this change: 505 targeted Node regression tests passed across the parent tool loop, project and extension writer, helper engine, browser/SSH/PC tool boundaries, saved artifacts, context compaction and runtime queues. Four native Python protocol tests passed for helper planning and exact-path project review. The UI check passed at desktop, tablet and 390px widths with 24 helpers and no renderer errors. TypeScript and the production UI build passed; the workflow catalog passed its integrity and reproducibility check.

These checks use scripted model responses and isolated fixtures. They establish orchestration, validation and persistence behavior; they do not measure real-model answer quality, VRAM usage or task speed. Existing permission boundaries and checks remain responsible for actual actions and execution.

## Arc 0.51.70 — clearer project planning

A real Strata extension-generation test exposed a planning failure: popup HTML linked its script, while the script referred back to the HTML's elements. The model treated both directions as file-writing prerequisites and repeated the circular plan. Arc rejected it before creating incomplete source files.

Project planning now explains that `dependsOn` means a file must be written first, not that two finished files refer to each other at runtime. Shared IDs, selectors and interfaces belong in the plan's shared conventions and file contracts. Extension planning includes a concrete acyclic ordering example. Unknown dependencies and circular plans receive distinct, specific correction guidance; invalid edges are never silently removed.

The complete extension-folder workflow from [0.51.69](releases/0.51.69.md) remains in place, including exact filenames, every-file completion, static Manifest V3/resource checks, bounded continuation and saved checkpoints. No new dependencies or model downloads are required.

Validation passed 139 focused regression tests. A real installed Strata Q2_0 model generated a neutral four-file word-counter extension through Arc's production project workflow: 32.1 seconds for generation and project work after a 40.5-second model load. Exported file hashes matched the saved project. Chromium 153 loaded the extension and passed all five word-count checks, with no page errors or external requests. These are one-machine measurements, not a general performance guarantee.

Verification covers neutral local popup behavior. This does not establish that every model-generated extension works on every website. Generated source and requested permissions still require review before installation.

## Arc 0.51.69 — complete browser-extension source projects

New browser-extension requests now use Arc's existing sequential project builder. Previously a manifest could be separated from HTML/JavaScript, and one saved file could satisfy the ordinary file-completion check even when the extension needed several files.

- Preserve `manifest.json` at the root and exact relative resource paths in one exported directory. Repeated builds get separate project folders instead of renaming required source files.
- Save every planned file before reporting the project complete. Up to 12 files can continue in ordinary text chat within 32 model rounds. Larger work and longer corrections retain Ultra → Until done, cancellation, hashes and saved checkpoints.
- Check basic Manifest V3 metadata, declared local resources, missing popup/scripts and common extension-page CSP errors. Failed checks trigger bounded repairs or retain an incomplete result.
- Keep assessment requests, explicit single-file requests, and live inspection/installation tasks distinct. Describing future extension behavior does not operate the user's browser or accounts.
- Record project tool names and fixed failure codes in local diagnostics without saving source code, page content or URLs.

Open **Code → Local website workspace**, choose the saved project, and use **Export & open folder**. Review its source and requested permissions before manually loading it through the browser's extension developer controls.

Validation includes 137 relevant automated checks covering routing, output-only write restrictions, multi-file export, pending resource references, repair, resumable limits, syntax and diagnostics. Browser behavior checks use a benign local popup fixture in a fresh Chromium profile; fixture-driven checks are not evidence of a particular model's coding reliability. There is no automatic installation, publishing, arbitrary-code execution or new dependency/model download. Static checks are not a complete Chrome manifest or security audit, and do not establish compatibility with arbitrary websites.

See [the source-project workflow](releases/0.51.71/LOCAL_TOOL_WORKSHOP.md) and [Chrome's manifest reference](https://developer.chrome.com/docs/extensions/reference/manifest).

## Arc 0.51.68 — tool assessment and creation routing

Arc now distinguishes inspecting an idea, writing browser-extension source files, and publishing an isolated reusable helper.

- An inspection followed by “tell me if you can build this” produces a feasibility report instead of being replaced by a missing-publication warning.
- Explicitly requested assessment reports can still be saved as files.
- Browser-extension projects use the normal source-file tools. Arc's JSON-helper sandbox cannot install an extension or access the browser DOM.
- Descriptions of a proposed extension's future behavior do not authorize typing or submitting account actions during inspection or source creation.
- An explicit later implementation instruction can request source creation. A source-file receipt is not proof that the extension was installed or tested in a browser.
- Local-helper failures identify whether authoring was unavailable, never attempted, or unsuccessful during validation/tests. Existing working revisions remain intact.

Validation: 149 automated tests passed, including real isolated helper execution and controlled browser fixtures. These checks do not establish live model reliability or compatibility with a particular website. No new dependency or model download is required.

This release retains the compact personal memories added in 0.51.67. See [the local workshop guide](releases/0.51.71/LOCAL_TOOL_WORKSHOP.md) for supported helpers, source files, assessment requests and execution boundaries.

## Arc 0.51.67 — compact personal memories

Arc can now remember clear personal facts from new direct messages and reuse relevant details in future replies. Find the automatic list and its switch in **Settings → Memories → Details you’ve shared**.

- Short personal facts save locally in encrypted storage, separate from exact manual notes.
- Duplicates are ignored; clear positive corrections update the existing category. Users can edit, remove or clear facts. User edits are protected against automatic replacement.
- No extra inference model or GPU memory is needed. Stored facts have a 768-token budget; each reply receives at most a 256-token relevant pack, only when context space permits.
- Temporary voice, shared screen/audio, attachments, tool output and generated prompt templates are excluded from capture. Saved direct microphone questions can contribute facts.
- Sensitive or ambiguous statements are skipped by the conservative English recognizer. Unsupported phrasing remains available for manual notes.
- Turning off automatic capture or the saved-memory master switch pauses capture and recall. Queued captures cannot undo a later clear/disable action.

See [automatic memory behavior and limits](releases/0.51.71/PERSONAL_MEMORIES.md). This update retains the Reddit draft/scheduling features from 0.51.66. No new dependency or model download is required.

## Arc 0.51.66 — Reddit drafts and timed posting

This beta adds **Settings → Reddit posts**, also accessible from Plugins. Enter a subreddit and topic, check the signed-in browser account, and generate up to ten editable title/body drafts with the currently selected local model. Draft generation keeps the selected model and context; no extra model or package is required.

The chat tool can find the Reddit destination, read the clock, save drafts and stop a queue. The model cannot approve its own writing. After reviewing the exact drafts, account and community rules in settings, the user can enable a finite queue at **15, 30 or 60 minute intervals**. The first post waits one full interval. Each post includes a visible automation disclosure before review. Drafting guidance calls for natural, useful prose without fabricated personal experiences or sources.

The browser adapter uses Arc's existing selected-browser connection and an observed Reddit text editor. It verifies the signed-in account, destination and exact live editor contents immediately before submission. It records submission intent on disk before one click, then checks the visible post and resulting permalink. Unknown outcomes pause for manual reconciliation instead of automatic retry. Login, CAPTCHA, required flair, restrictions, rate limits and unsupported layouts can stop a post without bypassing them. Other tabs and existing drafts are preserved.

Approved contents are revision checked. Editing a queued draft revokes its approval. Queue progress and receipts persist locally. Restart, Windows lock and sleep pause the queue; resuming waits a fresh interval and does not send a catch-up burst. Foreground inference, voice and browser work take priority. The queue stops when its reviewed batch ends. Pause and Cancel do not delete already published Reddit posts.

The new `read_clock` tool returns current local time, timezone and UTC. It does not grant scheduling permissions or change the system clock. A bundled workflow explains drafting, review, timing, evidence and recovery.

## Validation and remaining live check

The service and browser tests cover timer behavior, exact approvals, duplicate content, account changes, cancellation races, restart recovery, write failures and ambiguous submissions. The browser fixture uses the real Playwright controller against locally intercepted Reddit-shaped pages; it does not send a public post or access a personal browser profile. Responsive settings checks cover desktop and narrow layouts.

Actual Reddit compatibility still needs the planned test on the owner's subreddit. This is a conservative text-post integration, not a guarantee that every Reddit layout, flair requirement or account restriction is supported.

---

## Prior publication: cumulative release 0.51.65

Published October 12, 2026. This Windows x64 update includes **0.51.61–0.51.65** since public 0.51.60.

[Current feature overview](README.md) · [Release notes](releases/0.51.65.md) · [Measured workloads](docs/VRAM_WORKLOAD_REPORT_0.51.65.md)

Development notes below are newest first. Later versions supersede earlier defaults. Measurements apply only to their stated hardware and test scope. Guides provide instructions; they are not model training.

## Arc 0.51.65 — Measured memory tiers and task-output fixes

This release brings four optional lower-VRAM tiers, output continuity and multi-file website work to the public download, together with corrections found during real Arc Focus workload testing.

## Screenshot recovery

Some pages expose an accessibility landmark that contains useful text but has no painted box, such as MDN's `display: contents` main region. Arc now checks a selected screenshot area's visibility and dimensions before capturing. A layout-only or missing target returns a concrete instruction to select a visible child or request a page viewport, instead of repeatedly waiting on the same uncapturable element. Explicit area captures are not silently widened to include unrelated content.

Screenshot tools also accept valid current-page references beyond the first 100 observed elements. The reference must still match the strict format and current page. If a fully completed tool response contains arguments that fail the action schema, Arc can report the correction while keeping the healthy text worker loaded. Broken or unfinished tool streams still release the failed worker.

Combined research-and-screenshot requests retain search and source-reading tools for a bounded follow-through phase after saving the image. Successfully retrieved page text counts as a source read; search cards remain leads, and indexed fallback text is distinguished from a live page read. Missing requested source work is reported as incomplete while keeping saved screenshots attached. Multiple URLs do not necessarily represent independent publishers or establish factual accuracy.

Screenshot guidance prefers a viewport or visible-element crop for ordinary text, while honoring explicitly requested full-page captures. Vision is instructed to identify unreadable or cropped text and avoid substituting familiar examples for pixels it cannot read. These instructions reduce avoidable ambiguity; actual screenshot and answer accuracy are reviewed separately in the workload report.

For visual research, an untargeted screenshot uses the actual viewport unless the original user explicitly asked for full/whole/entire-page or top-to-bottom coverage. The receipt and notice describe the viewport and suggest scrolling for further sections. This prevents a model-selected full-page flag from needlessly shrinking a very tall document into unreadable pixels. Explicit full-page requests, targeted captures and standalone screenshot saving keep their existing scope; full-page vision can still have legibility limits.

A guided diagnostic also found that selecting the top-level `body` or `html` element with `fullPage: false` could capture an entire tall document. Root-document requests now use the actual viewport and retain that coverage in screenshot receipts. Oversized non-root elements and iframe targets return guidance to choose a smaller visible region instead of silently changing the requested scope. The focused capture suite passed. All five final screenshot phases delivered readable viewport images, actual vision calls and substantive answers. The workload report separately records remaining factual and citation problems; readable pixels and successful tools are not a guarantee of an accurate explanation.

Replies name the verified saved screenshot and retain its image card instead of substituting a webpage URL or an invented local filename for the delivered image. Requested vision observations are associated with the saved image ID and its recorded source page, with unreadability caveats preserved. Up to 20 observations remain bounded; unrelated image URLs and code examples are unchanged.

A later Tier 2 diagnostic found that screenshot reply cleanup could discard a substantive researched explanation merely because it opened with “I'll research.” Cleanup now removes plan-only narration while preserving the substantive body, citations, mixed code, quoted text and fenced lines. The focused suite passed 63 tests, and replaying the actual failed answer retained its substantive remainder. The original delivery failure remains in the diagnostic record. Final comparisons use the repaired source in every mode and distinguish successful tool calls from the delivered explanation.

Another diagnostic completed search, source reads, a readable screenshot and vision but returned only a plan with printed navigation calls. Final response instructions now close the tool phase and retain the combined research obligation. A confidently plan-only answer gets one bounded evidence-only composition retry that cannot run tools or repeat captures. A persistent plan remains incomplete while preserving real images and source receipts. Actual explanations, code, quoted plans and user-requested plans are preserved; image evidence fits a context-sensitive whole-payload budget. The focused repair suite passed 84 tests. This is completion handling, not automatic factual grading or guaranteed model accuracy; final live outcomes are reviewed separately.

## File boundaries and unavailable sources

For newly authored, line-counted JavaScript, Arc checks actual saved line counts across file sections. A missing separator between two otherwise complete sections can be inserted only when both sections parse as complete statements with safe semicolon boundaries and no open comment or literal. Exact-copy requests bypass this repair. Accepted bytes are not saved again during a retry. This addresses a measured case where 1,000 declarations were present but adjoining sections left fewer than 1,000 physical lines.

A later Tier 3 test exposed a different failure: the first 100-line section was replayed, producing 1,000 physical lines but only 900 unique declarations. A narrow replay guard now verifies the current file ID/hash and the same raw section plus unchanged nonempty checkpoint, then returns the existing receipt and exact file tail without appending duplicate bytes or advancing the chunk count. Intentional repetition with an advancing checkpoint and exact-copy requests is preserved. The focused suite passed 63 tests, including the actual failure shape. Final live file outcomes remain separate from syntax-only checks.

When a direct source read returns HTTP 403 or 429, Arc keeps that observation in trusted per-turn guidance through context compaction. It can choose another publisher or an already authorized normal browser path. This does not bypass a website's access restrictions or establish that the alternative source was successfully read. The workload report records the live retest outcomes separately.

The later readable-capture test exposed another path: automatic reads after search could retry three pages on the same denied host before the model had a chance to recover. Automatic selection now skips hosts already observed as denied during that turn. When the available lead pool is blocked, Arc may make one alternate-source search using the original query with explicit denied-site exclusions. User-required access attempts remain distinct. Focused tests passed; final live outcomes are recorded separately, and this bounded retry does not guarantee access or bypass restrictions.

A subsequent diagnostic completed the requested source reads but repeated a guessed unavailable URL instead of answering. Arc now retains exact failed-URL guidance and permits a narrowly scoped no-tools synthesis when a pure web-research contract is already fulfilled. Focused recovery tests passed. The failure ledger and incomplete-task checks remain; a second source receipt alone is not reported as a completed user-facing answer. Final live task results are recorded separately.

Compacted research evidence now retains bounded, query-relevant exact passages and nearby qualifications from retrieved pages, instead of relying on a fixed prefix that may contain mostly navigation. Omission notices identify what the excerpt leaves out. Retrieved page text remains untrusted source material rather than tool instructions; retaining it does not establish that the model interpreted every qualification correctly.

## GPU memory choices

**Settings → AI Performance → Lower VRAM usage** offers Tier 1 · Light, Tier 2 · Balanced, Tier 3 · Maximum and Tier 4 · Extreme. The limits are 20%, 35%, 50% and 75% lower generation throughput in the short selection benchmark; the same prefill limits apply when normal prefill timing is available. First-token delay is checked when both timings are available. They are eligibility limits, not an intentional delay or a guarantee for every task. Missing timing is not a successful latency comparison. The 75% speed tolerance does not promise 75% less VRAM.

Arc tests smaller processing buffers, supported cache precision and eligible CPU-layer placement. It keeps the selected model and context, respects the verified llama.cpp text worker's RAM allowance, rejects unstable trials, and reuses measured results. Normal mode restores the normal tested configuration. Strata retains its separate engine and upstream low-RAM policy.

Tier 4 adds at most three combinations of supported Q4 K/V cache and selective CPU placement to the earlier tests. It can reconsider healthy earlier trials against its more tolerant speed limit, while crashes, out-of-memory results and unhealthy configurations remain excluded. Existing Tier 1–3 candidate identities stay stable for safe cache reuse.

## Long output and saved work

- Ultra **Until done** writes bounded sections with revision-checked files, exact saved endings and disk checkpoints.
- Multi-file website projects preserve source, a shared plan and dependencies, then build one file at a time. Local syntax/link checks and directory export are available.
- File History retains verified versions so Arc can find and restore earlier work when the working file is lost.
- A finite native context window still applies. Disk records are retrieved as needed; they do not create unlimited active context or guarantee arbitrary output length.

## Six guides for completing the actual task

Six substantial workflows cover verified multi-source research, readable screenshots and vision, combined research/image requests, chunked file completion, debug/verify/save work, and resuming from evidence. The full references contain 8,100 words in total, with 1,310–1,390 words each. Find them by name in **Settings → Plugins**; the guide index describes each one.

Automatic retrieval uses a complete compact task/check/stop recipe within the existing context budget. Each serialized single-guide selection is 1,811–1,888 characters including the shared notice, identity and formatting; the recipe body is smaller. The longer reference is available when needed. Browser screenshots no longer imply a live screen-sharing session during routing. The guides use existing tools and permissions; they do not add executable backends or prove a general increase in intelligence or accuracy. Newly bundled entries preserve users' saved off switches.

## Release verification

The latest completed full automated regression suite reports 3,458 tests: 3,453 passed, 0 failed and 5 skipped, in 556.146 seconds. It predates the subsequent finalization repair. After that repair, 478 tests across 27 affected test files passed with no skips in 11.134 seconds. The overlapping full, affected-area and focused counts are not added together. An earlier concurrent run's crawl timeout and successful isolated rerun remain documented separately. Automated checks are not a general model-accuracy score or final installer verification.

The completed Normal/Tier 1/Tier 2/Tier 3/Tier 4 matrix used the same 20-file implementation fingerprint, real Arc Focus inference, public-web research, browser screenshots and local vision, a 1,000-line JavaScript file, post-task model residency, and the actual bundled guide path. All five modes delivered the requested research answers and readable images, passed the exact 38,785-byte/1,000-unique-line file check and independent syntax parse, and reused the prepared model after the task. Earlier diagnostic failures and recovered errors remain recorded. Manual review found remaining factual, source-independence and citation limitations; successful delivery is not a perfect accuracy score.

On this RTX 5090 desktop, Normal reached a median 187.28 native generation tokens/second with a raw whole-GPU generation peak of 8,278 MiB. Tier 3 reached 182.09 tokens/second with a 7,490 MiB peak: 788 MiB lower and 2.77% less throughput. Tier 4 reached 52.67 tokens/second with a 7,432 MiB peak: 846 MiB lower and 71.87% less throughput. Its 1,000-line file took 611.56 seconds versus Normal's 121.59 seconds, including two recovered model-call failures and an unusually slow call. That whole-task difference cannot be attributed entirely to CPU placement. Pre-load readings, separate vision/overall peaks, all other tiers and limitations are in the report; these raw GPU differences are not precise model-only allocations or a guarantee for smaller cards.

A separate screenshot-component UI check rendered three receipt-based image cards and verified expand/shrink without page errors or horizontal overflow at 1,100-pixel and 390-pixel widths. This component fixture check is separate from the live browser/vision matrix and a full Electron end-to-end test. Installer identity and package-source checks are recorded outside this bundled document with the final release checksum.

See the detailed workload report and AI Performance architecture for measured results, implementation details and limits. See the previous tier release notes for the initial short calibration measurements.

No new model download or application dependency is required. The selected chat model remains resident after work; auxiliary vision may temporarily release and restore it. The Windows installer is not Authenticode signed. Older VirusTotal scans do not apply to this installer; use the published checksum to identify this exact build.

## Arc 0.51.64 — Three GPU memory-saving tiers

**Settings → AI Performance → Lower VRAM usage** now offers three levels. Tier 1 accepts up to 20% lower measured generation/prompt speed, Tier 2 up to 35%, and Tier 3 up to 50%. The switch defaults off; Tier 1 is the initial choice when enabled.

Arc benchmarks the selected model with smaller prompt buffers and supported Q8/mixed Q8-Q4 caches. Tier 2 also tests a small number of model blocks in system RAM. Tier 3 tries more CPU blocks and a Q4 K/V cache. When the verified engine supports tensor placement, Arc keeps the output projection on its normal GPU path; otherwise it tests conventional partial GPU offload. Layer counts come from bounded GGUF metadata reads, never from a GPU-name guess.

The existing real-inference benchmark decides which settings qualify. All five basic response checks must pass, generation and prompt throughput must satisfy the chosen tier, and first-token delay is checked. When GPU telemetry is available, savings must be observed. Among memory results within 128 MiB, the existing performance/quality score breaks the tie, so a tiny whole-device fluctuation does not justify a large slowdown. Short tests are not a guarantee of speed or answer quality for every workload.

- Keep model weights, context window and the default 32 GiB RAM ceiling. Partial offload gets a conservative extra RAM allowance within that same ceiling.
- Keep the chosen model resident using Arc's existing residency service. The comparison temporarily reloads models; there is no new idle-unload timer.
- Reuse completed measurements when changing tiers or restarting. A speed rejection can qualify at a more tolerant tier; crashes/OOM and unhealthy configurations remain excluded.
- Cancel a running comparison and restore the previous preference/profile. Turning the switch off restores the normal tested baseline.
- Keep CPU-only models and Strata on their existing engines. No new model download, runtime dependency or automatic context reduction.

More VRAM saving is not proportional to slowdown. On a fast GPU, transferring computation to the CPU can exceed a 50% speed loss very quickly. Arc retains a faster compatible option when that happens. Measurement reports and repeatable native benchmarking are under `work/qa/low-vram-*` and `scripts/benchmark-low-vram.cjs`.

## Local measurements (RTX 5090, Arc Focus, 16,384 context)

The final isolated sweep used a 32 GiB RAM ceiling and real native generation. These are whole-device GPU peaks (other apps contribute), not per-model allocation claims.

| Selected mode | GPU peak | Reply speed | Observed saving vs normal |
| --- | --- | --- | --- |
| Normal | 8,251 MiB | 188.45 tokens/s | — |
| Tier 1 | 7,411 MiB | 184.32 tokens/s | 840 MiB |
| Tier 2 | 7,411 MiB | 184.32 tokens/s | 840 MiB |
| Tier 3 | 7,285 MiB | 191.15 tokens/s | 966 MiB |

All selected configurations passed 5/5 basic response checks. Tier 3 used a Q4 K/V cache and was slightly faster in this short run; the tier's 50% limit is a maximum acceptable loss, not an intentional delay. The mixed Q8/Q4 alternative was much slower for essentially the same memory use, so the tie-break retained the faster configuration.

Selective offload of two blocks reached 82.49 tokens/s, four blocks 52.09, and eight blocks 28.13, versus 188.45 normal. Their greater savings exceeded the 50% slowdown limit and were rejected. One CPU block qualified at Tier 3 but saved less than the chosen cache-only option. These results do not demonstrate large additional savings for Focus on this GPU. Other GPU/CPU/model combinations need their own measurements.

Evidence: `work/qa/low-vram-2026-10-12T21-10-30-239Z/report.json`. No installed-user settings or model files were modified by this test.

## Arc 0.51.63 — Lower VRAM usage

An optional **Lower VRAM usage** switch is now available in **Settings → AI Performance**, above Advanced choices. Arc tests smaller processing buffers and supported cache compression on the selected model, then keeps a lower-memory configuration only when the real inference checks pass.

- Preserve the selected model, its context window, the default 32 GiB RAM ceiling and existing model residency.
- Compare normal settings with smaller prompt batches and supported Q8 or mixed Q8/Q4 caches with Flash Attention.
- Reject failed configurations, basic response-check failures, and more than 20% generation/prefill slowdown; check first-token latency as well.
- Display actual whole-device GPU savings where measured. Unknown telemetry stays unmeasured.
- Reuse cached measurements on later launches. Turning the switch off restores the tested normal baseline.
- Support cancellation with cleanup and retain existing fallback handling if a selected engine later fails.
- Keep Strata on its own engine; CPU and incompatible engines keep normal settings.

On the development RTX 5090, a short Arc Focus comparison at 16K context observed **840 MiB less whole-device GPU memory** and **186.25 vs 189.06 tokens/s** (about 1.5% slower). All five basic response checks passed. Other GPU applications can affect these measurements; this is not a universal savings or quality guarantee.

No new dependencies, model downloads, automatic context reductions or system changes. This release reduces supported cache/workspace allocations; it does not compress all VRAM or add unlimited context. Existing workflows, voice, vision and Strata loaders are retained.

## Arc 0.51.62 — Multi-file website projects

Arc can build a complete local website as separate pages, styles, scripts and nested folders. Ultra's Until done mode writes one file at a time, saving source, a shared plan and progress on disk. Completed file bodies stay outside the next model prompt; relevant source can be read back when needed.

- Resume an unfinished project after stopping or restarting Arc, with source-revision checks before continuing.
- Save large files in sections, preserve original requirements and dependency contracts, and reject duplicate source sections.
- Check supported syntax and local HTML/CSS/JS links, with bounded targeted repairs before reporting completion.
- Export the whole project directory through **Code → Local website workspace → Export & open folder**, with per-file hash verification.
- Show completed-file counts and the next file in the workspace and saved-task panel.
- Register the project tools with the native model worker and allow valid code blocks within the existing token budget.
- Preserve existing File History, single-file tools, protected PHP preview and reviewed SSH publishing.

No new dependencies or model downloads. The default 32 GiB system-RAM policy and the selected context window remain unchanged. Projects retain explicit size and work limits; this is incremental generation using saved files, not unlimited context. Source creation never executes or publishes generated code. See Multi-file website builds for usage and limits.

## Arc 0.51.61 — Long output and saved task continuity

Long files can be written in smaller verified sections, reducing the need to fit the entire output into one model response. Small working windows get smaller sections and output reserves. Ultra's Until done mode preserves progress and retries repeated output within bounded limits.

- Persistent per-conversation task checkpoints and pinned requirements in the Context panel.
- Manual resume after restart with current-file hash verification.
- Compact file endings and declaration outlines to support continuing code without replaying its full contents.
- Checks for explicitly requested paragraph, function and JSON-entry counts; early completion claims can require more writing.
- Arc binds focused continuation calls to the saved file and revision, so small models no longer need to copy long identifiers or hashes. Explicit paragraph numbering and exact paragraph limits are checked before append.
- Repetition protection for ordinary prose files as well as stories.
- Incremental JSON validation before committing each section, plus complete-document checks at the end; complete existing files can be finalized without an extra append.
- Checked JSON record batches let small models emit ordinary valid arrays while Arc assembles the outer document, preserves exact values and enforces the requested minimum entry count.
- Longer bounded reply continuation, with honest incomplete status when recovery cannot finish.
- Relevant project-source excerpts and original-source recall to leave more working space for output.
- Foreground voice queue priority without interrupting an active generation or crossing model-switch boundaries.
- Context inspector improvements and completion-token/section reporting.
- Small-context memory-budget correction that preserves saved preferences and the reply allowance while borrowing only future-tool headroom.
- Theme compatibility fixes for recent File History, Plugins and Twitch controls.

No new runtime dependencies, model downloads or changes to the default 32 GiB system-RAM budget. Native model context limits remain enforced. This improves output continuity; it does not guarantee small-model accuracy or unlimited context. See the implementation notes for limits and testing scope.

---

## Prior publication: cumulative release 0.51.60

Published October 11, 2026. This Windows x64 update includes versions **0.51.53–0.51.60** since the previous public **0.51.52** release.

[Download 0.51.60](https://github.com/pixelgraple/arc-desktop/releases/tag/v0.51.60) · [Current feature overview](README.md) · [Detailed release notes](releases/0.51.60.md)

Development notes below are newest first. Later versions supersede earlier defaults and feature limitations. Test counts and measurements apply to their stated builds. In particular, 0.51.55 adds Twitch alerts after the earlier platform-guide release described them as future work. Guides are instructions, not additional model training or proof that a task succeeded.

## Arc 0.51.60 — File History

- Created Files now opens a searchable history with dates, file descriptions, source excerpts, version selection and exact restoration. The output folder remains one click away.
- New files, continued files, verified local-PC saves, local PHP project files and generated images are automatically backed up locally. Existing Created Files are imported after startup without loading an AI model.
- Arc can search earlier work, inspect a verified backup, and restore an explicitly requested file in chat. Results use real file receipts, not a reconstruction from model memory.
- SHA-256 verification detects damaged backups. Restoration preserves existing files by creating numbered copies. Backups are separate from conversation deletion and working-file deletion.
- Identical content is stored once. Storage is limited to 10 GiB of unique bytes and 50,000 records, with visible warnings and manual removal; no automatic deletion of old versions.
- Existing file permissions, download limits, code sandboxes and server-side SSH backup behavior remain in place. Restoring a file never executes it or publishes it to a server.

File History protects against losing a working copy on this PC. It does not replace an off-device backup, restore files deleted before their first backup, or automatically rebuild a complete multi-file project.

## Arc 0.51.59

Arc gains a searchable Browser tasks collection in Settings → Plugins: 120 detailed workflows across twelve groups, with concrete procedures, evidence checks, recovery instructions and editable example prompts. Enabled relevant excerpts use the existing context budget; the collection does not add 120 executable engines or claim to train a model.

Four new controls extend the existing browser controller:

- Locate literal rendered text with nearby context and region positions.
- Inspect a selected region as text, headings, links, labeled controls or table rows.
- Show a passive blue outline for discussion; Escape or ten seconds removes it.
- Scroll the page or a selected container, or bring an observed target into view.

The controls share the existing schema validation, browser connection, cancellation and human-verification boundaries. They do not expose arbitrary JavaScript. Region reading excludes hidden text and editable values and returns bounded, untrusted page evidence. Small-context tool results retain complete records and continuation positions; all 24 connected controller schemas remain available.

Workflows cover tabs, navigation, targeting, copying, screenshots, vision discussion, forms, posts and media, source research, structured data, reporting, usability and recovery. Screenshots and vision use the existing capture and inference paths. Instructions distinguish an actual image, a text-shot, a source URL, a draft and a verified submitted result.

Validation includes all 120 guide records and their selection/off switches; real Chromium DOM fixtures; real Playwright MCP transport in an isolated local page; browser controller, tool-agent and permission regressions; responsive React settings checks; TypeScript and production build. These checks do not benchmark a live language model or certify every external platform. Site sign-ins, browser permissions and CAPTCHA handling remain user-controlled. Bounded DOM inspection can omit canvas, shadow-root and frame content; existing frame snapshots or screenshot vision remain necessary where applicable.

No dependencies or model downloads were added. The installer is a separate update; building it does not modify a running Arc installation.

## Arc 0.51.58 — Work Areas

Arc now includes 60 work areas in Settings → Plugins, covering software, web development, research, data and mathematics, writing and learning, planning, platforms, voice and vision, professional information, and operations.

Each area includes a detailed guide, six stages with acceptance checks, prerequisites, failure handling, a concrete deliverable and an editable example prompt. The searchable interface supports group filters, guide enable switches and compact layouts. See the complete area index.

Seven new first-party utilities run through Arc's existing plugin dispatcher:

- Work-area planner: produces a pending six-stage plan and an optional JSON checkpoint file.
- Workflow checkpoint audit: identifies missing steps and evidence, stale guide versions and unreadable Created Files references.
- Research evidence audit: finds missing source links, absent excerpts, duplicate pages and declared conflicts.
- Weighted option comparison: calculates explicit 0–100 ratings against user-defined weights; incomplete options remain unranked.
- Measured-value summary: calculates count, range, sum, mean, median, percentiles and sample/population standard deviation.
- Explicit unit converter: handles compatible length, mass, time, storage, volume and absolute-temperature units.
- Text length and repetition review: measures words and paragraphs and flags repeated substantial passages, including complete Created Files up to 2 MiB.

These utilities have bounded schemas and no host shell, network, model-download or arbitrary filesystem access. Requested exports use the existing Created Files service, which preserves originals. Existing guide and tool switches remain effective; no personal guides are replaced. Relevant compact instructions stay within the existing selection budget, and full guides are read on demand.

Plans and evidence notes are not independent proof that a task was completed. The audits identify structural problems and verify artifact availability where applicable; they do not establish truth, narrative quality, source independence or successful remote actions. Platform access, speech, vision and code execution still depend on their existing supported runtimes and permissions. No model weights or inference backends change in this release.

Validation includes deterministic tool fixtures, all 60 planner/guide routes, real plugin dispatch and Created Files exports, persistence of off switches, malformed inputs, cancellation, twelve repeated-prose cases, catalog integrity and desktop/narrow React UI interaction checks. These checks are not a live benchmark of model intelligence or a claim that every external platform task was performed.

## Arc 0.51.57 — Check and debug saved files

Ask Arc to test/debug a saved file or check it and correct any errors. Direct debug requests and conditional correction now use the existing read, check, repair and recheck workflow. Arc preserves originals, saves verified revisions and can repair exact lines in larger files.

Existing-file diagnostic requests automatically check the saved source when its checker is enabled. JavaScript debug requests also require real isolated assertions, rerun unchanged after repair. Clean files stay unchanged; missing or unsupported checks and stale file hashes cannot become a successful validation result. Check-only requests do not grant editing permission, and syntax-only requests do not execute JavaScript.

Plugin descriptions include examples and explain the scope. Syntax checks do not establish complete runtime behavior, security or deployment readiness. No dependencies were added and no generated code gains host execution privileges. All 0.51.56 features, including file appends, line edits and Twitch alerts, are retained.

## Arc 0.51.56 — Targeted coding file edits

Arc can read exact line ranges and fix only those lines in Created Files, preserving the surrounding code and original version. Current source hashes and observed ranges guard against stale or guessed edits. Supported syntax checks and existing JavaScript assertions run on corrected revisions when enabled.

Appending now recognizes direct requests naming a code file, retains original source and hash through the writing stage, and checks the saved result. Older flat-layout files append in their actual location instead of failing against a new category folder. The local PHP workspace gains append and inclusive line-replacement actions without changing its separate publish controls.

No dependencies were added. Existing Twitch alerts and other features are retained. File operations do not execute generated code or grant access to system folders. Setup examples and limits are in Coding tools.

## Arc 0.51.55 — Twitch alerts

Twitch now has its own settings page, available directly in Settings and through Plugins. Connect with Twitch's public-client device authorization flow, choose followed or searched channels, and receive live-stream alerts while Arc is minimized. Optional stream-ended/category alerts, game filters, quiet hours, device-voice announcements, recent activity and notification testing are included.

The listener runs without AI inference and reuses Arc's notification inbox and Windows banners. Tokens use OS-protected storage; connection recovery, token refresh, duplicate suppression, cancellation and disconnect are covered by automated tests. Clicking a channel notification opens the channel in your default browser without focusing Arc.

This build requires a one-time Twitch Public application registration and Client ID. Setup instructions are built into the page and documented here. No Twitch account was connected during development. Real subscribed-channel delivery remains unverified until that setup is completed. No extra dependencies were added.

Existing Steam tools, platform guides, models and browser/voice workflows are retained. This feature does not automatically watch or record streams.

## Arc 0.51.54 — Platform research guides

October 10, 2026. Adds **150 original guides: 30 each for eBay, Steam, YouTube, X / Twitter and Twitch**, researched against official platform documentation. Each guide has a concrete example, distinct collection and analysis instructions, verification checks, failure handling and source references.

- Find the five collections under **Settings → Plugins → Platform research guides**. Browse a platform's guides or send its example request to chat.
- eBay: comparable sold prices, condition and lot normalization, screenshots, hidden-offer handling, small-sample uncertainty and exportable evidence.
- Steam: native game lookup and current metrics, regional prices, reviews, news, achievements and comparisons. SteamDB historical data remains a reference link, not an automated lookup.
- YouTube: available transcripts, timestamped summaries, tutorials, comparisons, claim checks, inspected frames and Watch with Arc.
- X / Twitter: connected-session searches, threads, screenshot collections, author attribution, source checks and follow-up research.
- Twitch: accessible public channel, schedule, category and clip research; discussion through explicitly enabled Voice screen/audio sharing. A dedicated Twitch OAuth/API connection, live alerts, full chat history and media downloader remain future work.
- Complete task recipes fit the existing 8K automatic guidance budget; full access details and research references remain available in the guide. Added platform/domain recognition for eBay, Twitch and SteamDB.
- Existing saved off switches remain authoritative. All previous guide files are retained. No dependencies, model changes, account connections or background recording were added.

See the 150-guide index and research findings and future feature opportunities. Guides improve how existing capabilities are used; they do not make unavailable accounts or tools accessible.

Validation covers guide contents, exact counts and hashes, reproducible generation, real tool references, task retrieval, 8K/16K guidance integration and upgrade/off-switch behavior. Website access still depends on the user's session and platform restrictions; this is not a claim that all 150 tasks ran against live signed-in accounts.

This installer includes the Steam game research tools introduced in 0.51.53. Model weights remain separate. No new VirusTotal result is claimed for this file.

## Arc 0.51.53 — Steam game research

Built October 9, 2026. Adds read-only game lookup and statistics to ordinary chat and a clear Settings → Plugins → Steam game research · SteamDB links section with an example prompt and enable switches.

- Search by game name, resolve the correct app ID and retrieve current Steam players, region-specific store prices, review totals, game metadata, global achievement percentages and recent news.
- Source cards, per-metric retrieval times, five-minute bounded caching and rate-limit cooldowns. Missing data is unavailable, never an invented zero. No account or API key required.
- SteamDB app/chart/history reference links. SteamDB disallows automated scraping and has no public API; Arc does not fetch or claim its historical statistics. Historical peaks, price lows, sales/owner estimates and private player data remain unavailable.
- Existing off switches, offline requests and host tool allowlists remain authoritative. No new dependencies, credential access, background polling or automatic downloads.

Validation: 18 new JavaScript Steam tests, three Python worker-protocol tests, 48 related chat/research/video/setup tests and three affected plugin-catalog/migration checks passed. Live Steam checks passed for Stardew Valley, Counter-Strike 2 and Terraria, with additional achievements/news/UK-price checks. TypeScript and production UI build passed. The package check caught a missing worker registration; the two Steam tools are now registered in the shared model protocol. A broader plugin-suite attempt stalled in an unrelated later test and was stopped; the affected plugin tests were then run separately and passed. This is not a claim that the entire app test suite passed, nor a new local-model benchmark.

See Steam research for prompts, endpoints and limitations. The Windows installer remains unsigned by Authenticode; no VirusTotal result is claimed for this new file. Model weights are separate and normal updates preserve user data.

---

## Prior publication: cumulative release 0.51.52

Published October 9, 2026. The previous public installer in this repository was **0.50.4** under the `EXE` tag. Arc 0.51.52 includes the work below as one cumulative Windows update.

[Download 0.51.52](https://github.com/pixelgraple/arc-desktop/releases/tag/v0.51.52) · [Current feature overview](README.md)

These are historical development notes, newest first. Later entries supersede earlier defaults, browser connection methods, limits and validation status. For example, the initial session-only Hey Arc setting was later replaced by a remembered automatic-start preference; separate social sign-in profiles were later supplemented by connections to the user's chosen browser; screenshot research now opens visibly when needed. Historical test counts and measurements apply to their recorded builds, not automatically to 0.51.52. Internal versions without an individual note are not assigned invented changes.

## Additional integrated work in this cumulative release

Several changes are documented in feature references rather than individual patch notes:

- **Long-form writing:** story mode saves one TXT manuscript, targets 20–90 paragraphs, counts saved paragraphs, preserves the exact ending and a continuity checkpoint on SSD, and can resume verified appends. Ultra Until done uses bounded continuation for files and replies. Repetition guards stop repeated passages, including rotating paragraph sequences; rejected sections get limited fresh rewrites. This reduces loops but cannot guarantee completion or literary quality, and does not remove physical context limits.
- **Model residency (0.51.19):** the selected chat model remains loaded while idle or minimized. Temporary vision work restores the original model/context after a necessary handoff. Voice can return its worker to chat. The shared GPU queue, explicit selection/unload, shutdown and errors still govern resource ownership.
- **YouTube research (0.51.19):** retrieve timestamped available captions first; optionally transcribe bounded accessible audio locally when captions are missing. Temporary audio is cleaned up. The later Watch with Arc companion adds playback-aware conversation and frames.
- **Research → Security (0.51.20):** native bounded adaptations of Omnisci3nt, CloakQuest3r, CloudUnflare and WebSecProbe, with per-module progress, cancellation, source links, JSON export and a Discuss with Arc draft. DNS/TLS/header/registration/history observations do not prove an origin IP or successful security bypass. CompleteDNS uses encrypted API-key storage; its API requires an eligible plan.
- **Generated-code isolation:** Python execution uses a fresh Pyodide/WebAssembly sandbox with a virtual filesystem and bounded text input/output. No host-shell, UAC or network package installation is exposed to that code. Existing JavaScript checks remain isolated; approved SSH, container and third-party integrations have separate boundaries.
- **Custom guides:** import JSON, text or Markdown guides through Plugins, with field descriptions and downloadable starter examples. Added 120 substantive Ultra Until Done playbooks, alongside the 300 story guides and 300 advanced Hey Arc guides described below. Guide imports never install executable code or grant permissions.
- **Agent Reach onboarding and routing:** optional discovery/help in Plugins explains supported platforms, account connections and example prompts. Arc routes suitable tasks through available enabled capabilities and reports setup/access limits. Public checks can run in batches of three; success for one sample does not establish universal platform access.
- **Browser connection polish:** choose a detected Brave, Chrome or Edge installation, authorize its local connection, and route work to a tab matching the request. A closed selected browser can be started. Personal-browser permissions remain required; isolated research sessions do not inherit passwords or cookies.
- **Android remote connectivity:** persistent, revocable desktop pairing, private Tailscale connectivity away from the home Wi-Fi, connection restoration and retry behavior. The desktop must stay awake and available. The separate Android app remains a preview with physical-device checks outstanding.
- **Voice and desktop polish:** prewarmed models, minimized-window behavior, a visible Arc Mini helper, local fallback wake recognition, streamed speech/text handling, speech normalization and media-control work. Recognition and audio interaction are device-dependent, not speaker authentication.
- **Research and UI repair:** measured tokens-per-second information, clearer safe scraper diagnostics, progress/partial-result preservation, refreshed theme tokens and responsive panels. Fixed memory-pressure handoffs and screenshot/file-delivery paths without replacing installed models or clearing conversations.

## Recorded patch notes

## Arc 0.51.52

Repairs eBay research requests that stopped before navigating or returned only a saved filename.

- Registers the existing local coding-workspace tool with both inference boundaries. Its missing registration could reject an unrelated research request before inference.
- Limits required eBay research to relevant tools and bounds navigation output, preserving the normal final-answer allowance.
- Opens a visible research window for requested screenshots; reuses a connected personal browser, or promotes Arc's own headless workspace while retaining its current page.
- Recognizes “take 3 of them and screenshot them” as three images. Preserves the research answer alongside the attachments.
- Keeps distinct sold-page evidence and exact page excerpts through compaction. Active listings, duplicate captures and result-page images cannot complete a request for distinct sold item screenshots. Accepted best offers are not disclosed sale prices.
- Stops at eBay sign-in or access challenges with a specific explanation. No CAPTCHA solving or login bypass.
- Adds Settings → Plugins → eBay research with sign-in instructions, an example request and a Browser settings shortcut.

Validation: browser/runtime regressions and Python worker tests; real Arc Focus Ultra navigation from a cold visible Brave window. The public live session reached eBay's Sold Items sign-in gate. Three authenticated sold listings and a live sold-price average remain unverified until the user signs in. The full existing suite also exposed two memory-budget failures in modules identical to 0.51.51; this release does not change those modules. No dependencies added.

## Arc 0.51.51

The Feedback button now sends a UTF-8 text-file attachment to Arc's Discord feedback channel through `https://arc-net.xyz/arc/feedback`. Email delivery is no longer involved. The form explains where submissions go; an optional reply address can still be included.

The webhook credential is stored only in a protected server configuration file. It is absent from the desktop source, renderer, installer and receipts. No Discord sign-in is needed. Feedback includes only the reviewed message, category, optional reply address, Arc version and random reference. No conversations, logs, existing files or hardware details are collected.

The relay disables Discord mentions and redirects, verifies the returned attachment, limits submissions and preserves references across retries. Uncertain delivery is reported as pending and is not automatically replayed. Definitive rejections preserve the form for a later retry.

The browser and Arc Focus research fixes from 0.51.50 are preserved.

## Arc 0.51.50

Improves screenshot research with Arc Focus when the browser starts closed.

- Handles the inference engine's known malformed tool-JSON error consistently whether it arrives as an HTTP failure or inside the response stream. Existing bounded recovery keeps the browser state and completed work; it does not execute partial calls or replay completed actions.
- Preserves automatic browser startup from 0.51.49. Public research can open a background Brave research session without a selected personal tab. Signed-in browser work retains the browser's own connection permission.
- eBay price research requires a readable eBay price page before claiming that portion is complete. General coin articles and search snippets alone are insufficient.
- Once a readable item page is open, a single requested screenshot becomes the immediate capture step instead of allowing repeated navigation to exhaust the work budget. The capture service checks the page again before saving.
- Final-answer instructions distinguish advertised asking prices from completed sales, preserve currencies and exact source URLs, and avoid treating seller rarity claims as verified facts.
- Screenshot receipts retain the bounded page text already read during capture, preserving exact prices independently of image OCR. Text coverage and screenshot coverage are explicitly separate.
- Error and access-check pages cannot satisfy a requested content screenshot. Explicit error-page screenshots remain available for diagnostics. When a marketplace is unavailable, the model can use another observed source for the item photo while reporting the valuation gap.

No dependencies added. Existing models, conversations, settings and Watch with Arc are preserved. Site access challenges still require the user; Arc does not solve CAPTCHAs. A screenshot of a listing is not an appraisal or proof of a completed sale.

## Arc 0.51.49

Fixes browser startup and missing screenshot attachments during research.

- Research checks the actual browser connection before relying on a cached connected status. A closed research browser is reopened automatically.
- When connecting to a selected personal Windows browser, Arc starts it normally if it is closed. Existing browser permissions remain required; Arc does not change debugging preferences or import credentials.
- Requests such as “give me a photo of it, screenshot, then tell me its value” retain their screenshot requirement even if the model's task interpretation omits it.
- After public research finds sources, the missing screenshot becomes the next required browser step. Text-only searches cannot silently replace the requested photo.
- Saved images remain attached if a later operation fails. Vision analysis and screenshot delivery retain separate completion receipts.

No dependencies added. Watch with Arc, installed models and settings are preserved.

Validation: 350 automated browser, screenshot, research, tool-agent, intent and YouTube tests passed; one opt-in live test skipped. An additional real-browser test closed its isolated browser, ran the reported penny request with Arc Focus, saved a visible 1978-S coin screenshot, and completed local vision analysis. The test recovered one failed capture before succeeding. This validates delivery, not the accuracy of every generated numismatic claim.

## Arc 0.51.48

Adds **Watch with Arc**, a YouTube companion available in Plugins, Research and Voice. Prepare caption/audio evidence, follow the current playback boundary, inspect selected frames, compare up to three prepared videos, and save timestamped notebooks and images.

Simple spoken questions reuse prepared evidence in one streaming inference call. Background vision gives priority to conversation and preserves the resident text model, including outside Voice mode. Optional observation shows a blue player outline and stops on Escape in the watched tab.

No new dependencies. Existing browser, Agent Reach, speech, vision, Created Files and tool-routing services are reused. Settings and installed models are preserved.

Validation includes actual browser cropping, ad exclusion, Escape/outline cleanup, desktop and narrow Voice/Research layouts, progressive audio timestamp tests, real public YouTube captions, real CPU speech transcription, and actual Focus/vision inference. See WATCH_WITH_ARC.md for setup, limits and retention.

Regression maintenance updates the IPC test harness to isolate the previously added task interpreter, updates old 512 KiB file-limit fixtures to Arc's existing 2 MiB limit, and regenerates theme tokens so the new panel respects all app palettes. The import-size error now matches its enforced limit.

## Arc 0.51.47

Arc now asks the selected local model to interpret a request before choosing tools. Chat shows the understood outcome and expandable steps. The interpretation guides existing tool routing and remains in working context; it does not grant extra permissions or count a plan as completed work.

The interpreter supports follow-up subjects, paraphrased screenshot requests, requested counts and missing-target questions. Strict validation, one bounded repair, cancellation and a deadline preserve the original request when interpretation fails. Existing account, filesystem and mobile restrictions remain in force.

Screenshot target schemas now preserve the exact frame-qualified references from the latest page snapshot, fixing captures where the model shortened a reference such as `f6e3` to `e3`. Generic page posts no longer imply a switch to the social browser.

Validation: twelve real Focus interpretation cases; three distinct actual local-page screenshots through the full agent; desktop/narrow request-panel checks; Python runtime tests and affected JavaScript regression suites. Live X access still depends on the browser's own connection approval and platform access. No dependencies were added.

## Arc 0.51.46

Fixes a routing gap that treated “screenshot 3 tweets” as general research and never offered the screenshot tool. Numeric capture requests now activate the browser, preserve the previous user request's author/topic, and use the current screenshot count.

Explicit tweet research now opens the existing browser. Distinct rendered posts are retained through context refreshes, and articles/search snippets cannot satisfy the requested tweet sample. Incomplete screenshot replies retain the concrete capture or connection error even when Ultra stops for lack of progress.

No new dependencies. Browser permission, account settings, screenshot limits and CAPTCHA handoff are unchanged. Automated and live-test evidence is recorded under `work/qa`; live access still depends on the user's browser accepting its connection.

## Arc 0.51.45 — browser research recovery

- Social screenshot research reconnects the selected existing browser instead of opening a separate profile without its sign-in.
- The browser permission handshake allows up to two minutes, with cancellation available, instead of expiring after 30 seconds.
- Enabled Agent Reach account readers reconnect after an Arc restart, preserving per-platform permission switches.
- A short “try again” retains the preceding read-only screenshot task, count and vision request. It does not replay publishing or account changes.
- “Some tweets” requests three distinct screenshots by default; explicit counts still support up to 20.
- Browser functions mistakenly wrapped in the plugin transport are routed through their normal typed tools, availability and permission checks.
- Navigation failures preserve a specific, redacted reason and current page state for recovery.
- Social screenshot guidance verifies names through search instead of guessing profile handles. Recent page observations survive context compaction to reduce repeated navigation to empty profiles.
- A rejected browser connection stops screenshot work promptly with its actual reason, before model loading or repeated search/compaction loops.
- The blue capture outline, verified Created Files images and batch vision pipeline remain in place.

No dependencies or model downloads added. Browser-side permission, platform sign-in and human-verification checks remain required when applicable.

## Arc 0.51.44 — screenshot research

- Save up to 20 requested screenshots in chat and Created Files, including topic-based X/Twitter searches.
- Capture batches from exact observed post links, preserve completed receipts, reject duplicate post/image captures, and return to the feed for additional results.
- Keep observed post links across feed refreshes, skip already captured or failed posts, and constrain later batches to the number of screenshots still needed.
- Recover from animated/background element screenshot timeouts with a verified viewport capture when the whole target is visible.
- Show a soft light-blue border around the selected screenshot area in the browser. It follows scrolling and resizing, allows clicks through, and is removed after capture or cancellation.
- Analyze exact saved images together with local vision, restore the selected chat model after the batch, and report incomplete analyses truthfully.
- Keep saved-image progress through context compaction; provide enough bounded work rounds for 20 images.
- Prevent plan-only browser replies and the Away summary from masquerading as completed work.
- Add an example prompt in Agent Reach connection settings.

Validation: focused automated coverage, actual browser capture of 20 distinct local test pages, local vision analysis of all 20 images, signed-in X post capture and vision checks, 20-card desktop/mobile UI checks, TypeScript and production build. External platforms can still require sign-in or human verification and can limit access. No new dependencies or models.

## Arc 0.51.43 — browser screenshot delivery

Arc could inspect a browser page, call an unavailable screenshot action, then end a screenshot request with a future-tense plan. Temporary vision captures were deleted and never attached. The reported 0.51.41 diagnostic marked a turn complete despite two ARC_BROWSER_ACTION errors and zero saved files.

### Changes

- Added `browser_save_screenshot`, a typed, permission-gated tool that captures the selected tab or a fresh element reference, saves the actual JPEG through Created Files, and attaches its verified receipt to chat. Saving does not require loading the vision model.
- Screenshot requests recognize plurals and requested counts, including “take a screenshot of 3 of them.” The working context retains delivered receipts through browser-history compaction. Plan-only replies receive bounded continuation from current state; incomplete requests report the real saved count instead of claiming success.
- Unavailable browser actions report available actions and the screenshot-save tool without replaying the rejected action or restarting navigation.
- Byte-identical captures do not create duplicate files. For multiple requested tweets/posts, repeated captures of an already saved X/Twitter status do not count as another post.
- Real image attachments render inline, load when scrolled into view, and can expand. Preview reads require the original receipt ID and SHA-256; only bounded raster images are decoded. Save a copy and Open folder remain available.
- Existing local PHP workspace and SSH changes from 0.51.42 are included unchanged.

### Bounds and verification

Capture remains limited to the connected, user-authorized browser and at most ten saved screenshots per turn. No model-supplied output paths, account posting, cookie imports, or CAPTCHA solving were added. Partial attachments survive a later failure. The source URL is retained; screenshot files alone do not prove that the model chose the correct author or relevant post.

The exact reported prompt is covered by a deterministic model-driver regression: reject the unavailable screenshot action, return a premature plan, resume the current page, and deliver three distinct images. A real isolated Brave session through Arc's bundled Playwright MCP captured viewport, full-page and selected-element screenshots. Each was saved and read back with its hash. Actual React cards displayed all three at desktop and 390px widths; Electron's native decoder verified bounded previews. This is not a claim that a live signed-in X account or every local model was tested.

No new package dependencies. Install this version to update the running application; source edits do not patch an already running Arc process.

Validation: 230 focused Node tests passed (one optional test skipped), and all 88 Python worker tests passed, including the new screenshot tool in text and voice modes. The 2,876-test full run exposed two regressions in this change (plugin screenshot accounting and the shared tool protocol); both were repaired and their suites passed on rerun. Five pre-existing failures remain in memory budgeting, file-boundary checks and legacy theme colors; they are not reported as passing.

## Arc 0.51.42

Added **Code → Local PHP workspace**, also at the top of **Settings → Servers &
SSH**. Arc can now develop a PHP site in a persistent local source workspace,
copy selected files from an SSH project, edit with hash checks, lint PHP in an
isolated container and open a separate restricted preview window. The chat tool
uses the same workspace APIs as the editor.

Publishing is a separate user action with before/after source review, revision
checks, pinned SSH server identity, conflict detection and existing verified
backups. No automatic production upload, commands, database changes or restart
is included. Partial upload receipts are retained.

The optional runtime uses Docker Desktop Linux containers and the official PHP
8.4 CLI image (tested PHP 8.4.26 with SQLite3/PDO SQLite). No npm dependencies
were added. This is not a full XAMPP/Apache/MySQL environment; see
scope and setup.

Validation includes workspace and existing SSH tests, real PHP rendering/form
submission, syntax failure, blocked external networking, isolated Electron
preview requests, and the actual React editor at 1280, 600 and 390px widths.

## Arc 0.51.39 — request-based browser tab selection

Arc can now inspect the open tabs in a connected Brave, Chrome, or Edge session and select the page that best matches the user's request. “This page” and “what I'm watching” refer to the active tab. A named destination that is not already open is opened in a new tab, leaving existing pages in place. Connecting no longer creates a blank task tab.

The existing-browser connection uses the browser's local CDP permission. The user enables remote debugging in browser settings; Arc connects only to the loopback endpoint and the connection remains off until the user starts it. CAPTCHA and human-verification checks still pause for the user.

Agent Reach opens requested pages in new tabs so it does not overwrite the user's current page. Browser task instructions and settings describe automatic tab selection and the broader browser permission.

Validation covers tab listing and selection, local endpoint setup, existing browser connection failure, voice and Agent Reach flows, CAPTCHA guards, TypeScript/UI build, and packaging. The real user's browser still needs remote debugging enabled before the new full-tab connection can be exercised there.

## Arc 0.51.38 — browser vision and human verification

Arc can inspect the visible page in the connected browser from chat. It reads accessible page text first, then can send one temporary viewport or selected-element screenshot to the installed local vision model when visual inspection is needed. The screenshot is deleted after analysis. Existing Playwright actions handle only the requested page navigation, clicks and form entry.

Browser actions are limited to the user’s current task. Page text and images remain untrusted evidence. Sending, publishing, purchases, deletion and account/security changes require explicit user instruction. A fresh page snapshot is checked before interactions. CAPTCHA and human-verification challenges pause automation and show the user a handoff in Browser settings; Arc does not inspect answers or attempt to solve them. An unreadable safety snapshot blocks the action without claiming a challenge was found.

Validation: focused browser-control, browser-task-policy, visual-research, runtime tool-protocol and native-browser tests pass. The offline browser UI check passes at desktop and compact widths and verifies the human-verification handoff.

## Arc 0.51.37 — dedicated browser task tab

When Arc connects to an existing Brave, Chrome, or Edge session through the Playwright extension, it now creates a fresh task tab immediately. Browser tasks and Agent Reach account reads use that Arc-owned tab, keeping the user's approved page out of the task flow. The extension still requires its normal tab authorization when a browser session is connected.

Validation: browser-control and Agent Reach account tests pass; the production UI and TypeScript build succeeds.

## Arc 0.51.30

Add personal task-guide import to Settings → Plugins. Users can import UTF-8 JSON, text, or Markdown guide files, see the accepted fields and an example in the app, and download starter examples in both JSON and text formats. Imported guides are stored through Arc's existing local, versioned guide library and appear with the normal enable switch and task matching.

Guide imports accept only a name, description, tags, and plain-text instructions. Files are limited to 64 KiB. Guides remain reference material: importing one does not install code, add permissions, or override Arc's safeguards.

## Arc 0.51.29

Long-reply protection now recognizes rotating sequences of paragraphs, repeated sentence sequences without line breaks, and loops followed by a partial new sentence. The previous detector only recognized identical consecutive paragraphs at the end of a response. Detected loops cancel generation, retain one copy, and keep the reply explicitly incomplete. Fenced code and JSON structure are excluded from prose-cycle detection.

Strata now receives Arc's calculated `reasoning_budget_tokens` limit. The pinned Strata 0.1.39 runtime supports this field. Previously Arc sent the limit only to llama.cpp, allowing Strata reasoning to consume the whole response reserve during crowded-context tests. The cap follows the already fitted output allowance and preserves the selected thinking tier.

The story-section rewrite recovery from 0.51.28 remains in place. Repetition detection reduces runaway output; it cannot guarantee semantic originality or make a single reply exceed its context/output limits.

Oversized file sections now receive a bounded smaller-section retry instead of a contradictory instruction to collapse the whole file. Argument-length failures have a safe diagnostic category; successful writes clear the retry hint. Story section prompts leave room below the tool limits for both prose and continuity checkpoints.

Final answers receive the same repetition check before persistence, including deferred tool-loop answers and short streams that never reach the periodic scan threshold.

## Arc 0.51.28

Long-story writing now goes straight to the section writer in Ultra with Until done enabled. Rejected drafts are removed from the next working prompt; successful full sections remain in the file, with only the exact saved ending and continuity checkpoint carried forward. Original tool observations remain available for diagnostics.

A rejected section gets up to two fresh rewrite attempts. Successful saves reset that allowance, so unrelated repetition later in a long story does not consume an earlier section's retry allowance. Persistent failure still stops safely, preserves saved text, and reports the incomplete file. Duplicate checks and exact file/hash validation remain enforced.

The native runtime now recognizes start/append file tools as file generation and reserves sufficient response space, including the smaller reasoning allowance used for file writing. Chunked output keeps its existing sampling settings.

These changes reduce retry loops; they do not guarantee literary quality, semantic uniqueness, or completion of every requested length with every local model.

## Arc 0.51.27 — Agent Reach connections and live checks

Settings → Plugins → Agent Reach connections now opens a separate Brave sign-in window for each platform. Users sign in themselves, then enable that session for read-only platform tools. Disable access retains the session; Clear saved session removes that platform's Arc browser data. Personal browser profiles are not imported. Browser challenges and session expiry still require the user. A public-page read is not proof of account authentication.

The bundled Agent Reach integration now exposes 18 structured tools: the existing ten public readers, web search using Arc's Exa/search fallback, three V2EX readers, Bilibili search and details, and two connected-platform read/search tools. New tools are available on upgrades without overriding previously saved off switches. Platform tools cannot post, send private messages, execute arbitrary shell commands or retrieve browser cookies.

Opt-in public diagnostics exercise 15 network samples, three at a time, with cancellation and per-sample results. Routine dependency refresh does not perform these network checks. A passed sample means that particular sample returned expected content at the displayed time; it does not guarantee every resource or account works.

Bilibili API failures can fall back to indexed search leads or an isolated rendered page. These results explicitly identify their limitations and never claim to be captions or watched video. Reddit-style challenge pages are rejected as research evidence even when HTTP succeeds. Connected platforms use distinct profiles and serialize same-platform requests while independent platforms can run together.

No new npm/Python dependencies or global upstream installers. Agent Reach remains pinned to the reviewed 1.5.0 archive; Arc uses its existing browser engine and public transports. This release does not install OpenCLI, import cookies from personal browsers, configure Groq, or enable publishing/social write actions. Xiaoyuzhou currently supports episode-page reading; podcast audio transcription through its upstream Groq integration is not configured. Logged-in capabilities require each user's sign-in and cannot be verified with public fixtures alone.

## Arc 0.51.26 — in-chat visual research

- Fixed Strata HTTP 400 failures after research/tool context fitting. Token counting now uses the same grammar-normalized tool definitions as generation, including the descriptions added for large string limits. A small backend context margin covers Strata's reserved tokens. Original execution validators and user context limits are unchanged.
- Included the built-in screenshot/vision research tool on existing-profile upgrades. Previously it could remain only an optional offer, so an explicit visual research request performed a text read instead. Saved disabled switches remain disabled.
- Automatically opened research workspaces now run Brave headlessly. Tool progress, screenshot file receipts, vision findings and sources remain in chat. Explicit Browser settings connections still open the chosen visible workspace or approved existing session.
- Screenshot research now hands off to an evidence-based answer when its six-page budget is reached. Already queued extra captures return a coverage-limit notice instead of creating a repeated-failure loop. A website-purpose question starts with the homepage and only expands when necessary.
- Diagnostics identify screenshot research, browser setup and compaction events and retain bounded tool/schema counts without storing prompts or raw server errors.

Validation: browser/plugin/visual research regressions; Python runtime tests; replay of a real Strata request that previously returned HTTP 400. Live Purple research and release packaging evidence are saved in the workspace's `outputs/purple-research-qa` directory.

The full Node suite reported 2,758 passed, five skipped and three failures in unchanged areas: two tests still assume a 512 KiB text-file limit where the application now allows 2 MiB, and the theme audit identifies pending migrations in `src/security-research.css`. Targeted research, browser, plugin and Python runtime checks pass.

Limits: Brave and Arc's local vision model must be installed. Disabled browser tools remain disabled. Login walls and inaccessible pages may still prevent capture. Vision and a large text model can take turns in VRAM, which adds loading time; this release does not claim instant visual research.

## Arc 0.51.14

Hey Arc starts automatically after setup when Focus and speech are installed. Arc Mini appears without restoring or focusing the main window. Focus and the speech engines prepare before the first wake phrase and remain warm while the assistant or a voice conversation needs them. A resident compact Focus allocation can be reused without changing the written-chat context.

Turning Hey Arc off is remembered across restarts. It leaves an active voice conversation running. Windows microphone permissions still apply; lock, suspend, quit, and capture failures stop listening. Lock/unlock does not restart the microphone within the same app session. Shared GPU clients can still reclaim idle resources.

Strata setup now offers the alesha-pro abliteration projection variant in Q2_0 and IQ2_XS. The Strata engine and existing model IDs are preserved. Verified existing weights can be reused with a 483,520-byte pinned projection download; there is no second 66 GB model copy. New configuration and receipt validation checks the vector's location, hash, size and projection flags. The previous configuration is kept for recovery while new installs select the requested variant.

See Hey Arc and Strata integration for behavior and limitations. Bounded regression checks and a local Strata comparison accompany this release; they do not establish universal hardware performance or microphone accuracy.

On the RTX 5090, a controlled Q2_0 comparison used three paired requests in each of three categories with 16k context and thinking disabled. Median generation rates were 140.2 to 146.3 tokens/sec for explanation, 217.0 to 217.2 for coding, and 188.7 to 191.2 for tool arguments (original to variant). First-token latency increased by 23 to 105 ms. Both configurations passed all six syntax/argument checks. These short prompts establish similar performance; they do not measure broad correctness, refusal behavior, IQ2_XS performance, or operation on a 32GB system-RAM PC.

## Arc 0.51.0 — Android companion preview and Hey Arc repair

### Desktop Hey Arc

Focus now explicitly supports the 8,192-token context requested by Hey Arc, retaining the same conservative 8,192 MiB free-memory requirement. Previously the registry listed only 16,384 tokens, causing settings validation to reject enabling Hey Arc. Existing measured larger-context optimizer results can be reused for a smaller supported context of the same model without fabricating a new benchmark or repeating optimization. The original measured context and results remain intact. Settings now show the sanitized underlying failure instead of only a generic save error.

### Android companion

New Settings → Android companion pairs an Android 11+ phone with the desktop. The native app supports wake listening during an explicitly started foreground session, tap-to-talk, text questions, research, streamed replies and speech, optional screen samples, and permitted playback-audio transcription. Focus and speech warm up when the session starts. The phone tool boundary is research-only, with no file creation, filesystem, shell, browser control, or phone-control tools. See setup, limitations, security and build instructions.

The preview APK is debug signed and distributed separately. It is not a production app-store release. No phone AI models are downloaded. No existing Arc model or Strata installation is replaced. Desktop and phone voice sessions are mutually exclusive; the PC must remain running, awake and unlocked.

### Validation

The full desktop suite ran 2,650 tests: 2,644 passed, five were skipped, and one older test incorrectly expected Focus to reject 8k. That expectation was updated, and the focused 99-test regression run passed. Additional mobile host tests cover the real adapter's Focus selection, research-tool allowlist, wake-recognizer fallback, and explicit deeper-question policy. Android compile and lint pass; retained lint warnings concern the intentionally pinned private certificate and English-only UI text.

The Android emulator pairs over real pinned HTTPS with the production transport and exchanges a question and a clearly labeled fixture reply. This checks application/transport behavior, not model speed or quality. Physical S22 microphone recognition, YouTube capture policy, speaker echo separation, background battery restrictions and real end-to-end model latency remain acceptance checks when the device is available.

Added desktop packages: selfsigned 5.5.0 and qrcode 1.5.4 (MIT). Android uses native platform APIs. Existing dependency-audit findings were recorded separately; no broad dependency upgrade was folded into this change.

## Arc 0.50.8

### Advanced Hey Arc guides

300 additional, individually specified voice tasks across 30 domains: model readiness, conversation, sharing, reading, visual analysis, media, research, local information, comparison, files, Windows apps, browsers, PHP, Python, JavaScript, web design, debugging, data, writing, learning, meetings, planning, everyday calculations, health literacy, accessibility, memory, task chains, recovery, privacy and images.

Every guide includes inputs, concrete operations, task-specific acceptance criteria, supported capability routes, follow-up examples, model and file handling, and failure recovery. Full documents exceed 1,200 words. Their complete core procedure fits Focus's 8k guide budget. The library now has 20,066 Arc-authored guides and 236 retained upstream guides. See the complete index.

Guides are instructions, not new inference engines or automatic permission. Existing runtime policy prepares models; guides cannot invent model-loading tools, install models silently, or control an unconnected browser/PC. Matching remains optional, with zero to 24 applicable guides subject to the context budget.

### Save and show files

When the user explicitly asks to create/download a file and show it in Explorer, Arc can request selection of a receipt-backed file from that task after completion. This does not execute files. The reply distinguishes an Explorer request from visually verified success. Older files retain the file-card folder button. Disabled capabilities and missing host support remain respected.

Example: “Hey Arc, create a checklist for my project, then show it in Explorer.”

### Visible screen-sharing boundary

A soft light-blue border and a small status label appear while a screen or window is shared. The label separately reports whether shared-audio listening is enabled. A selected full screen marks its display; a movable selected window uses a session indicator on all displays labeled “Selected window shared.” This indicator does not expand capture scope.

Escape stops screen sharing and shared-audio listening even while another app is focused. The ordinary voice conversation remains available. Local Escape and the Stop sharing button are also available. If Windows will not register the global shortcut, the label directs the user to Stop sharing in Arc. Stopping or closing voice removes the border and unregisters the shortcut; no shortcut is reserved when sharing is off.

The overlay is click-through, does not take keyboard focus, and uses capture protection to avoid feeding its border into screen observations. Screen memory still analyzes occasional changed frames; the border does not imply continuous video understanding. Exclusive fullscreen applications may suppress ordinary desktop overlays.

### Validation scope

Automated catalog, guide routing/context, file delivery/authorization, sharing lifecycle and voice IPC tests are included. No claim is made that all 300 tasks were executed with every model, that hardware latency improved, or that all Windows media and multi-monitor configurations were tested.

## Arc 0.50.7

Adds 16 substantive voice-task playbooks for researched answers, screen explanations, video discussions, coding help, local information, browser actions and task recovery. Voice matching prioritizes a relevant task playbook over generic speaking coaching.

Explicit spoken requests to go deeper now select Ultra for that turn and reach the inference backend. The following ordinary question stays fast. Screen access still uses the explicit source chooser; this release does not add silent desktop capture.

See HEY_ARC_TASKS.md for all playbooks, selection rules and test limitations.

## Arc 0.50.6

Adds the opt-in Hey Arc desktop assistant under Settings → General & voice. A visible desktop indicator accompanies local wake listening. Wake detection uses the installed Windows English speech recognizer on the CPU, selects Focus for fast voice conversations, caches the greeting when speech can prewarm, and retains idle Focus weights between wake conversations. It pauses for other models/work and stops on lock, suspend or exit.

The first version expects “Hey Arc” followed by a pause, then the question. It is session-only and off on the next launch. No automatic screen capture, cloud transcription, or wake-model download is added. See HEY_ARC.md for memory policy, prerequisites, tests and limitations.

## Arc 0.50.5

Arc 0.50.5 adds 300 focused story-writing guides: 220 general fiction guides and 80 adult-erotica craft guides. They help Arc respond to simple prompts such as “write a non-erotic story” or “write an erotic story” with useful choices for premise, outline, drafting, revision, and continuation. Explicit non-erotic and adult-erotic requests retrieve their matching guide family without cross-matching.

Erotic-fiction guidance is for clearly adult characters and consensual situations. These are instruction-only guides; they do not add execution tools, enable adult-content generation beyond the user's request, or change Arc's safety controls.
