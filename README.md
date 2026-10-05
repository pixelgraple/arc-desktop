# Arc 0.50.4 — a local AI workspace for voice, code, research, and images

Arc is a Windows desktop workspace that brings local AI chat, coding, research, image understanding, speech, and task tools together. Its standout feature is **Show Arc**: share a window, screen, or camera, optionally share computer audio, and ask Arc about what you are seeing or hearing in a voice conversation.

This is an early Windows x64 release. Models and runtimes are selected or installed separately; the Arc installer does not bundle the large model weights. Hardware support, model quality, and response time vary. The benchmark section below describes the measured scope instead of implying every feature works identically on every PC.

## The feature to try first: talk about your screen and audio

In Voice → **Show Arc**, choose a screen, window, or camera. When you ask about something visible, Arc can send a fresh still to its local vision model. You can also enable **Remember recent screen context**: while voice is idle, Arc samples changed frames about every two seconds, skips unchanged frames, and keeps up to ten observations for at most ten minutes. This is sampled context, not video recorded or analyzed at display frame rate.

To discuss a video, meeting, or other computer audio, select **Share with speaker audio** in the operating system's picker and turn on **Listen to shared audio** in Arc. A local speech model transcribes short segments as they arrive. Arc can retrieve relevant excerpts from up to ten minutes of recent speech (bounded separately to 24,000 characters, roughly 8,000 tokens). It pauses observation while you speak or Arc replies. Raw shared audio is not saved; the transcript and sampled visual notes are discarded when the voice session closes. Capture is user-started and can be stopped at any time. Audio loopback depends on Windows and the selected source.

That lets you ask things such as “What does this diagram show?”, “Summarize what the speaker just said,” or “Read this part of the screen.” The result is limited by missed frames, transcription errors, model speed, and available local GPU memory. Arc does not continuously understand every frame or every word.

## What Arc includes

- **Local chat and task work:** choose an installed text model, use Instant or Think deeply, and select a supported context size. High and Ultra effort are subject to that model's limits and the configured context. Arc can plan bounded multi-step tasks, use approved tools, show progress, and preserve intermediate evidence.
- **Context and memory:** automatic context compaction and retrieval can continue longer chats without replacing the original transcript. Saved conversation and file originals remain available locally for exact reads; retrieval is selective and can miss relevant material. Optional remote BrainAPI memory requires separate configuration and is off until enabled.
- **Coding:** ask Arc to create and revise real files in Created Files. Supported syntax checks cover HTML, CSS, PHP, JavaScript, Python, and JSON. Optional repairs preserve the original; isolated JavaScript fixtures run in a restricted interpreter. A check is evidence for that check, not a security audit or proof of general correctness.
- **Research:** public web search and page reading return source links; Research also contains News, Web Scraping, and Security. News updates on its automatic hourly schedule and falls back to publisher feeds when the index is unavailable. Scraping is bounded and follows public-site access rules; it does not bypass CAPTCHAs or sign-in barriers.
- **Browser control:** connect a Brave session explicitly to let Arc inspect accessible page text and operate supported controls. Browser use is separate from Arc's built-in browser. Canvas-only controls, CAPTCHAs, and some inaccessible pages require human help; consequential clicks and submissions still affect the signed-in website account.
- **Voice:** local speech recognition and synthesis support spoken conversation. The selected text model can stay resident during Voice when memory allows; GPU contention and model size can add wait time. Voice uses up to a 16k working context.
- **Images and design:** Vision AI can inspect an image and extract visible text. Image Studio can request one hosted image at a time in square, portrait, or landscape formats; prompts go to its hosted generation service and results save as local PNGs. Image generation is not an offline local checkpoint in this release.
- **Tools and guides:** Arc includes file, research, browser, system, and other tools, plus **19,686 task-guide entries** in the bundled catalog. These are instruction references, not 19,686 executable plugins. Guides are matched before work and loaded only when relevant; the new 2,000 spoken-English guides support voice and screen/audio conversations without adding a model or recording behavior.
- **Other workspace features:** saved memories, prompt commands, branching from an earlier prompt, SSH project access, local system information, update notifications, and a plugin/guide library with enable switches.

