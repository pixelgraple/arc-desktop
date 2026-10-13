# Arc 0.51.65 — Lower VRAM measurements, design, and workload checks

**Five-mode workload comparison completed.** All five modes passed the requested mechanical checks: web research, a saved screenshot with vision, an exactly 1,000-line file, and post-task model reuse. Manual review confirmed the delivered images, explanations and exact files, while finding material factual and citation limitations in the research answers. Earlier failed attempts remain in the diagnostic record. Installer identity and package checks are recorded separately from these runtime measurements.

Arc now offers four optional Lower VRAM tiers. They compare real inference configurations for the selected model and context, then choose a working configuration within the selected speed allowance. This report measures the trade-offs on one Windows desktop using Arc Focus. It also tests whether Arc finishes useful tasks: web research, a saved screenshot with vision analysis, and an exactly 1,000-line JavaScript file.

**On this PC, Tier 3 provided the better observed trade-off:** its text-generation phase peaked 788 MiB below Normal, with 2.77% lower native token throughput. Tier 4 saved 846 MiB against Normal—only 58 MiB more than Tier 3 in these raw whole-device samples—while generation throughput fell 71.87%, from 187.28 to 52.67 tokens/second. Its file task took 611.56 seconds versus 121.59 seconds in Normal: about 5.03 times as long. That file run also included recovered model-call failures and an unusually slow call, discussed below; the difference cannot all be assigned to CPU placement. The setting does not cap complete task latency. These are one-run observations with different desktop baselines, not precise model-allocation measurements or a universal tier recommendation.

The separate short Tier 4 calibration selected Q8 K/V cache with four CPU-resident blocks and recorded 1,531 MiB less whole-device memory with 73.014% lower generation throughput. That short selection trial is retained with its own scope; it is not substituted for the longer workload results.

Test date: October 12, 2026 in America/Indianapolis; final runs began October 13 at 00:01–00:18 UTC. Release: **Arc 0.51.65**. All final runs identify themselves as 0.51.65. Installer identity and packaged-source verification are recorded separately with the release checksums, because this report itself is bundled inside the application.

## The computer and model

These values were verified from Arc's hardware detection and installed runtime configuration. The matrix records hardware and driver identity again for each mode; measurements from a changed configuration are not silently combined.

| Component | Recorded configuration |
| --- | --- |
| Operating system | 64-bit Windows; the harness did not record the Windows build |
| CPU | AMD Ryzen 7 9800X3D, 8 physical cores / 16 logical threads |
| Selected GPU | NVIDIA GeForce RTX 5090, Blackwell, compute capability 12.0 / SM120 |
| GPU capacity | Nominal 32 GB; detector reported 34,190,917,632 bytes, approximately 31.84 GiB |
| NVIDIA driver | 616.56 |
| CUDA information | Driver API support reported as 13.4; a separately installed CUDA Toolkit version was not detected |
| System RAM visible to Windows | 83,305,525,248 bytes, approximately 77.58 GiB; this is the recorded total, not an assumption of 96 GB |
| Arc text-inference RAM policy | 32 GiB configured ceiling; this does not cap all applications on the PC |
| Other graphics device | AMD Radeon integrated/shared graphics was detected but was not the inference device in this matrix |
| Text model | Arc Focus, 9B-class model, Q4_K_M weights |
| Installed text weight file size | 5,780,090,272 bytes, approximately 5.38 GiB; file size is not runtime memory use |
| Backend | llama.cpp b10590, Windows x64 CUDA 13.3 build; commit `6657ded4faa3b8450221119fc6b4d002e35104a2` |
| Active text context | 16,384 tokens in every mode |
| Vision model / precision | Separate Qwen3-VL-4B-Instruct-heretic, Q8_0 weights: 4,280,407,104 bytes; Q8 projector: 453,974,752 bytes. Vision context 8,192 tokens; maximum image side 1,536 pixels |
| Storage | Local model and task files. The harness did not identify the physical drive model or isolate SSD-specific traffic |

The installed `llama-server.exe` SHA-256 is `8c7a555486f14297192f110fc7f518dcc6aa23097b4e8fbf9363df53fc333c4f`, matching the trusted runtime marker and pinned Arc manifests. CUDA 13.3 is the binary build version; the driver's 13.4 API support is a separate value.

The installed vision recipe was verified separately from the text model. Its pinned catalog revision is `fc66f488427738c8a4ed90a9a3e2c959ea86b0b9`. The vision path exposes duration and status, not native vision tokens/second. Its measured operation includes model handoff/startup and text-model restoration performed inside the image-analysis call; it is not pure vision decode time. Artifact inspection compares its descriptions with the saved pixels separately from whether the call completed.

Reported GPU hardware capabilities are not proof that this test used FP4 or FP8 inference. The matrix compares the actual recorded runtime configuration, not a GPU-name-based assumption. In particular, quantized KV state is different from the installed model's weight quantization.

## What the settings do

The control is in **Settings → AI Performance → Lower VRAM usage**. It is optional and off by default. The selected model, installed weights, and context remain the same. The optimizer tests smaller prompt-processing buffers, supported attention-cache formats, and eligible CPU placement.

| Mode | Maximum generation loss; prompt throughput checked when normal timing is available | Additional options considered |
| --- | ---: | --- |
| Normal | Reference | The selected successful normal configuration |
| Tier 1 — Light | 20% | Smaller buffers, supported Q8 K/V or mixed Q8-key/Q4-value caches |
| Tier 2 — Balanced | 35% | Earlier options plus a small number of CPU model blocks, where supported |
| Tier 3 — Maximum | 50% | Earlier options, more CPU blocks, and supported Q4 K/V cache |
| Tier 4 — Extreme | 75% | Earlier options plus up to three combined Q4 K/V and CPU-placement trials |

Tier 4 is the additional, more tolerant option requested for users willing to trade more speed for memory. Its minimum accepted generation throughput is 25% of the normal comparison; the same floor applies to prompt throughput when the normal prompt timing is available. At that boundary, generating an equal number of tokens could take about four times as long; the percentage is a throughput loss, not a 75% increase in latency. Full tasks also include loading, prompt processing, tools, and network activity.

These limits are **not percentages of VRAM saved**, and they do not guarantee a maximum slowdown on every workload. A higher tier can retain the same configuration as a lower tier. Arc does not intentionally make an answer slower when a faster configuration has equivalent measured savings.

Where selective tensor placement is supported, the trials move a few numbered model blocks to the CPU while retaining the usual GPU output projection. The fallback uses conventional partial GPU-layer offload. Tier 4 tests combined Q4 cache and CPU placement together; it does not add their separate savings and pretend the combination was measured.

The optimizer requires five basic response checks and acceptable measured generation speed. Prompt speed is compared when the normal result has a positive finite prompt rate; a candidate must then supply an acceptable measured rate. Missing normal prompt timing skips that comparison. First-token latency is bounded when both timings are available; missing timings are not a latency pass. Where GPU telemetry is available, a candidate must show at least 32 MiB less whole-device GPU memory. Among candidates within 128 MiB of the largest saving, the existing score prefers the stronger performance/quality result. A short trial rejected only for speed can be reconsidered at a more tolerant tier. Crashes, out-of-memory events, and unhealthy runtime configurations remain excluded.

The five checks are arithmetic, exact instruction following, extraction, JSON formatting, and a tiny JavaScript function. The scoring also contains curated model-suitability priors. **Neither the score nor a 5/5 result is a general accuracy percentage.** These are compatibility and basic-output checks, not an intelligence benchmark or proof that cache quantization has no quality cost.

## How the tests were run

The release harness uses Arc's production model runtime, optimizer, tool loop, bundled plugin/guide manager, web reader, browser controller, saved-file service, task checkpoints, and vision path. Inference, web requests, screenshots, and files are real. It runs with isolated QA state and a dedicated browser profile. It does not import personal browser cookies or passwords, change the user's normal settings, or benchmark through a mocked language model.

The final matrix enables the actual bundled guide-selection path. Its catalog is initialized once before resource measurement; automatic guide selection and reading during each task count toward that task's wall time. Selected guide IDs and guidance hashes are recorded. Optional Agent Reach accounts/executors are not enabled in this harness. Earlier focused and failing runs without this path remain diagnostic evidence only. The final source inventory contains 20 explicitly allowed files, including the catalog, plugin manager, workflow selector, context-excerpt implementation and research-grounding checks. Historical 19-file fingerprints remain diagnostic; they are not treated as the new frozen source.

This is a **production-service test, not an automated end-to-end test of the Electron interface**. UI checks and installer checks have separate scopes below. The research browser may have different site access from a user's signed-in browser.

Each full mode run follows the same sequence:

1. Apply the real tested configuration for Normal or the selected tier. The optimizer may reuse valid calibration records.
2. Record approximately three seconds before explicit model preparation.
3. Prepare a new text worker/server and measure load-to-ready time.
4. Generate three bounded text samples using the identical watershed prompt below.
5. Perform text web research with two sources.
6. Perform research that also saves a real MDN screenshot and uses vision to describe it.
7. Write, append, save, and check an exactly 1,000-line JavaScript file through the normal tool loop.
8. Wait five seconds, request the same prepared text model again, and check whether Arc reuses it.

The optimizer's calibration is separate from the workload matrix. The task-level sampler starts after configuration, so workload tables must not be labeled as the total cost of first-time optimization. The pre-load sample is also not proof that the GPU is completely idle: a prior configuration step or unrelated application can still have allocations.

Web, screenshot research, and file creation use **Ultra with Until done enabled**. Generation samples use Instant mode, grounded sampling, and a 512-token cap. The 700-word wording intentionally keeps generation going to that cap; it is not a 700-word-delivery test. A length-limited generation sample can be valid throughput evidence without being a completed explanation.

