# Arc — your local AI workspace

**Talk with your PC. Research the web. Work on code. Ask about what you see and hear.**

Arc brings local models, voice, screen context, research and task tools into one Windows desktop app. Its standout feature is **Show Arc**: share a screen or window and optional computer audio, then have a conversation about it. Local speech, vision and a text model work together; you choose when sharing starts and stops.

**Current release: 0.51.52 · Windows x64 · development build**

[Download the Windows installer](https://github.com/pixelgraple/arc-desktop/releases/download/v0.51.52/Arc-Setup-0.51.52.exe) · [Release notes](https://github.com/pixelgraple/arc-desktop/releases/tag/v0.51.52) · [Version history](CHANGELOG.md) · [Arc website](https://arc-net.xyz/)

This repository distributes Arc releases and documentation. Optional model weights are downloaded separately; they are not packed into the installer. Arc remains under active development, and external sites, hardware and model behavior can affect whether a task completes.

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
- **Guides and helpers:** thousands of optional task instructions, specialized voice/story/Ultra playbooks, and up to three read-only helpers sharing the selected inference queue. Import your own JSON, text or Markdown guides in **Settings → Plugins**, using the included examples. Guides are instructions, not executable plugins.
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

An older RTX 5090 optimizer sample measured approximately 231 tokens/s for Swift and 192 tokens/s for Focus. Those are historical engine samples, not current-version benchmarks, whole-app latency or general accuracy scores. New Strata Q2_0 paired checks found similar short-prompt speeds before and after the projection variant; see the [version history](CHANGELOG.md). No universal speed or accuracy claim is made.

## Additional features and current limits

Saved conversations, prompt commands, source cards, model/context controls, local usage information and a measured tokens-per-second display remain available. Image Studio uses a separate hosted service; it is not an offline local image model. Feedback now submits a reviewed TXT attachment through Arc's Discord relay without requiring the user to sign into Discord.

The desktop includes **Android Companion preview** pairing/settings. A separate development APK can use the PC's models for voice, research and permitted screen/audio context; remote use needs a private connection such as Tailscale, with the PC running and awake. The APK is not included in this Windows release. Physical-device behavior remains a preview acceptance check. There is no iPhone app in this release.

Local inference stays on the PC when local components are used. Web search, connected platforms, downloads, hosted image generation, optional remote memory and submitted feedback use their respective online services. Normal updates preserve separately stored models, settings and conversations.

## Download verification and release status

`Arc-Setup-0.51.52.exe` is approximately 319 MiB (334,292,623 bytes).

SHA-256:

```text
7aaf881bcdf8507272c2e2a72d1f6a6907257f38b08aa7ac97a79617148de380
```

The release includes `SHA256SUMS.txt`. This installer is **not Authenticode-signed**. No matching VirusTotal report has been supplied for this exact build; reports for older hashes do not scan this file.

For 0.51.52, 216 focused Node tests, 91 Python worker tests and 96 voice-boundary tests passed. The complete test run found three failures; the screenshot/file-card regression was repaired and passed its rerun, while two existing memory-budget failures remain. Live Focus/Brave testing reached eBay's Sold Items login screen; three authenticated sold listings and a live average are still unverified. See the release notes for precise coverage.