## Text and vision model choices

These are pinned optional weight downloads. Sizes below are approximate binary GiB for the model file(s), not the VRAM/RAM required while running. Runtime buffers, context/KV cache, other resident models, and the operating system require additional memory. The installer contains the registry, not the weights.

| Arc selector | Model family / quantization | Approx. weights | Current evidence |
| --- | --- | ---: | --- |
| Arc Spark | Qwen3.5 2B, Q4_K_M | 1.22 GiB | Included in the bounded optimizer comparison on this development PC. |
| Arc Swift | Qwen3 4B Instruct 2507, Q4_K_M | 2.33 GiB | Fastest measured decode result in the optimizer sample below; synthetic checks passed 5/5. |
| Arc Focus | Qwen3.5 9B, Q4_K_M | 5.38 GiB | Highest measured overall optimizer score on this RTX 5090 sample; synthetic checks passed 5/5. |
| Arc Core | Qwen3.8 27B dense, Q3_K_S | 11.24 GiB | General chat model; benchmark history is not a broad accuracy study. |
| Arc Scout [Lite] | Qwen3.6 35B-A3B MoE, IQ3_XXS | 12.69 GiB | Lower-memory MoE; see the workflow caveats below. |
| Arc Code [Lite] | Qwen3-Coder 30B-A3B MoE, Q4_K_M | 17.28 GiB | Optional coding model; bounded file-generation checks, no defensible decode-speed figure published. |
| Arc Reason [Lite] | Qwen3.6 35B-A3B MoE, Q4_K_M | 19.78 GiB | Higher-precision MoE; one 32k deep coding workflow passed its recorded checks. |
| Arc Strata [Q2_0] | Strata 125B-class sparse model, Q2_0 | 61.84 GiB | Separate Strata engine; optional, 16k profile, about 115 GiB free storage, and a supported GPU with at least 24 GiB dedicated VRAM required by Arc's setup gate. No Arc benchmark claim. |
| Arc Strata [IQ2_XS] | Strata 125B-class sparse model, IQ2_XS | 63.33 GiB | Same separate engine and eligibility gate; weight size and SSD offload do not promise a particular speed. |
| Vision model | Qwen3-VL 4B Heretic, Q8_0 plus projector | 4.41 GiB combined | Separate optional local vision download; vision quality and speed vary by image and hardware. |
| Voice models | Kokoro-82M synthesis and Faster-Whisper base.en recognition | About 0.47 GiB of principal CPU/ONNX weight files, plus runtime packages | Separate voice setup; GPU synthesis has an alternative ~0.33 GiB weight file plus voice assets. Package/runtime disk space is additional. |
| CPU reviewer | Qwen3 1.7B, Q4_K_M | 1.03 GiB | Optional experimental reviewer, off by default. |

The seven smaller text choices are compatible with Arc's llama.cpp worker paths. Strata starts its own verified Strata server and is never silently routed through llama.cpp. Current Windows inference optimization implements llama.cpp CPU, CUDA, and Vulkan paths. Hardware detection can report other adapters, but Arc does not yet provide working HIP/ROCm, SYCL, Metal, or TensorRT-LLM execution paths in this build. It is not a universal multi-platform release.

## Performance and task-progress report

The recorded optimizer comparison was a real, short inference run on a development PC with a Ryzen 7 9800X3D, RTX 5090, and a **32 GiB configured Arc RAM ceiling**. It tested eight selected installed configurations in about 123 seconds; it did not download every model or test every possible combination. The figures below are model-engine samples, not full app response times.