The harness permits up to ten minutes for the general workload phases and twenty minutes for the file task. A timeout or an incomplete task remains a failure in the results. The final comparison contains one full run per mode, three generation samples per run, and one instance of each task per mode. All 15 generation samples and all 15 requested web/research/file tasks completed their mechanical checks. This is not dozens of independent task repetitions or a statistical accuracy benchmark.

The final comparison accepts only full runs with identical recorded source hashes. The frozen 20-file workload fingerprint is `1f6f62d9fcd076fec5871c13164fb3759d1cb32e556e089d874d91212330880b`; individual source hashes remain in the evidence package. This fingerprint identifies the measured workload implementation, not every asset in the application. Focused retests and older failing runs are separate diagnostic evidence. A report having a finish timestamp means that the harness ended; it does not mean that every phase passed.

## Six substantial guides for completing the actual task

This release adds six authored workflows with **8,100 words in total**. Each full document contains 1,310–1,390 words with procedures, worked examples, checks and stop conditions. They are available in **Settings → Plugins** by the names below. They instruct the existing tools; they are not six new executable tools or a replacement for the implementation fixes.

| Guide | The behavior it teaches |
| --- | --- |
| Reliable multi-source web research | Read the relevant sources, track which claim each supports, preserve qualifications, distinguish independent evidence from mirrors, and separate search leads from retrieved text. |
| Readable screenshots with verified vision and real attachments | Choose the requested visible page region, verify the saved image receipt, analyze legible pixels, preserve unreadability warnings, and deliver the actual image rather than a webpage URL. |
| Complete research and screenshot requests together | Keep a compact ledger of every deliverable and continue the missing source or screenshot work after another part succeeds; a plan is not a finished answer. |
| Long files in checked chunks without repeated content | Append bounded sections using the current file revision and exact saved ending, verify counts and syntax, avoid duplicate accepted bytes, and retain a continuation checkpoint. |
| Debug the exact file, verify the repair and save it | Reproduce the defect, inspect the correct ranges, apply the demonstrated repair, verify the saved revision and distinguish a syntax check from an executed behavioral test. |
| Resume stalled tasks from evidence and keep models ready | Resume from receipts and unmet requirements, distinguish failed actions from uncertain outcomes, and avoid repeating unchanged calls or needlessly resetting a healthy model. |

Automatic retrieval uses a complete compact task/check/stop recipe. A serialized single-guide selection is 1,811–1,888 characters, including the shared guidance notice, guide identity and JSON formatting; that is not the recipe body's length. The guided diagnostic recorded bodies of 1,177 characters for file completion, 1,143 for readable screenshots and 1,149 for combined research. It does not insert all six long documents into every prompt. The existing context-dependent selection budget still applies; at this matrix’s 16K context, the guide payload budget is 4,800 characters. The model can read the longer matching reference on demand. Guides remain optional reference material: they cannot grant permission, enable a tool, override instructions or prove that the model followed every step.

Routing distinguishes a request for a saved browser screenshot from a live screen-sharing session. Explicit screen/watch/audio intent and real sharing context still select live guidance when relevant. The selection and reader paths retain complete task, verification and failure instructions within the available budget; they do not force extra research just to use a guide. Fresh and upgraded profiles receive the bundled entries while previously saved off switches remain off.

The matrix records which guide IDs and guidance hashes were actually used in each phase. The document count and word count describe the shipped reference material, **not intelligence, accuracy, speed or task-success improvements**. The measured behavior belongs to the combined production implementation and selected guidance; these runs do not isolate a causal improvement from the guides alone.

## Calibration decisions: distinct from the workload comparison

Short 128-token calibration generation and five synthetic quality checks. Whole-device GPU high-water marks; not externally sampled workload-phase peaks.

| Configuration | Measured UTC | Reused | Decode tok/s | Prefill tok/s | First token ms | GPU peak MiB | GPU MiB saved | Decode slowdown | Decision |
| --- | --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | --- |
| Full GPU offload; Smaller prompt batches (128); KV cache: f16 / f16 | 2026-10-12T21:11:24.242Z | Yes | 189.067 | 706.334 | 86 | 7809 | 607 | 2.057% | Passed |
| Full GPU offload; Flash Attention; Smaller prompt batches (128); KV cache: q8_0 / q8_0 | 2026-10-12T21:11:40.023Z | Yes | 184.325 | 718.415 | 85.5 | 7411 | 1005 | 4.514% | Passed |
| Full GPU offload; Flash Attention; Smaller prompt batches (128); KV cache: q8_0 / q4_0 | 2026-10-12T21:11:56.433Z | Yes | 137.348 | 486.684 | 117.5 | 7283 | 1133 | 28.849% | Passed |
| 1 layers in system RAM; Flash Attention; Smaller prompt batches (128); KV cache: q8_0 / q8_0 | 2026-10-12T21:12:13.224Z | Yes | 116.115 | 412.314 | 156 | 7313 | 1103 | 39.848% | Passed |
| 2 layers in system RAM; Flash Attention; Smaller prompt batches (128); KV cache: q8_0 / q8_0 | 2026-10-12T21:12:31.045Z | Yes | 82.493 | 297.362 | 211 | 7175 | 1241 | 57.266% | Passed |
| 4 layers in system RAM; Flash Attention; Smaller prompt batches (128); KV cache: q8_0 / q8_0 | 2026-10-12T21:12:50.769Z | Yes | 52.092 | 199.233 | 328 | 6885 | 1531 | 73.014% | Selected |
| 8 layers in system RAM; Flash Attention; Smaller prompt batches (128); KV cache: q8_0 / q8_0 | 2026-10-12T21:13:15.111Z | Yes | 28.131 | 114.915 | 547 | 6407 | 2009 | 85.427% | Generation was over 75% slower than the normal configuration or could not be measured. |
| Full GPU offload; Flash Attention; Smaller prompt batches (128); KV cache: q4_0 / q4_0 | 2026-10-12T21:13:30.646Z | Yes | 191.152 | 734.656 | 78 | 7285 | 1131 | 0.977% | Passed |
| Full GPU offload | 2026-10-12T22:13:39.297Z | No | 193.037 | 730.221 | 78 | 8416 | 0 | 0% | Passed (baseline) |
| 2 layers in system RAM; Flash Attention; Smaller prompt batches (128); KV cache: q4_0 / q4_0 | 2026-10-12T22:13:57.147Z | No | 83.283 | 293.5 | 211 | 7212 | 1204 | 56.856% | Passed |
| 4 layers in system RAM; Flash Attention; Smaller prompt batches (128); KV cache: q4_0 / q4_0 | 2026-10-12T22:14:17.086Z | No | 53.556 | 202.391 | 328.5 | 6922 | 1494 | 72.256% | The first response was noticeably slower than the normal configuration. |
| 8 layers in system RAM; Flash Attention; Smaller prompt batches (128); KV cache: q4_0 / q4_0 | 2026-10-12T22:14:41.531Z | No | 28.17 | 112.221 | 547.5 | 6591 | 1825 | 85.407% | Generation was over 75% slower than the normal configuration or could not be measured. |

All twelve records passed five short synthetic quality checks. Rejected candidates failed selection policy, not these quality checks.

The fresh Q4/Q4 + 4 CPU-block candidate was rejected at 328.5 ms first-token latency against a 328 ms cutoff (78 ms baseline + 250 ms). The selected earlier Q8/Q8 + 4 CPU-block result was 328 ms. That 0.5 ms difference is a threshold outcome, not proof that Q4 is unusable.

- Some candidates were genuinely benchmarked earlier and reused from the existing profile; timestamps distinguish reused and fresh results.
- Whole-device GPU differences across times include unrelated graphics activity. Treat savings as calibration observations, not precise allocations.
- A selection rejection can be a speed/latency policy rejection, not model initialization or quality failure.
- The full workload matrix independently measures actual chosen tier settings; these short calibration figures must not substitute for it.

## What was actually selected

These settings come from the final workload configuration receipts. The tier names indicate allowed trade-offs; only the recorded selected settings establish what actually ran. Multiple tiers may select the same configuration.

| Mode | Flash Attention | Key / value cache | Placement | Batch / microbatch | Context |
| --- | --- | --- | --- | --- | --- |
| Normal | Off | f16 / f16 | Full GPU offload | 512 / 512 | 16384 |
| Tier 1 | On | q8_0 / q8_0 | Full GPU offload | 512 / 128 | 16384 |
| Tier 2 | On | q8_0 / q8_0 | Full GPU offload | 512 / 128 | 16384 |
| Tier 3 | On | q4_0 / q4_0 | Full GPU offload | 512 / 128 | 16384 |
| Tier 4 | On | q8_0 / q8_0 | 4 CPU blocks; output on GPU | 512 / 128 | 16384 |

## Text speed and memory

The generation speed below is the median of the three native generation rates in the bounded 512-token text phase. It is separate from short calibration throughput. GPU memory is the maximum whole-device sample during that same text-generation phase. The adjacent pre-load median shows the whole-GPU reading during the short interval before explicit model preparation; that interval is not guaranteed to be free of other allocations. Both readings include the desktop and other graphics users, so they are **observed whole-device memory**, not exact model allocations. Savings remain the raw difference between generation peaks, without silently subtracting or normalizing by pre-load readings. These headline savings do not represent the peak across the complete workload. The separate 4B vision model can produce the same or a higher overall GPU peak in every tier; the resource tables below report both per-phase and overall peaks.

