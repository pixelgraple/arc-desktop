# Arc — your local AI workspace

**Talk with your PC. Research the web. Work on code. Ask about what you see and hear.**

Arc brings local models, voice, screen context, research and task tools into one Windows desktop app. Its standout feature is **Show Arc**: share a screen or window and optional computer audio, then have a conversation about it. Local speech, vision and a text model work together; you choose when sharing starts and stops.

**Current release: 0.51.75 · Windows x64 · development build**

[Download the Windows installer](https://github.com/pixelgraple/arc-desktop/releases/download/v0.51.75/Arc-Setup-0.51.75.exe) · [Release notes](https://github.com/pixelgraple/arc-desktop/releases/tag/v0.51.75) · [Version history](CHANGELOG.md) · [Arc website](https://arc-net.xyz/)

This repository distributes Arc releases and documentation. Optional model weights are downloaded separately; they are not packed into the installer. Arc remains under active development, and external sites, hardware and model behavior can affect whether a task completes.

## New in 0.51.75

This release includes the changes since public 0.51.71:

- **360 detailed guides:** 120 for email, 120 for Reddit and 120 for timed work. Find them in Settings → Plugins under Guides. Compact relevant guidance enters working context; complete instructions remain available through the paged reader. These guide existing capabilities rather than adding executable tools.
- **Relevant saved-chat recall:** retrieve earlier work and recognized corrections from local saved written conversations. Use Settings → Memories → Use past conversations, or exclude recall for a request. No extra GPU model or training.
- **One approved Reddit post, now or later:** review the exact account, community, title, body and time. Arc submits once while running and awake. This replaces interval batches; missed times and uncertain outcomes require review.
- **Questionnaire-based email assistance:** choose categories, reply examples, sender filters and pace, then approve one supported inbox pass. Each message gets fresh local-model context, with delivery records and pause/stop controls.
- **Better guide routing:** pause, cancel and status requests retain their intended operation; timed guidance requires a matching timed intent.

The release regression run passed 44 tests. The earlier email implementation passed 3,791 tests with 10 skips and no failures. Production and packaged-reader checks verify the shipped build. Counts cover different scopes and should not be added together. No new speed or VRAM benchmark is claimed.

Email adapters initially support fixed English paginated Gmail, Outlook and Proton layouts; live-account compatibility needs a supervised trial. Reddit posting also needs a supervised account check. Other timed-work guides cover reminders and planning, **not a general background research/file/email scheduler**.

[Release details](releases/0.51.75.md) · [Guide index](releases/0.51.75/EMAIL_REDDIT_TIMED_GUIDES.md) · [Email setup](releases/0.51.75/EMAIL_ASSISTANT.md) · [Reddit setup](releases/0.51.75/REDDIT_POSTS.md) · [Chat recall](releases/0.51.75/CONVERSATION_RECALL.md)

## Earlier improvements in 0.51.65

- **Four measured VRAM tiers:** choose Light, Balanced, Maximum or Extreme in **Settings → AI Performance → Lower VRAM usage**. Arc tests supported cache/buffer settings and eligible CPU placement while keeping the selected model and context. The short comparison accepts at most 20%, 35%, 50% or 75% generation speed loss respectively, with the same prompt-speed check when normal timing is available; real task speed and memory savings vary.
- **Clearer research screenshots:** validate visible page areas, use a readable viewport unless whole-page coverage was requested, and deliver the actual saved image with its vision observations. Final-answer repairs preserve substantive explanations alongside the image; a persistent plan-only reply is reported as incomplete.
- **Six substantial completion guides:** find research, screenshots, file continuation, debugging and recovery workflows in **Settings → Plugins**. The full references contain 8,100 words; compact instructions load when relevant, with longer references available on demand. They guide existing tools rather than add new executable backends.
- **Longer files in sections:** Ultra's optional **Until done** mode keeps requirements, exact file endings and revision-checked checkpoints on disk. Incremental JSON and repetition checks help protect accepted work while bounded continuation builds the next section.
- **Multi-file website projects:** save a shared plan, create pages/styles/scripts one file at a time, check supported syntax/local links, resume from saved revisions and export the directory.
- **Measured evidence:** the report separates engine token speed from web/browser/vision/file task completion, and GPU memory from host RAM. It records actual failures and limits instead of counting an intended action as completed.

These are tested tool and workflow improvements, not a guarantee that every model answer is accurate or every task will finish.

### Historical 0.51.65 measurements on Arc Focus and RTX 5090

One Windows PC, 16,384-token context, three 512-token generation samples per mode. These are **raw whole-device text-generation peaks**, not exclusive model allocations; pre-load medians were not subtracted.

| Mode | Median tok/s | Throughput change | Pre-load GPU median, MiB | Generation GPU peak, MiB | Raw peak MiB saved |
| --- | ---: | --- | ---: | ---: | ---: |
| Normal | 187.28 | Reference | 1,711 | 8,278 | Reference |
| Tier 1 | 185.38 | 1.01% loss | 2,096 | 7,781 | 497 |
| Tier 2 | 184.24 | 1.62% loss | 2,008 | 7,927 | 351 |
| Tier 3 | 182.09 | 2.77% loss | 1,889 | 7,490 | 788 |
| Tier 4 | 52.67 | 71.87% loss | 2,210 | 7,432 | 846 |

All five final modes delivered the requested web answer, readable screenshot/vision result and exact 1,000-line file, and reused the model after work. Manual review still found factual and citation errors. Tier 4's file took 611.56 seconds versus Normal's 121.59 seconds, including recovered model-call failures and an unusually slow call. Its extra raw generation saving over Tier 3 was only 58 MiB in this run, so Extreme was a poor speed/memory trade-off here. Overall workload peaks, vision handoffs, CPU/RAM/disk counters, individual samples, earlier failures and measurement limits are in the full report.


[Changes since 0.51.60](releases/0.51.65.md) · [Full workload report](docs/VRAM_WORKLOAD_REPORT_0.51.65.md) · [Machine-readable evidence](docs/VRAM_BENCHMARK_DATA_0.51.65.json) · [VRAM, RAM and SSD explained](docs/MEMORY_ARCHITECTURE.md)

## Watch, listen, and talk

- **Show Arc:** discuss a shared screen, window or camera. Ask about visible text, a diagram, code, an article or what someone just said. Optional recent visual context retains up to ten sampled observations; optional shared audio is transcribed locally and bounded to the current session. This is still-frame sampling, not continuous video understanding.
- **Visible sharing:** a soft blue boundary indicates sharing. Escape stops desktop sharing and shared-audio listening. Screen access and microphone access are separate permissions.
- **Hey Arc and Arc Mini:** once setup, Focus and speech are ready, the desktop helper can listen for “Hey Arc” while the main window is minimized. Say the phrase, wait for the greeting, then ask. Automatic startup is configurable; disabling it is remembered. Windows microphone permission is still required.
- **Responsive voice:** Focus and speech prepare in advance and can stay warm. Replies use streamed text and speech, with spoken-text cleanup and optional media controls. Actual latency and echo rejection depend on the PC and audio setup.
- **Watch with Arc for YouTube:** find the current video, prepare captions or permitted audio, follow playback, inspect selected frames, bookmark moments, compare up to three prepared videos, and save timestamped notebooks with analyzed images. Find it in **Plugins**, **Research → YouTube**, or **Voice → YouTube**. Background observation gives priority to the conversation.

## Research that shows its work

Arc combines web search, source reading, Agent Reach, browser actions, screenshots and local vision. Tool activity, sources, saved files and incomplete work appear in chat.

- **Agent Reach:** public web, YouTube captions/search, GitHub, RSS, V2EX, Bilibili, and connected-platform readers. Supported account-based access includes X, Reddit, Facebook and other platforms shown in Settings. These are the capabilities Arc implements, not a promise that every upstream Agent Reach integration is available.
- **Your browser:** choose installed Brave, Chrome or Edge, connect it with its required browser permission, and let Arc select the relevant tab from your request. Connected personal sessions retain their sign-ins. Arc's separate research profile has its own sign-in and does not import passwords or cookies.
- **Screenshot research:** request up to 20 screenshots, save them in chat/Created Files, and ask local vision to summarize them. A blue outline marks the capture area. Partial results survive a later failure; a screenshot is not automatically proof that a claim is true.
- **eBay:** request actual sold listings and screenshots, with an average only where comparable explicit sold amounts are available. Asking prices, lots, differing currencies and undisclosed accepted offers must be treated separately. See **Settings → Plugins → eBay research** for sign-in and resume instructions.
- **Research → Security:** bounded domain-research workspaces inspired by Omnisci3nt, CloakQuest3r, CloudUnflare and WebSecProbe, with module progress, cancellation and JSON export. These are native adaptations with limited scope, not the complete upstream CLIs. CompleteDNS API use requires suitable account access.

Sign-ins, CAPTCHAs and human-verification checks remain the user's responsibility. Connecting a browser or importing a guide does not grant unlimited permission to act on accounts.

## Create, code, and continue

- **Instant, Medium, High and Ultra:** choose a work level. Ultra can continue through useful work stages with context refreshes and saved evidence. A request summary shows what Arc understood before it acts.
- **Until done:** optional chunked file/reply continuation, bounded checkpoints, exact saved-file endings, repetition checks and partial-result preservation. Long-story mode targets 20–90 paragraphs in a TXT file. This does not remove context limits or guarantee every requested length or task will finish.
- **Created Files:** create, inspect, revise and append to saved files, with verified receipts and appropriate syntax/behavior checks. Image cards support previews, Save a copy and Open folder.
- **Local PHP workspace:** develop and preview a PHP site locally before publishing reviewed changes through SSH. Uses optional Docker/PHP containers; it is not a complete XAMPP/MySQL distribution. Publishing is a separate explicit action with revision checks and backups.
- **Generated Python:** runs in a fresh Pyodide/WebAssembly environment with a virtual filesystem, bounded inputs/outputs and no host shell, network package installs or UAC access. Third-party integrations and authorized SSH operations have separate permissions.
- **Guides and helpers:** thousands of optional task instructions, specialized voice/story/Ultra playbooks, and coordinated read-only helper teams with up to 24 assignments per turn, five admitted at once, sharing the selected inference queue. Import your own JSON, text or Markdown guides in **Settings → Plugins**, using the included examples. Guides are instructions, not executable plugins.
- **Memory and model residency:** keep the selected model loaded between replies; temporarily switching for vision restores the original selection where possible. Shared GPU scheduling and errors can still require a handoff. Local SSD-backed originals and retrieval support longer tasks without pretending to enlarge the active model context.

## Models and hardware

The selector includes these optional models. Sizes come from the current pinned model manifest and are approximate **weight download sizes**, not required VRAM or total installation space.

| Model | Quantization | Approx. weights |
| --- | --- | ---: |
| Arc Spark | Q4_K_M | 1.22 GiB |
| Arc Swift | Q4_K_M | 2.33 GiB |
| Arc Focus | Q4_K_M | 5.38 GiB |
| Arc Core | Q3_K_S | 11.24 GiB |
| Arc Scout [Lite] | IQ3_XXS | 12.69 GiB |
| Arc Code [Lite] | Q4_K_M | 17.28 GiB |
| Arc Reason [Lite] | Q4_K_M | 19.78 GiB |
| Arc Strata Abliterated [Q2_0] | Q2_0 | 61.84 GiB |
| Arc Strata Abliterated [IQ2_XS] | IQ2_XS | 63.33 GiB |

Strata uses its own SSD-aware inference engine; it is not routed through llama.cpp. Its abliteration variant reuses verified existing weights with a small pinned projection file where eligible. A new Strata setup needs substantially more free storage than the final weights alone. Its minimum verified-GPU gate is 12 GiB dedicated VRAM, with 24 GiB recommended; compatibility does not establish speed.

Local sight and speech use separate optional models. The hardware optimizer benchmarks a bounded set of installed, compatible configurations and uses a default **32 GiB Arc system-RAM ceiling**. Windows CPU, CUDA and Vulkan execution paths are implemented; detecting AMD, Intel or Apple hardware does not mean every corresponding acceleration backend is implemented. This release is Windows x64, not a macOS build.

The historical 0.51.65 [workload report](docs/VRAM_WORKLOAD_REPORT_0.51.65.md) compares Normal and four memory tiers, identifying the tested GPU, model, context, tuning and resource counters. It separates short calibration from research, screenshots, vision and file completion. These results do not establish universal VRAM savings, lower-card fit or model accuracy. See [the memory design](docs/MEMORY_ARCHITECTURE.md) for what stays on GPU, what can use RAM, and why Strata's SSD-aware engine is separate.

## Additional features and current limits

Saved conversations, prompt commands, source cards, model/context controls, local usage information and a measured tokens-per-second display remain available. Image Studio uses a separate hosted service; it is not an offline local image model. Feedback now submits a reviewed TXT attachment through Arc's Discord relay without requiring the user to sign into Discord.

The desktop includes **Android Companion preview** pairing/settings. A separate development APK can use the PC's models for voice, research and permitted screen/audio context; remote use needs a private connection such as Tailscale, with the PC running and awake. The APK is not included in this Windows release. Physical-device behavior remains a preview acceptance check. There is no iPhone app in this release.

Local inference stays on the PC when local components are used. Web search, connected platforms, downloads, hosted image generation, optional remote memory and submitted feedback use their respective online services. Normal updates preserve separately stored models, settings and conversations.

## Download verification and release status

`Arc-Setup-0.51.75.exe` is approximately 320 MiB (335,240,500 bytes).

SHA-256:

```text
6b46b4833e93cc40ab6b1068fb1a327b16b994e76b15380810ed639bcb2ac3ad
```

The release includes `SHA256SUMS.txt`, a cumulative changelog and [detailed release notes](releases/0.51.75.md). The historical [0.51.65 workload report](docs/VRAM_WORKLOAD_REPORT_0.51.65.md) remains in the repository with its original scope. This installer is **not Authenticode-signed**. No matching VirusTotal report has been supplied for this exact build; older reports do not scan this file. Signed application-update metadata is a separate protection.

Current release notes distinguish automated guide, email, scheduling and package checks from unverified live-account behavior. The historical workload report retains its own automated and real-task verification limits. Browser/account restrictions and model behavior can still prevent task completion. Authenticated eBay sold-listing results and real Twitch account delivery retain their documented setup requirements.