| Model / backend | Decode | Warm time to first token | Prompt processing | Synthetic checks | Optimizer score |
| --- | ---: | ---: | ---: | ---: | ---: |
| Arc Swift / CUDA | 230.85 tokens/s | 31.5 ms | 1,807.83 tokens/s | 5/5 | 79.66/100 |
| Arc Focus / CUDA | 192.34 tokens/s | 94.0 ms | 723.63 tokens/s | 5/5 | 83.24/100 |

Arc selected Focus in that run because its quality and task-suitability priors contributed to the weighted score; raw decode speed is not the only factor. Swift was faster in that sample. The five small synthetic checks cover narrow behaviors, not general factual accuracy or coding reliability. Prompt processing, model loading, queue waits, speech, vision, and long-context quality are separate costs.

Additional bounded Qwen3.6 MoE coding checks on the RTX 5090 produced mixed results. On one 32k Think deeply coding task, Arc Reason [Lite] (Q4_K_M) finished in 78.97 seconds and passed 11/11 model-authored and 10/10 independent checks. Arc Scout [Lite] (IQ3_XXS) took 148.31 seconds on its 32k coding case; independent checks passed 10/10, but its own tests passed only 5/6 and the overall workflow was marked failed. At 16k, the Q4 case passed independent tests but its own generated assertions included incorrect expectations. Medium arithmetic responses had the right values but failed the JSON-only format requirement. These examples show progress and remaining reliability problems; they do not establish a model-wide accuracy percentage.

For the separate Qwen3-Coder 30B model, bounded tests recorded short-reply success and real PHP/JavaScript file workflows, including a final repair continuation with 10/10 independent checks. Other coding attempts exposed defects (including 8/10 and 9/10 independent checks). Its end-to-end task timings include setup, model loading, tools, and checks; no engine token/sec result was available, so none is claimed.

All measurements are specific to the recorded software, prompts, Windows PC, and test runs. They are not guarantees for another GPU, full context windows, all tasks, sustained heat, or user-visible conversational latency. Arc's progress UI and verification receipts should report incomplete work rather than present a failed workflow as success.

## File size, updates, and privacy

The Windows installer contains the application and bundled runtimes, not the optional text, vision, or speech weights listed above. Model downloads are individually approved and hash-checked. Context, settings, models, and created files are stored locally and are preserved separately from the replaceable program during normal updates. Update metadata is signed and installer downloads are hash-verified; that metadata signature is not a Windows Authenticode publisher signature.

Local chat, file work, speech, and screen/audio analysis run on the user's PC when their local components are selected. Web search, publisher news, connected browser sessions, hosted image generation, and explicitly configured remote memory use their respective internet services. Arc does not upload local conversation history for model training or operate a publisher analytics/crash-report upload service. Review the in-app privacy notice for exact data flows and controls.

## VirusTotal report for this installer

The maintainer supplied [this VirusTotal report](https://www.virustotal.com/gui/file/ce4bcbcddfbf47e3182a596353f8442afd7677592c426856707f2a99c20ea058?nocache=1) and reports that several scanners timed out. Those engines returned an inconclusive result rather than a clean pass or a detection. The report identifies SHA-256 `ce4bcbcddfbf47e3182a596353f8442afd7677592c426856707f2a99c20ea058`; the Arc 0.50.4 installer below has SHA-256 `8fb0ad7e53a2cce196b97433f80779c442ae3b5795f93513c56e529011c9046e`. **These hashes do not match**, so the supplied report is not represented as a scan of this release. A matching scan is pending. VirusTotal results are point-in-time third-party analysis, not a guarantee that software is risk-free.

## Availability

Arc 0.50.4 is a Windows x64 alpha. Setup and use remain in active development; third-party model terms vary, and generated research, code, voice transcripts, and images need user review. The planned $20 one-time product license and website activation flow are not implemented in this build. Hosted Image Studio access is separate from that future license.

> **Installer signature:** Arc-Setup-0.50.4.exe is not Authenticode-signed. The VirusTotal report above is for a different SHA-256 and is not a scan of this installer.