| Mode | Load, s | Median tok/s | Speed loss / gain | Pre-load GPU median, MiB | Generation GPU peak, MiB | Raw peak MiB saved | Resident reused |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Normal | 13.41 | 187.28 | Reference | 1,711 | 8,278 | Reference | Yes |
| Tier 1 | 13.34 | 185.38 | 1.01% loss | 2,096 | 7,781 | 497 | Yes |
| Tier 2 | 13.65 | 184.24 | 1.62% loss | 2,008 | 7,927 | 351 | Yes |
| Tier 3 | 13.33 | 182.09 | 2.77% loss | 1,889 | 7,490 | 788 | Yes |
| Tier 4 | 13.25 | 52.67 | 71.87% loss | 2,210 | 7,432 | 846 | Yes |

Generation speed loss is `(1 − tier tok/s ÷ normal tok/s) × 100`, shown as a positive loss when the tier is slower. A faster result is labeled as a gain, never a negative loss. Memory saved is `normal peak MiB − tier peak MiB`; retain a negative value if the sample used more memory. Neither value is forced to appear as an improvement.

The median is useful for a small comparison, but three samples on one computer are not enough to promise a stable percentage to every user. The downloadable evidence retains the individual samples and their measurement scope. Tier 1 and Tier 2 selected identical runtime settings yet had different raw memory peaks; desktop allocations, sampling and workload variability remain confounders. No pre-load baseline was silently subtracted.

The separate vision model is unchanged by these text tiers. Overall workload peaks ranged from 8,706 to 9,140 MiB, with Tier 4 at 8,996 MiB. Those overall values include browser, vision and restored text work; the generation-phase savings are not a claim that vision allocations were reduced.

## Did Arc finish the tasks?

Task status and wall time are shown together. An unsuccessful attempt that stops early is not a faster completed task.

| Mode | Web research | Research + screenshot + vision | 1,000-line file |
| --- | --- | --- | --- |
| Normal | Passed · 26.98 s | Passed · 60.09 s | Passed · 121.59 s |
| Tier 1 | Passed · 30.23 s | Passed · 67.66 s | Passed · 119.46 s |
| Tier 2 | Passed · 20.11 s | Passed · 63.81 s | Passed · 117.69 s |
| Tier 3 | Passed · 21.35 s | Passed · 62.85 s | Passed · 117.47 s |
| Tier 4 | Passed · 60.73 s | Passed · 116.54 s | Passed · 611.56 s |

The file check reads the actual saved bytes. It requires 1,000 lines in order, with each expected numbered declaration, and a successful JavaScript parse. It does not execute the file because the prompt explicitly says execution is not needed. This is a strong delivery/continuation check for this particular contract, not a demonstration that Arc can correctly implement any 1,000-line application.

For research, the harness checks successful search activity, source receipts, a saved JPEG where requested, and completion flags. The aggregator separately exposes screenshot and vision-tool receipts, source counts, and substantive reply length. These are mechanical checks. Two URLs can be mirrors of the same publisher and do not establish independent corroboration. A completed workflow can still omit callback side effects or misread screenshot code. The separate artifact review compares saved pixels, vision descriptions, cited pages, and the explanation of mutation/callback behavior. A search result card is a lead; it is not proof that a page was read or that a claim is true.

**Final web and screenshot review:** all five frozen-source modes delivered a real, readable viewport image, an actual vision call and a substantive final research answer. That confirms delivery, not full factual accuracy. All five overgeneralized that `map` leaves the original array unchanged without adequately qualifying callback side effects. Several second URLs were MDN mirrors rather than independent publishers. Tier 1 and Tier 3 web answers cited search leads instead of their successful second reads; Tier 4's web prose generated an incorrect MDN link even though the source card was correct. Tier 4's screenshot research used a Stack Overflow indexed fallback without explaining that distinction in its prose. Vision also sometimes added a general claim or paraphrase that was not visible in the capture. These limitations remain in the evidence; vision is not treated as independent fact verification. Independent file review confirmed the exact same 38,785 UTF-8 bytes in every mode: 1,000 unique declarations in order, 1,000 physical lines, a successful independent Acorn parse, and SHA-256 `0427076307bc047f7885df6558aa0aaf5d56423c7d5c134cd76c2bc8bda94fe7`. No generated code was executed. This validates the literal file contract, not arbitrary application behavior. Tier 4 also recovered an invalid browser-action argument before obtaining its real capture.

Final completion does not mean every intermediate call succeeded. Normal file creation recovered one `ARC_INCOMPLETE` model call. Tier 4 recovered two model-call failures (`AbortError` and `ARC_INCOMPLETE`) and included one 197.518-second call with 9.078 native generation tokens/second. Its file-phase decode median was 45.084 tokens/second, while its token/time-weighted rate was 32.182; median prefill was 1,809.57 tokens/second versus Normal's 7,771.33. These longer-task measurements are distinct from the three short generation samples. The 611.556-second total includes those calls, recovery, prompts and file checks; it cannot be attributed solely to CPU offload. Tier 4's file phase also recorded 78.83% mean whole-system CPU activity and approximately 1.42 GiB system disk reads / 9.87 GiB writes, compared with 0.091 GiB of owned-process logical reads. The system counters include other software and OS activity; they do not establish Arc SSD offload, model-only disk use or a measured paging cause. The final file still matched the exact requested bytes.

### Manual delivery and accuracy review

These findings use the exact final-run artifact review, separately from harness flags. A delivered answer can still have factual limitations; a successful screenshot or source read cannot make a plan-only answer complete. Unreviewed output remains unreviewed.

| Mode | Task | Delivery review | Bounded conclusion | Recorded limitations |
| --- | --- | --- | --- | --- |
| Normal | web | Delivery reviewed as complete | Substantive sourced explanation delivered with remaining technical precision limitations. | The claim that source-array length/elements always remain unchanged is too broad: callbacks can also mutate the original array itself.<br>The primitive-value summary could imply input replacement rather than values in the returned array. |
| Normal | research | Delivery reviewed as complete | Requested tools, image and researched answer delivered; not a perfect factual or verbatim visual-grounding score. | Research cites MDN and its own GitHub source: two websites but not independent publications; calling the repository a secondary source overstates corroboration.<br>The answer overstates original-array preservation and omits callback side effects. A variable named squared actually holds doubled values; the computed output is correct.<br>Vision adds a Creates new array comment not literally visible in the source image and adds an unqualified no-mutation claim. The final claim that the screenshot shows the input remains unchanged is inference from code, not an observed input readback. |
| Normal | file | Delivery reviewed as complete | Exact file delivery and independent syntax verification passed. | This task checks literal 1000-declaration delivery and syntax, not complex program design or runtime behavior.<br>The delivered file passed after 1 model-call errors (ARC_INCOMPLETE). Final success does not mean every attempt succeeded. |
| Tier 1 | web | Delivery reviewed as complete | Substantive answer delivered, with source-provenance and wording limitations. | The second final citation is a StackOverflow search lead, not one of the two successful full-text reads. The actual second read was MDN content on GitHub.<br>The opening unqualified nonmutation claim is broader than the later callback caveat; two actual read hosts still carry the same publication. |
| Tier 1 | research | Delivery reviewed as complete | Capture, visible-example analysis and researched-answer delivery passed, with imperfect source attribution and factual scope. | The final answer quotes the vision paraphrase that map does not mutate as an explicit source note, although that sentence is not visible in the screenshot.<br>No callback-side-effect caveat qualifies the general nonmutation claim.<br>MDN, its GitHub source and GetDocs carry the same underlying publication; host count is not independent corroboration. |
| Tier 1 | file | Delivery reviewed as complete | Exact file delivery and independent syntax verification passed. | This task checks literal 1000-declaration delivery and syntax, not complex program design or runtime behavior. |
| Tier 2 | web | Delivery reviewed as complete | Substantive sourced answer delivered, with callback-mutation and independence limitations. | The answer overgeneralizes nonmutation for map and forEach without callback-side-effect caveats.<br>MDN and its GitHub repository are the same publication, not independent corroboration. |
| Tier 2 | research | Delivery reviewed as complete | Capture, observed numeric example and answer delivery passed; scope and quotation precision remain imperfect. | Final prose presents the vision no-mutation paraphrase as a literal MDN statement, although it is not visible in the screenshot.<br>The no-mutation guarantee omits callback side effects. The claimed visible input preservation is inferred from code rather than an actual readback of the original array. |
| Tier 2 | file | Delivery reviewed as complete | Exact file delivery and independent syntax verification passed. | This task checks literal 1000-declaration delivery and syntax, not complex program design or runtime behavior. |
| Tier 3 | web | Delivery reviewed as complete | Substantive answer delivered, with citation-provenance and factual-scope limitations. | Final prose cites DigitalOcean and StackOverflow search leads as confirmation, whereas actual text reads were MDN, its GitHub source and UNB mirror.<br>The no-mutation guarantee omits callback side effects. Three actual read hosts contain one publication. |
| Tier 3 | research | Delivery reviewed as complete | Requested capture and researched-answer delivery passed, with semantic caveats. | The general nonmutation claim ignores callbacks that alter the input or shared objects. A claim that output length matches input needs the initial-length scope if callbacks change input length.<br>The visual conclusion that the input is unchanged is inferred from code rather than directly observed in a readback.<br>MDN, its GitHub source and UNB mirror are one publication, not independent corroboration. |
| Tier 3 | file | Delivery reviewed as complete | Exact file delivery and independent syntax verification passed. | This task checks literal 1000-declaration delivery and syntax, not complex program design or runtime behavior. |
| Tier 4 | web | Delivery reviewed as complete | Substantive explanation delivered with a generated-link error and scope caveats. | The generated MDN inline link incorrectly uses Global/Objects instead of Global_Objects; the verified source card is correct.<br>Opening and summary statements about unchanged input are broader than the callback caveat. Two read hosts are the same publication. |
| Tier 4 | research | Delivery reviewed as complete | Requested capture, sources and researched answer delivered, with citation-retrieval and factual-scope limitations. | Final prose does not disclose that StackOverflow evidence was indexed fallback content rather than a verified live-page read.<br>The nonmutation guarantee omits callback side effects, despite the example itself being correct.<br>Raw vision adds a general nonmutation conclusion beyond the visible text; image inference is not independent fact verification. |
| Tier 4 | file | Delivery reviewed as complete | Exact file delivery and independent syntax verification passed. | This task checks literal 1000-declaration delivery and syntax, not complex program design or runtime behavior.<br>The delivered file passed after 2 model-call errors (AbortError, ARC_INCOMPLETE). Final success does not mean every attempt succeeded.<br>The full file task took 611.556 seconds; its slowest completed call took 197.518 seconds at 9.078 native tokens/second. This outlier is retained; a fixed-sample decode slowdown target does not cap full task time. |

