# Arc version history — cumulative release 0.51.52

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