## CPU, RAM, disk, and GPU activity

### Sample variation and whole-workload peaks

Overall memory peaks below include vision and browser work. Text-tier savings do not imply lower vision memory.

| Mode | Generation range, tok/s | Samples | Weighted prefill tok/s | Median native TTFT, ms | Overall GPU peak, MiB | Owned RSS peak, GiB | Owned private commit peak, GiB | System RAM peak, GiB |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Normal | 186.41–187.40 | 3 | 256.45 | 47.00 | 9,140 | 6.86 | 9.27 | 25.55 |
| Tier 1 | 183.77–185.40 | 3 | 393.68 | 46.00 | 8,842 | 6.61 | 8.77 | 25.22 |
| Tier 2 | 183.73–185.63 | 3 | 380.50 | 47.00 | 8,906 | 6.62 | 8.72 | 26.30 |
| Tier 3 | 181.99–182.42 | 3 | 384.90 | 47.00 | 8,706 | 6.60 | 8.77 | 25.23 |
| Tier 4 | 51.39–52.69 | 3 | 154.54 | 78.00 | 8,996 | 6.98 | 8.81 | 29.71 |

### Per-phase GPU observations

| Mode | Phase | Wall, s | GPU peak, MiB | GPU activity mean / peak, % | Memory-controller activity mean / peak, % | Temp peak, °C | Power peak, W | Samples | Largest gap, s |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Normal | idle-before | 3.01 | 1,711 | 0.00 / 0 | 0.00 / 0 | 41 | 38.03 | 3 | 1.01 |
| Normal | cold-load | 13.41 | 7,148 | 1.07 / 5 | 0.00 / 0 | 43 | 68.27 | 14 | 1.01 |
| Normal | generation | 8.59 | 8,278 | 81.12 / 85 | 68.75 / 72 | 57 | 407.11 | 8 | 1.00 |
| Normal | web | 26.98 | 8,622 | 49.41 / 95 | 37.56 / 73 | 60 | 409.54 | 27 | 1.01 |
| Normal | research | 60.09 | 9,140 | 40.87 / 95 | 21.75 / 77 | 64 | 405.38 | 60 | 1.01 |
| Normal | file | 121.59 | 9,135 | 78.52 / 94 | 62.51 / 73 | 72 | 413.79 | 122 | 1.00 |
| Normal | resident-after | 5.01 | 8,785 | 1.40 / 3 | 0.80 / 4 | 61 | 197.08 | 5 | 1.01 |
| Tier 1 | idle-before | 3.01 | 2,096 | 0.00 / 0 | 3.00 / 5 | 55 | 69.70 | 3 | 1.00 |
| Tier 1 | cold-load | 13.34 | 7,767 | 1.07 / 5 | 6.29 / 10 | 54 | 76.90 | 14 | 1.01 |
| Tier 1 | generation | 8.55 | 7,781 | 82.12 / 84 | 69.12 / 71 | 66 | 408.05 | 8 | 1.01 |
| Tier 1 | web | 30.23 | 7,905 | 52.90 / 85 | 37.13 / 72 | 67 | 411.74 | 30 | 1.01 |
| Tier 1 | research | 67.66 | 8,842 | 39.63 / 86 | 21.62 / 72 | 69 | 403.22 | 68 | 1.00 |
| Tier 1 | file | 119.46 | 8,342 | 78.94 / 87 | 60.54 / 71 | 73 | 413.42 | 119 | 1.01 |
| Tier 1 | resident-after | 5.03 | 7,675 | 1.40 / 2 | 1.60 / 4 | 64 | 290.70 | 5 | 1.00 |
| Tier 2 | idle-before | 3.02 | 2,035 | 4.33 / 9 | 8.00 / 15 | 56 | 52.62 | 3 | 1.00 |
| Tier 2 | cold-load | 13.65 | 7,454 | 1.79 / 5 | 1.71 / 6 | 56 | 78.93 | 14 | 1.00 |
| Tier 2 | generation | 8.57 | 7,927 | 82.78 / 84 | 69.22 / 71 | 67 | 408.61 | 9 | 1.00 |
| Tier 2 | web | 20.11 | 7,890 | 53.20 / 86 | 35.75 / 72 | 68 | 406.77 | 20 | 1.00 |
| Tier 2 | research | 63.81 | 8,906 | 41.11 / 85 | 21.67 / 72 | 70 | 411.06 | 63 | 1.01 |
| Tier 2 | file | 117.69 | 7,953 | 79.76 / 86 | 61.80 / 72 | 73 | 413.07 | 118 | 1.01 |
| Tier 2 | resident-after | 5.01 | 7,679 | 1.00 / 1 | 0.00 / 0 | 64 | 220.37 | 5 | 1.00 |
| Tier 3 | idle-before | 3.01 | 1,889 | 2.33 / 3 | 5.33 / 8 | 57 | 58.31 | 3 | 1.00 |
| Tier 3 | cold-load | 13.33 | 7,414 | 3.21 / 6 | 5.36 / 9 | 56 | 81.63 | 14 | 1.00 |
| Tier 3 | generation | 8.67 | 7,490 | 84.75 / 86 | 69.62 / 71 | 67 | 406.93 | 8 | 1.00 |
| Tier 3 | web | 21.35 | 7,502 | 55.64 / 86 | 39.09 / 70 | 69 | 411.49 | 22 | 1.00 |
| Tier 3 | research | 62.85 | 8,706 | 40.35 / 87 | 20.36 / 77 | 70 | 411.66 | 62 | 1.00 |
| Tier 3 | file | 117.47 | 8,082 | 81.36 / 87 | 62.87 / 71 | 73 | 413.14 | 118 | 1.00 |
| Tier 3 | resident-after | 5.01 | 7,930 | 0.00 / 0 | 0.00 / 0 | 64 | 227.89 | 5 | 1.00 |
| Tier 4 | idle-before | 3.01 | 2,210 | 0.00 / 0 | 0.67 / 2 | 57 | 71.86 | 3 | 1.00 |
| Tier 4 | cold-load | 13.25 | 7,378 | 1.00 / 5 | 7.00 / 55 | 56 | 77.21 | 14 | 1.00 |
| Tier 4 | generation | 29.95 | 7,432 | 21.53 / 24 | 17.50 / 20 | 59 | 179.30 | 30 | 1.00 |
| Tier 4 | web | 60.73 | 7,450 | 27.18 / 72 | 13.65 / 21 | 61 | 221.38 | 60 | 1.00 |
| Tier 4 | research | 116.54 | 8,996 | 31.57 / 71 | 9.73 / 24 | 61 | 363.77 | 117 | 1.01 |
| Tier 4 | file | 611.56 | 8,046 | 19.03 / 82 | 12.11 / 80 | 58 | 213.14 | 569 | 3.42 |
| Tier 4 | resident-after | 5.01 | 7,853 | 0.00 / 0 | 0.00 / 0 | 51 | 61.91 | 5 | 1.00 |

### Per-phase host observations

| Mode | Phase | Wall, s | Owned RSS peak, GiB | Owned commit peak, GiB | System RAM used peak, GiB | Owned CPU seconds | Owned mean CPU cores | System CPU mean / peak, % |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Normal | idle-before | 3.01 | 0.45 | 0.46 | 19.36 | 0.02 | 0.01 | 9.00 / 10.20 |
| Normal | cold-load | 13.41 | 5.31 | 5.91 | 24.23 | 14.72 | 1.13 | 18.78 / 26.90 |
| Normal | generation | 8.59 | 5.88 | 8.27 | 24.80 | 7.77 | 1.11 | 18.91 / 23.50 |
| Normal | web | 26.98 | 6.02 | 8.38 | 24.98 | 25.88 | 0.99 | 18.87 / 29.90 |
| Normal | research | 60.09 | 6.86 | 9.27 | 25.55 | 59.84 | 1.01 | 22.95 / 45.30 |
| Normal | file | 121.59 | 6.76 | 8.94 | 25.38 | 129.70 | 1.07 | 15.92 / 34.20 |
| Normal | resident-after | 5.01 | 6.86 | 9.03 | 25.38 | 0.61 | 0.15 | 10.22 / 15.30 |
| Tier 1 | idle-before | 3.01 | 0.45 | 0.45 | 19.41 | 0.02 | 0.01 | 3.10 / 3.30 |
| Tier 1 | cold-load | 13.34 | 5.43 | 6.60 | 24.38 | 14.75 | 1.13 | 10.25 / 20.90 |
| Tier 1 | generation | 8.55 | 5.64 | 7.08 | 24.61 | 7.89 | 1.12 | 17.16 / 21.40 |
| Tier 1 | web | 30.23 | 5.78 | 7.09 | 24.74 | 28.59 | 0.98 | 14.11 / 19.10 |
| Tier 1 | research | 67.66 | 6.61 | 8.77 | 25.21 | 65.17 | 0.97 | 21.05 / 40.10 |
| Tier 1 | file | 119.46 | 6.53 | 7.61 | 25.22 | 124.20 | 1.05 | 15.98 / 33.40 |
| Tier 1 | resident-after | 5.03 | 6.51 | 7.59 | 24.95 | 0.06 | 0.02 | 5.02 / 10.60 |
| Tier 2 | idle-before | 3.02 | 0.45 | 0.46 | 19.34 | 0.02 | 0.01 | 13.25 / 16.10 |
| Tier 2 | cold-load | 13.65 | 4.82 | 5.88 | 24.33 | 15.09 | 1.16 | 25.51 / 35.40 |
| Tier 2 | generation | 8.57 | 5.60 | 7.04 | 25.15 | 9.25 | 1.16 | 15.41 / 19.30 |
| Tier 2 | web | 20.11 | 5.75 | 7.01 | 25.24 | 16.22 | 0.85 | 13.19 / 24.70 |
| Tier 2 | research | 63.81 | 6.62 | 8.72 | 26.30 | 63.30 | 1.02 | 19.94 / 37.10 |
| Tier 2 | file | 117.69 | 6.55 | 7.62 | 25.71 | 126.61 | 1.08 | 22.61 / 66.40 |
| Tier 2 | resident-after | 5.01 | 6.49 | 7.58 | 25.49 | 0.03 | 0.01 | 29.82 / 45.70 |
| Tier 3 | idle-before | 3.01 | 0.45 | 0.46 | 19.20 | 0.02 | 0.01 | 3.60 / 4.00 |
| Tier 3 | cold-load | 13.33 | 5.37 | 5.88 | 24.12 | 14.73 | 1.13 | 12.03 / 23.00 |
| Tier 3 | generation | 8.67 | 5.60 | 6.91 | 24.35 | 7.91 | 1.13 | 14.41 / 22.20 |
| Tier 3 | web | 21.35 | 5.75 | 6.89 | 24.56 | 17.53 | 0.83 | 13.06 / 19.20 |
| Tier 3 | research | 62.85 | 6.60 | 8.77 | 25.05 | 61.48 | 1.01 | 18.77 / 40.80 |
| Tier 3 | file | 117.47 | 6.51 | 7.46 | 25.23 | 125.67 | 1.07 | 16.81 / 36.80 |
| Tier 3 | resident-after | 5.01 | 6.50 | 7.45 | 25.21 | 0.17 | 0.04 | 6.94 / 9.80 |
| Tier 4 | idle-before | 3.01 | 0.45 | 0.46 | 19.55 | 0.02 | 0.01 | 4.60 / 6.50 |
| Tier 4 | cold-load | 13.25 | 5.46 | 6.37 | 24.52 | 16.70 | 1.28 | 15.04 / 33.40 |
| Tier 4 | generation | 29.95 | 5.60 | 6.52 | 24.69 | 230.92 | 7.96 | 55.03 / 59.70 |
| Tier 4 | web | 60.73 | 5.76 | 6.52 | 24.76 | 338.39 | 5.73 | 42.47 / 74.00 |
| Tier 4 | research | 116.54 | 6.53 | 8.81 | 26.69 | 419.55 | 3.61 | 51.05 / 99.20 |
| Tier 4 | file | 611.56 | 6.98 | 7.28 | 29.71 | 3,271.59 | 5.36 | 78.83 / 100.00 |
| Tier 4 | resident-after | 5.01 | 6.46 | 7.04 | 29.42 | 0.08 | 0.02 | 18.76 / 27.70 |

### Per-phase I/O observations

Logical process counters and whole-system disk deltas are different scopes; neither is a direct measurement of Arc-only SSD inference traffic.

| Mode | Phase | Owned logical reads, MiB | Owned logical writes, MiB | System disk reads, MiB | System disk writes, MiB |
| --- | --- | --- | --- | --- | --- |
| Normal | idle-before | 0.00 | 0.00 | 1.89 | 3.35 |
| Normal | cold-load | 6,107.06 | 0.00 | 5.26 | 3.81 |
| Normal | generation | 0.07 | 0.08 | 2.32 | 1.00 |
| Normal | web | 20.64 | 0.21 | 7.49 | 16.20 |
| Normal | research | 5,033.34 | 35.43 | 2.58 | 100.24 |
| Normal | file | 22.12 | 0.92 | 2.24 | 61.61 |
| Normal | resident-after | 0.13 | 0.01 | 0.02 | 1.05 |
| Tier 1 | idle-before | 0.00 | 0.00 | 0.33 | 4.44 |
| Tier 1 | cold-load | 6,105.41 | 0.00 | 0.00 | 2.26 |
| Tier 1 | generation | 0.07 | 0.08 | 0.34 | 2.34 |
| Tier 1 | web | 20.65 | 0.22 | 0.02 | 19.38 |
| Tier 1 | research | 5,032.95 | 35.30 | 1.09 | 104.37 |
| Tier 1 | file | 4.82 | 3.97 | 0.70 | 65.10 |
| Tier 1 | resident-after | 0.00 | 0.00 | 0.00 | 1.91 |
| Tier 2 | idle-before | 0.00 | 0.00 | 0.00 | 2.71 |
| Tier 2 | cold-load | 6,106.97 | 0.00 | 0.21 | 17.59 |
| Tier 2 | generation | 0.08 | 0.09 | 0.40 | 29.09 |
| Tier 2 | web | 0.30 | 0.16 | 0.26 | 7.89 |
| Tier 2 | research | 5,032.09 | 34.57 | 1.20 | 91.93 |
| Tier 2 | file | 1.82 | 0.91 | 9.65 | 242.07 |
| Tier 2 | resident-after | 0.01 | 0.01 | 0.01 | 4.00 |
| Tier 3 | idle-before | 0.00 | 0.00 | 0.00 | 4.04 |
| Tier 3 | cold-load | 6,103.79 | 0.00 | 0.19 | 1.53 |
| Tier 3 | generation | 0.07 | 0.08 | 0.00 | 0.77 |
| Tier 3 | web | 0.31 | 0.17 | 0.00 | 11.67 |
| Tier 3 | research | 5,032.43 | 34.70 | 0.46 | 71.86 |
| Tier 3 | file | 1.82 | 0.91 | 1.79 | 64.67 |
| Tier 3 | resident-after | 0.00 | 0.01 | 0.00 | 1.04 |
| Tier 4 | idle-before | 0.00 | 0.00 | 0.00 | 3.67 |
| Tier 4 | cold-load | 6,099.53 | 0.00 | 0.04 | 4.81 |
| Tier 4 | generation | 0.09 | 0.10 | 0.00 | 5.28 |
| Tier 4 | web | 20.60 | 0.17 | 0.17 | 9.79 |
| Tier 4 | research | 5,020.72 | 21.00 | 40.13 | 215.49 |
| Tier 4 | file | 93.54 | 16.06 | 1,454.04 | 10,104.65 |
| Tier 4 | resident-after | 0.00 | 0.00 | 0.00 | 22.84 |

### Screenshot/vision receipts

Image-analysis operation wall time can include internal model handoff/startup and text-model restoration. Native vision tok/s was unavailable; completion alone does not establish visual accuracy. Source cards can include search leads and are not equivalent to page reads.

| Mode | Vision attempts | Completed | Failed | Vision operation total, s | Source cards | Card hosts | Screenshot saved |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Normal | 1 | 1 | 0 | 19.11 | 5 | 3 | Yes |
| Tier 1 | 1 | 1 | 0 | 18.87 | 10 | 6 | Yes |
| Tier 2 | 1 | 1 | 0 | 18.42 | 5 | 3 | Yes |
| Tier 3 | 1 | 1 | 0 | 18.55 | 10 | 6 | Yes |
| Tier 4 | 1 | 1 | 0 | 18.39 | 6 | 3 | Yes |

### Actual source-read receipts

Read receipts are separate from source cards. Multiple hosts can carry the same publication; even a fulfilled read contract does not establish factual accuracy or successful final-answer delivery.

| Mode | Task | Search completed | Reads | Read hosts | Source work incomplete |
| --- | --- | --- | --- | --- | --- |
| Normal | web | Yes | 4 | 4 | No |
| Normal | research | Yes | 2 | 2 | No |
| Tier 1 | web | Yes | 2 | 2 | No |
| Tier 1 | research | Yes | 3 | 3 | No |
| Tier 2 | web | Yes | 2 | 2 | No |
| Tier 2 | research | Yes | 3 | 3 | No |
| Tier 3 | web | Yes | 3 | 3 | No |
| Tier 3 | research | Yes | 3 | 3 | No |
| Tier 4 | web | Yes | 2 | 2 | No |
| Tier 4 | research | Yes | 3 | 3 | No |

### Automatically selected bundled guides

The catalog is initialized before resource measurement; actual guide selection/reading is included in each task wall time. IDs and hashes identify each selected compact guidance body. Body character counts exclude the shared notice, identity and JSON envelope. Earlier no-guide diagnostics are not final matrix evidence.

| Mode | Task | Guide ID | Body characters | Body SHA-256 |
| --- | --- | --- | --- | --- |
| Normal | web | arc-workflow-mdn-api-usage | 1647 | a236cd61a312ac59271a8602c7ff129f9b54de01f83439e748f271e565963b60 |
| Normal | web | arc-workflow-task-data-extraction-07-explain | 1595 | b724b731c00a2ff0bfa5e4e8016020f26bfd3173b5a85abd4c0f73f3e26d7026 |
| Normal | research | arc-workflow-reliable-readable-screenshots | 1143 | 1cf321ac115d090e324f6f12a89f80885bf21616cb21102d9531e6cbcd74e266 |
| Normal | research | arc-workflow-reliable-research-with-images | 1149 | eda80694e3529908feb460a6ff358cfa5d2703c3fb6cf26627b1b7e07c4eeef6 |
| Normal | file | arc-workflow-reliable-chunked-file-completion | 1177 | 0c74b874415cfc76bb4394d6338f16e4dedb3c64aaedbdbd13b2d4817464c607 |
| Tier 1 | web | arc-workflow-mdn-api-usage | 1647 | a236cd61a312ac59271a8602c7ff129f9b54de01f83439e748f271e565963b60 |
| Tier 1 | web | arc-workflow-task-data-extraction-07-explain | 1595 | b724b731c00a2ff0bfa5e4e8016020f26bfd3173b5a85abd4c0f73f3e26d7026 |
| Tier 1 | research | arc-workflow-reliable-readable-screenshots | 1143 | 1cf321ac115d090e324f6f12a89f80885bf21616cb21102d9531e6cbcd74e266 |
| Tier 1 | research | arc-workflow-reliable-research-with-images | 1149 | eda80694e3529908feb460a6ff358cfa5d2703c3fb6cf26627b1b7e07c4eeef6 |
| Tier 1 | file | arc-workflow-reliable-chunked-file-completion | 1177 | 0c74b874415cfc76bb4394d6338f16e4dedb3c64aaedbdbd13b2d4817464c607 |
| Tier 2 | web | arc-workflow-mdn-api-usage | 1647 | a236cd61a312ac59271a8602c7ff129f9b54de01f83439e748f271e565963b60 |
| Tier 2 | web | arc-workflow-task-data-extraction-07-explain | 1595 | b724b731c00a2ff0bfa5e4e8016020f26bfd3173b5a85abd4c0f73f3e26d7026 |
| Tier 2 | research | arc-workflow-reliable-readable-screenshots | 1143 | 1cf321ac115d090e324f6f12a89f80885bf21616cb21102d9531e6cbcd74e266 |
| Tier 2 | research | arc-workflow-reliable-research-with-images | 1149 | eda80694e3529908feb460a6ff358cfa5d2703c3fb6cf26627b1b7e07c4eeef6 |
| Tier 2 | file | arc-workflow-reliable-chunked-file-completion | 1177 | 0c74b874415cfc76bb4394d6338f16e4dedb3c64aaedbdbd13b2d4817464c607 |
| Tier 3 | web | arc-workflow-mdn-api-usage | 1647 | a236cd61a312ac59271a8602c7ff129f9b54de01f83439e748f271e565963b60 |
| Tier 3 | web | arc-workflow-task-data-extraction-07-explain | 1595 | b724b731c00a2ff0bfa5e4e8016020f26bfd3173b5a85abd4c0f73f3e26d7026 |
| Tier 3 | research | arc-workflow-reliable-readable-screenshots | 1143 | 1cf321ac115d090e324f6f12a89f80885bf21616cb21102d9531e6cbcd74e266 |
| Tier 3 | research | arc-workflow-reliable-research-with-images | 1149 | eda80694e3529908feb460a6ff358cfa5d2703c3fb6cf26627b1b7e07c4eeef6 |
| Tier 3 | file | arc-workflow-reliable-chunked-file-completion | 1177 | 0c74b874415cfc76bb4394d6338f16e4dedb3c64aaedbdbd13b2d4817464c607 |
| Tier 4 | web | arc-workflow-mdn-api-usage | 1647 | a236cd61a312ac59271a8602c7ff129f9b54de01f83439e748f271e565963b60 |
| Tier 4 | web | arc-workflow-task-data-extraction-07-explain | 1595 | b724b731c00a2ff0bfa5e4e8016020f26bfd3173b5a85abd4c0f73f3e26d7026 |
| Tier 4 | research | arc-workflow-reliable-readable-screenshots | 1143 | 1cf321ac115d090e324f6f12a89f80885bf21616cb21102d9531e6cbcd74e266 |
| Tier 4 | research | arc-workflow-reliable-research-with-images | 1149 | eda80694e3529908feb460a6ff358cfa5d2703c3fb6cf26627b1b7e07c4eeef6 |
| Tier 4 | file | arc-workflow-reliable-chunked-file-completion | 1177 | 0c74b874415cfc76bb4394d6338f16e4dedb3c64aaedbdbd13b2d4817464c607 |

The resource measurements have these distinct meanings:

| Metric | What the report can support |
| --- | --- |
| GPU memory peak | Highest observed whole-device memory sample in the phase |
| Pre-load GPU median | Typical whole-device reading before explicit preparation; not an empty-GPU guarantee, model allocation or silently subtracted baseline |
| GPU utilization, mean / peak | Sampled device activity; not model-only kernel occupancy |
| GPU memory-controller utilization, mean / peak | Fraction of sampling time device memory was being accessed; **not** percentage of VRAM capacity used |
| GPU temperature and power | Sampled whole-device values; power alone does not establish model energy efficiency |
| Owned-process RSS peak | Harness and observed descendant processes, including model/browser workers; shared mappings may be counted more than once |
| Owned-process private commit peak | Private committed memory, reported separately from RSS; never add the two |
| Whole-system RAM used peak | Physical RAM used by the entire PC, including unrelated programs and OS cache |
| CPU seconds / core-equivalents | CPU time summed over observed owned processes; core-equivalents divide by sampled duration |
| Whole-system CPU mean / peak | System CPU utilization, distinct from the owned-process CPU measurement |
| Logical process reads / writes | Process I/O counters, which can include non-file activity and cached reads |
| Whole-system disk reads / writes | Deltas across all disks and applications; not isolated Arc SSD traffic |
| Native prefill tok/s and first token | Backend token-processing timings, distinct from time to a finished user-visible reply |
| Vision duration / call status | Image-analysis calls shown separately from language-model generation |

Native allocator buffers and the model's exclusive GPU allocation were not archived by this harness. The GPU tables therefore use raw whole-device peaks and pre-load medians. They do not substitute planning estimates such as `estimatedVramBytes`, or silently subtract the pre-load reading to manufacture a precise model-memory figure. Different desktop allocations and sampling times can affect the raw differences.

Telemetry is sampled approximately once per second. The tables show sample count and largest sampling gap. Fast spikes, short-lived processes, and activity before the first or after the last in-phase sample can be missed. Worker lifetime high-water marks are not reused as per-phase peaks; external samples establish the phase tables.

Native generation tok/s is generated tokens divided by native generation time. It excludes browser navigation, searches, downloads, saving, and most application overhead. For tool-enabled turns, generated tokens can include reasoning and structured arguments, not just words visible in chat. Prompt-processing speed changes with input length and prefix caching. A median of per-call rates and a token/time-weighted rate are different statistics; label whichever is shown.

Native first-token latency differs from first public content. Tool output can be buffered until a valid call or final response is complete. Full task wall time includes reasoning, repeated prompts, network waits, screenshot handling, vision model transitions, validation, and file writing. It is the more relevant measure of how long a user waits for the requested deliverable.

“Cold load” means a new Arc model worker/server. The operating-system file cache was not flushed, so it is not a guaranteed cold-disk test. Resident reuse means the prepared text model was reused after the five-second check; it does not demonstrate hours of uninterrupted residency or eliminate temporary vision handoffs.

## Bugs the real workloads exposed

Earlier diagnostic runs failed useful tasks even when short calibration checks passed. Those failures were retained and investigated rather than removed from the record.

| Problem observed | Repair | Verification and limits |
| --- | --- | --- |
| A screenshot targeted MDN's `main` element, which uses `display: contents` and has no drawable bounding box | Preflight now rejects that target immediately with instructions to choose a visible child or an explicitly requested page capture; it does not silently broaden a crop | Direct real-browser viewport and full-page captures succeeded; zero-box, hidden, missing, and normal-element regression cases passed |
| A `body`/`html` element capture with `fullPage: false` still saved the entire tall document, making text illegible when scaled for vision | The capture-coverage repair narrows a top-level document-root request to the actual viewport, records that coverage, and refuses oversized non-root or iframe targets with instructions to choose a smaller visible region | The focused capture suite passed 99 checks. A later live normal-mode diagnostic saved a readable approximately 197 KB viewport and vision read its example correctly. The combined research task still failed separately; this does not establish final matrix success |
| In visual research, the model could independently choose an untargeted full-page capture even when the user had not requested the whole page | Untargeted visual-research captures use a viewport unless the original user explicitly asked for the full/whole/entire page or top-to-bottom coverage. The saved scope and notice identify the viewport and suggest scrolling for further sections; a document-root target cannot evade that internal readability policy | The latest related focused suite passed 111 tests. Explicit full-page requests, actual non-root targeted captures and standalone screenshot saving retain their existing scope. A viewport improves legibility but does not represent unseen page content |
| Screenshot schema accepted only the first 100 references from a larger snapshot | Removed that screenshot-only enum; bounded reference syntax and actual current-DOM resolution still apply | Regression selects a valid element after the 100th snapshot reference; state-changing browser-control restrictions were preserved |
| Complete but schema-invalid tool calls produced a generic failure and unnecessarily unloaded the healthy model | Diagnostics identify the trusted schema field/type/rule without argument values; correction retains the healthy worker after a fully ended response | Valid empty viewport and boolean full-page calls pass; type/enum/unknown-field failures are actionable; invalid identity, JSON, and incomplete streams still fail closed |
| A model could omit the newline between separately generated file sections | For explicit line-counted JavaScript, Arc may insert a separator only between independently parsed complete statements; exact-copy requests bypass this normalization | Ten 100-line chunks produce exactly 1,000 distinct lines; unfinished strings/comments, strict copies, and size limits have regression coverage |
| A later file attempt replayed the first 100-line section with an unchanged checkpoint, reaching 1,000 physical lines but only 900 unique declarations | The replay guard verifies the current file ID/hash and recognizes the same raw section with the same nonempty checkpoint. It returns the unchanged file receipt and exact tail without saving duplicate bytes or advancing the chunk count. Advancing checkpoints and exact-copy requests preserve intentional repetition | The focused suite passed 63 tests. An actual-shape regression reaches 1,000 unique declarations despite replayed first and second sections. The real Tier 3 duplicate failure remains preserved as a diagnostic, separate from the frozen-source matrix |
| A mixed research/screenshot task could finish immediately after saving an image, with requested search or source reads still missing | A bounded follow-through contract retains search and page-read work, with actual-read receipts separate from search leads and indexed fallback excerpts | Focused real retests obtained search and two source receipts; independent-publisher and interpretation limitations remained and are recorded below |
| A final reply could embed a webpage URL or invented screenshot filename instead of the saved picture | Delivery uses the verified image file/card and associates requested vision observations with that image ID and its recorded source URL | Automated regressions cover actual cards, bounded per-image observations, unreadability caveats and preservation of unrelated image links and code. All five final screenshot phases delivered their actual saved image; interpretation caveats remain in the manual review |
| Screenshot reply cleanup discarded a substantive answer because it began with “I'll research” | Cleanup now removes plan-only narration without deleting a researched explanation that happens to open with planning words; substantive text, citations, mixed code, quoted text and fenced lines are preserved | The focused suite passed 63 tests. Replaying the actual failed 821-token answer retained its substantive remainder. The final screenshot answers retained substantive explanations; that does not establish that every claim was accurate |
| A combined research/browser request completed search, source reads, capture and vision but returned another plan with printed navigation calls, incorrectly labeled complete | Final synthesis instructions close the tool phase and retain the research obligation alongside browser results. One bounded evidence-only composition retry can recover a confidently plan-only reply without tools or repeated captures; a persistent plan is incomplete with images and receipts preserved. Actual explanations, code, quoted plans and user-requested plans are preserved, and image evidence has a context-sensitive whole-payload bound | The focused repair suite passed 84 tests; the broader affected-area suite passed 478. All five final screenshot phases delivered substantive answers. This handles completion, not automatic factual grading or guaranteed model accuracy |
| Generic “screenshot” wording could route a browser research task into live screen-sharing guidance | Routing uses actual live-sharing intent/context, while compact recipe extraction preserves the selected task/check/stop instructions | Focused routing/reader checks and guide-catalog validation precede the final guide-enabled run; live effectiveness is not inferred from the guide inventory |
| A source's navigation text displaced useful passages and qualifying details | Query-relevant, exact page excerpts replace the fixed navigation-heavy prefix; explicit omission notices preserve the excerpt boundary | Focused tests cover late qualifications and untrusted source passages; final answer accuracy still requires review |
| Research kept spending work on hosts that denied or rate-limited direct reads | Per-turn guidance retains the observed unavailable direct-read hosts and steers general research toward accessible sources | Bounded state and tool-loop tests pass; permitted ordinary-browser access and source-specific limitations remain distinct |
| After search, the automatic source reader tried three URLs on the same denied host and exhausted the failure limit before the model could recover | Automatic source selection consults per-turn unavailable-host evidence and skips already denied hosts. If the available lead pool is blocked, it may make one alternate-source search using the original query with explicit denied-site exclusions; user-required access attempts remain distinct | The focused denial/alternate-search suite passed 51 tests. Actual retrieval types and final answer limitations are reported separately; the extra search is bounded and neither bypasses access restrictions nor guarantees a readable alternative |
| A pure web-research turn already had its requested search and source reads, but repeated a guessed unavailable URL instead of delivering the answer | Exact failed-URL guidance is retained, and a no-tools synthesis is allowed only when the pure web-research contract is already fulfilled; errors and incomplete-task guards remain | The relevant focused suite passed 61 tests after a diagnostic stopped despite having two source reads. Final source-matched task results and manual citation checks are reported separately |

The historical protocol failure did not preserve its rejected arguments. The first-100-reference mismatch was reproduced independently; do not claim the old trace proves the exact argument that triggered every rejection.

**Latest completed full automated suite: passed, before the finalization repair.** Its regression log reports **3,458 tests: 3,453 passed, 0 failed, 5 skipped and 0 cancelled**, in **556.146 seconds (9 minutes 16.146 seconds)**. That run predates the later final-synthesis ordering and plan-only completion repair in `tool-agent.cjs` and `research-grounding.cjs`; it is not full-suite coverage of that newer patch. After the final repair, a broader run across 27 affected test files passed **478 of 478 tests, 0 skipped, in 11.134 seconds**. The repair's own focused suite passed 84 tests. These suites overlap and their totals are not added. The final 20-file live matrix and artifact review are reported above; package verification has its own release record. Test counts are not model-accuracy results.

The earlier full regression run had 3,427 tests: 3,421 passed, 5 skipped and 1 failed. Its local controlled 100-link crawl exceeded the 30-second timeout during concurrent CPU/GPU work. The isolated rerun passed both targeted tests; that original failure remains recorded separately from the later successful full run. Focused runs also passed 63 narration-cleanup, 63 duplicate-section replay, 61 research-recovery and 111 screenshot/readability checks, and the six new guide documents passed 5 focused tests plus the authored-catalog validator. These overlapping counts are not added to the final suite total. A separate settings UI check exercised all four tier controls and responsive layouts without renderer errors; the TypeScript/production UI build passed before the newest repairs, and the final package checks are separate.

A separate screenshot-component fixture check rendered three actual receipt-based image cards, exercised expand/shrink, and found no page errors or horizontal overflow at 1,100-pixel and 390-pixel widths. Both rendered layouts were visually inspected. This is component/UI validation, separate from the five live browser/vision tasks and from a full Electron end-to-end test.

## Diagnostic retests and output accuracy

These runs used the code revision available at the time. Earlier diagnostics did not enable bundled guidance; the later 22:37 UTC focused run did use the real guide path. They explain which defects were found and repaired. Neither group is the final Normal/Tier 1/Tier 2/Tier 3/Tier 4 performance comparison.

| Diagnostic outcome | What was confirmed | What it did not establish |
| --- | --- | --- |
| An earlier four-mode screenshot attempt failed | No saved screenshot was delivered in any of those visual phases; the records remain in the failure history | A short calibration pass did not make the browser task work |
| Focused 1,000-line file retest passed | The actual saved file contained all 1,000 unique declarations in order, exactly 1,000 lines and 38,785 bytes; application and independent JavaScript parsing passed. Nine omitted section separators were safely restored | The 111.421-second task ran during other CPU validation, so it is functional evidence rather than an uncontended speed measurement; it does not prove arbitrary software correctness |
| A full-page MDN capture and vision call completed, but the combined task failed | The screenshot was real and the vision operation completed; its tall 1,236 × 10,789 layout left small body text difficult to read | Search and the requested second source were missing; the generated answer supplied an incorrect code example and an unsupported search claim |
| Later focused web research passed the mechanical contract | Arc searched and read MDN plus an MDN GitHub mirror; its numeric mapping example was correct and it acknowledged the duplicated publication | Two hosts did not provide independent corroboration; the answer overgeneralized that the original array stays unchanged without explaining callback side effects |
| Later focused research/screenshot/vision passed the mechanical contract | A legible 199,314-byte MDN viewport image was saved and the vision output read its visible example correctly. Search, MDN text and a Stack Overflow indexed excerpt were present | The second source was indexed fallback text, not a verified live-page read. A generated comment incorrectly called the mapped result the same array, and the answer embedded a webpage URL despite the separate real attachment |
| Guided focused file creation passed | The new file-completion guide was actually selected. The saved file again contained exactly 1,000 ordered unique declarations and 38,785 bytes, passed both syntax checks, and matched the prior completed file hash | One incorrect generic plugin call was recovered before writing. Its 129.415 seconds included concurrent CPU tests, so it is diagnostic timing rather than a clean speed comparison |
| Guided focused web research passed mechanically | Search and two MDN/GitHub source reads completed. The numeric transformation, callback-arity example and shared-object mutation explanation were correct | The two hosts still carried the same publication. Generated prose announced another search and printed a pseudo-call without a corresponding tool receipt, and omitted direct input-array mutation |
| Guided focused screenshot research passed mechanically but failed visual grounding | Search, three source reads, a saved image and actual vision all completed. MDN and W3Schools were page reads; Stack Overflow was labeled indexed content in the ledger. The real saved screenshot card was delivered | A root-element capture saved a 1,236 × 10,789 full-document image despite `fullPage: false`. Scaling its longest side to 1,536 pixels left only about 176 pixels of width. Vision invented a `[1,2,3,4,5]` example instead of reading the actual `[1,4,9,16]`; tool completion is not a visual-quality pass |
| A later normal-mode diagnostic recovered readable capture but failed research | The repaired capture produced an approximately 197 KB viewport image and vision correctly read the visible example | Automatic source selection then tried three URLs on the same denied Stack Overflow host and triggered the repeated-failure pause before the model could choose an alternative. This run became diagnostic evidence, not a completed final normal baseline |
| The 23:03 UTC normal diagnostic failed pure web-research delivery | Search and two MDN/GitHub source reads completed, and the source ledger recorded that part of the contract as fulfilled | The model repeated the same guessed unavailable W3Schools URL three times. Only the first attempted the network, but cached failures still exhausted the bounded stop. No final explanation was delivered |
| The same 23:03 UTC diagnostic completed screenshot research and the file task | Research took 92.726 seconds, delivered a legible 201,549-byte viewport with the actual image card, and vision correctly read the visible numeric example. File creation took 133.018 seconds, matched the exact completed-file hash, and the selected model was reused afterward | Three read hosts were MDN or copies, not independent corroboration. The answer overgeneralized nonmutation and attributed a note to the screenshot that was not visible. The run remains diagnostic because its web phase failed and the code changed afterward |
| A later Tier 2 diagnostic lost its substantive research answer during final cleanup | Screenshot/research tools produced evidence, and the raw answer contained a researched explanation | Cleanup treated the opening “I'll research” as proof that the entire reply was only a plan and discarded the substantive text. Mechanical tool completion must not be reported as successful delivery. The associated earlier Normal/Tier 1/Tier 2/Tier 3 attempts used the old cleanup path and are retained as diagnostics, excluded from the repaired source-matched comparison |
| A later Tier 3 diagnostic produced a syntactically valid but incomplete file | The saved file had 1,000 physical lines and parsed successfully | The first 100 declarations were repeated with an unchanged checkpoint, leaving only 900 unique declarations and omitting 901–1000. Independent exact-declaration validation correctly failed the task. Syntax success and physical line count were not treated as completed delivery |
| The later Tier 4 screenshot task recovered invalid capture calls | After three invalid target/argument attempts, Arc saved a readable 1,251 × 1,228 image, used vision, retained the substantive answer and attached the real image card | Recovery involved extra work, and mechanical completion did not remove the recorded accuracy limitations. The entire 23:10–23:27 UTC series remains diagnostic after the duplicate-section guard changed the measured source |
| A subsequent Tier 4 diagnostic passed all seven harness phases | Its saved file again contained all 1,000 required declarations and matched the known completed-file hash | The file task took 468.672 seconds while other tests overlapped. This is diagnostic functional evidence; its timing is excluded from the clean final comparison and seven phase passes are not a general accuracy score |
| The 23:46 UTC normal diagnostic passed capture mechanics but failed final research delivery | Search and three actual reads included MDN, its GitHub source and independent DigitalOcean material. A readable 1,251 × 1,228 viewport was saved, vision read the numeric example correctly and the actual image was attached | The final body was another plan with printed `browser_navigate` calls, not the requested researched explanation and citations. Manual review failed delivery despite a 69.256-second mechanical pass. The 19-file source run is superseded by finalization changes and is excluded from the new final comparison |

The passing file’s SHA-256 is `0427076307bc047f7885df6558aa0aaf5d56423c7d5c134cd76c2bc8bda94fe7`. The legible focused viewport’s SHA-256 is `1512966596d50c3302ce585593acfb49920dba599527fd5411cbba04d8bff29d`. These identify diagnostic artifacts only; final screenshots and files need their own receipts.

The guided diagnostic's oversized image was 1,399,116 bytes, SHA-256 `f66d96edb34373fb5861374f80ce2f469b1e8cbd1945e4b4ab2113a892298aa6`. This identified the separate root-element capture-coverage defect even after actual file-card delivery was repaired. It is retained as a failed visual-grounding example rather than counted as an accurate screenshot summary.

The readable 23:03 UTC screenshot's SHA-256 is `e807e1ea128c9a0c4717adc66f7605d57966e046c3bae9505999d00749ef81a6`. Its correctly described numeric example does not validate every sentence in the final explanation; the nonexistent visible note and overbroad mutation claim remain review findings.

The readable 23:46 UTC screenshot was 196,429 bytes, SHA-256 `493090e3967e91ada789a8b08cdadce452623070d4b502fd8f1b72cee005e8dd`. It proves a real capture was saved; it does not turn the plan-only final message into a completed research answer.

Screenshot delivery, query-relevant evidence retention, viewport coverage and new guidance repaired specific paths but did not eliminate inaccuracies. The diagnostic source-reader, final-answer cleanup, duplicate-section replay and plan-only finalization failures remain in the record; the final comparison reran every mode against the same repaired source. All final task deliveries completed, with the factual and citation limitations listed above. Explicit whole-page captures can still make small text unreadable when downscaled for vision. A correct explanation of this workload states that `map` returns a new array, while a callback can mutate its input array or objects shared with it. The final manual review assessed that distinction, source-retrieval labels, screenshot fidelity and delivery separately from the mechanical pass flag.

## RAM, SSD, and Strata: what this does and does not change

GPU weights, cache state, and runtime buffers consume VRAM. Eligible CPU blocks use system RAM and CPU compute. Installed model files, saved work, conversation checkpoints, and backups live on storage. The OS may map and cache model-file pages. None of these facts makes an SSD a processor or a transparent substitute for VRAM bandwidth.

Arc's default 32 GiB text-inference ceiling is additionally constrained by physical and available RAM reserves. It covers the verified llama.cpp worker/server, not the sum of Electron, browser, voice, vision, Windows, and other applications. GPU fitting includes runtime/cache overhead and free-memory reserves. Lower-VRAM variants retain conservative admission checks; they cannot authorize a larger model just because a rough weight-file size looks small enough.

Strata remains a separate model/backend option. Its model-specific expert caching and CPU/SSD arrangement were not benchmarked in this Focus matrix, and the new tiers do not reconfigure it. Strata stores large assets and lookup data on storage, uses GPU-resident components/cache, and can perform expert work through CPU/RAM paths. Storage misses and transfers can cost time. The llama.cpp RAM policy is not a universal Strata memory cap.

Long outputs use another mechanism: save accepted work, retain a compact task state and exact file ending, then generate the next bounded section. That can produce a file larger than one response without placing the entire file in every prompt. It does not expand the model's native context or guarantee perfect recall from disk. The file workload measures this continuation path together with memory settings.

## Exact workload prompts

These are the exact prompts used by the release harness.

### Bounded generation sample — repeated three times

System instruction:

> Follow the instruction. Write only the requested explanation.

User prompt:

> Explain how rain travels through a watershed into reservoirs, how treatment works, and how clean water reaches homes. Write at least 700 words with different details in each paragraph. Do not repeat sentences.

The cap is 512 generated tokens per sample. This measures a sustained generation segment; do not grade it as failing the requested word count.

### Web research

> Search the web for how the JavaScript Array.map method works. Research its behavior using the MDN documentation and another public source. Explain whether map mutates the original array, include a short example, and cite both sources.

### Research, screenshot, and vision

> Search the web for how the JavaScript Array.map method works. Research its behavior using the MDN documentation and another public source. Take a screenshot of the MDN Array.map documentation at https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map and use vision AI to summarize what is visible. Attach the actual screenshot in this chat. Explain whether map mutates the original array, include a short example, and cite the sources.

### File continuation

> Create offsets.js with exactly 1,000 nonblank lines of JavaScript. Line N must be exports.addN = value => value + N; for every integer N from 1 through 1000. Use literal declarations, not a generator, loop, placeholder or ellipsis. Write 100 lines per file chunk and continue until all 1,000 lines are saved. Check the final JavaScript syntax. No execution needed. Attach the completed file.

### Calibration prompts, separate from workload results

Warm-up:

> Reply with only the word ready.

128-token sustained calibration:

> Write a continuous numbered list of practical everyday objects, starting with item 1. Give each item a short description of at least eight words. Continue for at least twenty items; do not conclude or explain the task.

The sustained calibration sample needs at least 96 generated tokens and valid native timing to count as a speed sample. The five basic checks are:

1. “What is 17 + 25? Reply with only the integer answer.”
2. “Reply with exactly this lowercase word and nothing else: cobalt”
3. “Read this fictional note: The blue parcel goes to Mira. The red parcel goes to Leon. Which person receives the blue parcel? Reply with only their name.”
4. “Return only a JSON object with exactly two properties: "color" with the string "green", and "count" with the integer 3. Do not add Markdown.”
5. “Write only JavaScript code for a function named add with parameters a and b. It must return their sum without converting types, logging, or changing external state. Use a function declaration and one return statement.”

## Limits and next tests

This is one PC, one text model, one context size, and the recorded CUDA backend. It does not establish performance on 4, 8, 12, or 24 GB cards, AMD/Intel GPUs, Apple Silicon, CPU-only systems, other model quantizations, or Strata. A large-GPU memory-saving result is not proof that the normal fitting requirement has disappeared on a smaller card.

Public websites can change or deny access between runs. Tasks can take different routes, return different text, or require different numbers of calls. The receipts preserve those differences; they prevent attributing every timing change to VRAM settings. No large general knowledge, reasoning, long-context retrieval, or vision-accuracy evaluation was performed by these few prompts.

Next validation should include repeated runs on smaller GPUs, realistic long prompts, longer mixed workloads, and task-specific output-quality review. CPU offload depends on CPU and RAM bandwidth, so results should be remeasured on the target computer.

## Reproducibility and release assets

The release's separate `SHA256SUMS.txt`, installer asset metadata and package-verification record identify the exact executable. Publication checks require the matching-version installer, completed report, sanitized evidence and package-source verification. The installer hash is deliberately not embedded in this report: changing a report bundled inside the installer would change the installer hash again. This document is finalized before packaging and remains frozen while the package is verified. Release notes and checksum files outside the package record that later verification.

The public evidence package contains aggregate measurements, source/backend digests and reviewed artifact receipts. It excludes local filesystem paths, browser-profile locations, hardware UUIDs, process IDs, credentials and unrelated conversation content. Failed attempts, missing readings, task status, sample counts and measurement scope remain visible. Raw diagnostic traces stay local.

The Windows installer is not Authenticode signed. No matching VirusTotal scan has been supplied for this new build. Older scan links describe only their own installer hashes; timed-out engines are inconclusive. Arc’s signed update metadata and checksum verification are separate from Windows publisher signing.
