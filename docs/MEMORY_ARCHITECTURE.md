# How Arc uses GPU memory, RAM and storage

Arc's optional **Lower VRAM usage** setting tests ways to reduce the memory required by the selected text model. It preserves that model, its installed weight quantization and its selected working context. It does not turn the SSD into extra GPU memory, deliberately slow every answer, or assume that one configuration works on every computer.

The setting is under **Settings → AI Performance**, above the advanced controls. It is off by default. Enabling it starts with Tier 1; the result of a real local comparison decides which supported settings are retained. Turning it off restores the tested normal configuration.

## What each tier changes

| Mode | Allowed generation loss; prompt throughput checked when the normal timing is available | Configurations Arc considers |
| --- | --- | --- |
| Normal | Reference | The selected model's successful normal configuration |
| Tier 1 · Light | Up to 20% | Smaller processing buffers; supported Q8 K/V and mixed Q8-key/Q4-value caches with Flash Attention |
| Tier 2 · Balanced | Up to 35% | Tier 1 options, plus one or two CPU model blocks when selective placement is supported |
| Tier 3 · Maximum | Up to 50% | Earlier options, four or eight CPU blocks, and a supported Q4 K/V cache |
| Tier 4 · Extreme | Up to 75% | Earlier options plus supported Q4 K/V combined with two, four or eight CPU blocks |

These percentages are acceptance limits from Arc's short comparison, not a promised speed reduction, a maximum slowdown for every application task, or a percentage of VRAM that will be saved. Tier 2 can select the same settings as Tier 1. Higher tiers can select a cache-only configuration that is as fast as normal if moving work to the CPU is too expensive. Tier 4 accepts measured generation throughput as low as 25% of normal; the same floor applies to prompt throughput when the normal prompt timing is available. It does not promise 75% less VRAM.

Arc uses the selected, verified runtime's actual capabilities. Where its tensor-placement feature is available, the trial moves a few numbered model blocks to the CPU and leaves the output projection on its normal GPU path. Without that feature, it tests conventional partial GPU-layer offload: approximately 87.5% and 75% of the model's blocks remain on GPU at Tier 2, with a 50% trial added at Tier 3. The layer count is read from bounded GGUF metadata. Invalid or unavailable metadata prevents these trials rather than triggering a guess.

Tier 4 adds at most three combined Q4-cache/CPU-placement trials. Without selective placement, those combinations use the same 87.5%, 75% and 50% GPU-block ratios. Arc measures the combined configurations directly; it cannot assume they inherit the savings or speed of separate tests. Earlier candidate identities are retained, and valid cached trials can be reused at another tier without rerunning them. More permissive thresholds never revive an unhealthy result.

For each candidate, Arc loads the model and performs real inference. All five basic response checks and measured generation speed must meet the tier's limits. Prompt speed is compared when the normal result has a positive finite prompt rate; the candidate must then have an acceptable measured rate. An unavailable normal prompt rate skips that comparison. First-token latency is checked when both timings are available; missing timings do not constitute a latency pass. Where GPU telemetry is available, a candidate must show at least 32 MiB of whole-device savings. Among candidates within 128 MiB of the largest saving, Arc uses its existing performance/quality score to avoid trading substantial speed for a tiny memory difference. Unknown GPU telemetry remains explicitly unmeasured.

The response checks cover arithmetic, instruction following, extraction, JSON structure and a small JavaScript function. They are useful compatibility checks, not a comprehensive assessment of model intelligence, long-context quality or coding reliability. Cache quantization can change output. A successful five-question check does not establish that every answer is equally accurate.

Failed initialization, out-of-memory events, crashed backends and unhealthy configurations cannot become winners merely because a more tolerant tier is selected. A successful inference trial rejected only for speed can be reconsidered against the higher tier's threshold. Measurements are reused instead of rerunning the comparison on every launch.

## Four different kinds of memory

| Resource | What it holds | What the lower-VRAM setting can do |
| --- | --- | --- |
| GPU VRAM | GPU-resident model weights, attention/cache state, compute buffers and runtime allocations | Reduce supported cache precision and processing-buffer size; test moving selected model computation to the CPU |
| System RAM | Inference workers, mapped model pages, CPU-resident weights and CPU computation state | Accommodate eligible offloaded blocks within the configured text-inference budget |
| SSD or other local storage | Installed model files, conversations, saved sources, generated files, task checkpoints and backups | Preserve durable work and model assets; it is not a directly usable replacement for VRAM |
| Operating-system file cache/pagefile | Operating-system-managed file pages and committed-memory backing | Its presence affects loading and memory behavior; Arc's low-VRAM switch does not reconfigure Windows virtual memory |

A model's file size alone does not establish whether it fits. Runtime memory also includes its working context, KV/recurrent state, temporary buffers, driver/backend allocations and safety reserves. Compressing the KV cache leaves the installed model weights unchanged. Moving model blocks to RAM changes where work is done; it does not make the CPU or PCIe link as fast as the GPU's local memory.

The normal fitting configuration must pass first. Low-VRAM variants retain conservative GPU admission estimates and cannot silently authorize a larger model. This release therefore reduces allocations for an already supported configuration; it is not evidence that Arc Focus becomes an 8 GB model or that a 4 GB card can load every model in the catalog. No lower-memory GPU hardware was established by testing only an RTX 5090.

## What the 32 GiB budget means

The default **32 GiB** preference is a ceiling for the verified llama.cpp text-inference worker and server. The effective allowance is the smallest of the configured ceiling, physical RAM minus 4 GiB, and currently available RAM minus 2 GiB. A PC with exactly 32 GiB of RAM consequently receives an effective inference allowance below 32 GiB.

For a dedicated GPU, the planning allowance is capped at 90% of dedicated capacity and currently available VRAM minus 2 GiB. When free VRAM is unknown, the planner uses a more conservative fraction. Shared graphics memory is not counted as extra dedicated VRAM.

Before load, Arc checks memory again. During supported Windows inference, its owned worker/server are contained and their working set and private committed memory are sampled. Either host-memory counter exceeding the effective cap, or losing the required free-memory reserve, stops the owned inference process. CUDA GPU memory is sampled separately. These safeguards reduce risk; periodic samples can miss short peaks, and Vulkan does not have the same live GPU-allocation measurement in this implementation.

This is **not** a 32 GiB cap on all of Arc plus every browser tab, speech process, vision model and other program on the computer. Whole-PC RAM and inference-process RAM must be reported separately. Working-set and committed-memory figures describe different things and must not be added together. Partial-offload candidates receive a conservative host allowance for weights, cache and workspace within the same effective cap.

## Strata uses a separate design

Arc continues to load its Strata models with the Strata engine. These low-VRAM tiers do not replace it with llama.cpp or rewrite its configuration.

The pinned Strata 0.1.39 integration supports a model-specific mixture-of-experts execution path. Its GPU keeps the components needed for each token and a cache of frequently used experts. Work for experts outside that cache can run on the CPU using RAM; parts of the execution also transfer data across PCIe. Local storage holds the large model assets and lookup table. In low-RAM modes, expert data can be mapped from files, with operating-system cached pages and/or a bounded resident RAM set reducing repeated storage reads.

That distinction matters: **an SSD stores data; it does not perform the model's matrix computation**. When the needed expert data is already in GPU memory or RAM, inference avoids storage waits. When many required pages must be fetched from disk, storage latency and bandwidth can substantially reduce speed. A fast result on a large-VRAM machine does not demonstrate the same speed on a smaller GPU with many more storage or CPU misses.

Arc's Strata installer requests the upstream low-RAM setup at 16K context with native Strata vision disabled. Strata's setup decides its own compatible memory arrangement. The llama.cpp optimizer's 32 GiB preference is not currently passed through as a universal Strata process-memory cap. Likewise, Strata's optional expert-cache, KV-streaming and elastic-VRAM features must not be described as features of the new four-tier switch. They need their own integration and measurements before Arc can make those claims.

This explanation is grounded in Arc's pinned integration and the bundled Strata `docs/HOW_IT_WORKS.md` and `docs/DETAILS.md`. It does not reuse upstream speed estimates as Arc benchmark results.

## Keeping the model ready

Completing an ordinary text task does not start a new idle-unload timer. Arc keeps the selected healthy model resident for the next request. Explicit model/context changes, shutdown, required resource handoffs or a failed worker can release it.

Vision is a separate workload. The saved-screenshot batch path temporarily releases the owned text model, loads one vision worker for the batch, then restores the previous text selection once the batch finishes, including bounded cleanup/restoration after cancellation or failure. Some live voice/vision paths first try keeping models together when memory permits. A comparison of native text tokens/second alone does not include screenshot capture, image preprocessing, vision inference or the time to restore the language model.

This is why the benchmark report should include full task completion time as well as token speed. A lower text-model VRAM peak does not prove that every vision pipeline uses less memory or becomes faster.

## Long files use saved progress, not unlimited context

Arc's long-output and multi-file features solve a different problem. They keep accepted output and task state on local storage, then send a compact relevant view into the model's actual working window for each step.

For a continued file, that view can include the original request, pinned requirements, file revision, structure outline, exact ending and a short progress note. Arc verifies the revision before appending. Repeated text and malformed structured sections can be rejected without throwing away earlier accepted work. If corrections or work limits are exhausted, it reports the task as incomplete.

Multi-file website projects save the plan, dependency contracts, source and status one file at a time. Completed file bodies are not repeatedly copied into every prompt. The model can read relevant source ranges when it needs exact interfaces. Syntax and static local-link checks provide bounded verification; they do not prove all behavior or design quality.

The SSD can therefore hold a deliverable larger than one generation. The model still does not attend to all archived text at once. Retrieval can omit useful details, and additional generations can take much longer than one answer. Neither low-VRAM mode nor saved checkpoints remove the model's native context limit.

Current bounds include 2 MiB per Created File, 512 file sections in a run, and 32 additional answer generations. Multi-file website builds have separate limits of 2–80 files, 512 KiB per file, 8 MiB of source per project and 256 model rounds per run. The user can stop and resume supported work from verified checkpoints; it does not run indefinitely without bounds.

## What to publish with measurements

A credible report should identify Arc and backend versions, model/quantization, selected context, GPU/CPU and physical RAM, the 32 GiB policy, exact selected tuning, warm/cold state and task prompt. Report the normal configuration and every tier using the same workload. Identify how many repetitions were completed rather than presenting one short run as a statistical guarantee.

Keep these metrics separate:

- Native generation tokens/second, native prompt tokens/second and first-token latency.
- Wall-clock time to a complete verified task, including tools, network waits, vision handoffs and model loads.
- Whole-device GPU memory/utilization versus the owned text worker/server's RAM counters.
- Storage activity that was actually measured versus architecture inferred from source.
- Actual source reads, captured screenshots, successful vision analyses and created-file validation results.

Web availability, login requirements and browser challenges can cause tool failures independently of text-model VRAM settings. Do not hide a failed screenshot or an incomplete 1,000-line file behind a successful synthetic token-speed result. When network work overlaps another application, report that confounder. This report does not establish behavior on AMD/Intel hardware, 4/8/12/24 GB cards, every context size or every model unless those configurations were actually run.

## Other recent changes

**0.51.60 — File History.** Search locally created work by description and date, inspect versions, verify backups with SHA-256 and restore a numbered copy without overwriting the current file. Identical content is stored once. The local store has a 10 GiB unique-content/50,000-record limit with visible warnings and manual removal. This protects an existing backup on the same PC; it does not replace an off-device backup or restore work that was never backed up.

**0.51.61 — Long output and task continuity.** Per-conversation checkpoints and pinned requirements survive restarts. Smaller windows use smaller file sections, exact file endings and compact outlines. Bound continuation to the current file revision; check requested paragraph/function/JSON-entry counts; reject substantial repetition; validate JSON sections and assemble checked record batches. User-submitted resume preserves confirmed work. No unlimited-context or universal task-success claim is implied.

**0.51.62 — Multi-file website builds.** Write pages, styles, scripts and nested directories sequentially in the local coding workspace; save a shared plan and dependency contracts; resume after checking saved source hashes; run available syntax/static-link checks; export a complete directory with per-file verification. Source creation does not execute generated code or publish to a server.

**0.51.63–0.51.64 — Lower-VRAM controls.** Add the optional switch, then three measured speed-tolerance tiers, safe cache/buffer trials, selective CPU-block trials, cached results and normal-mode restoration. No new runtime dependencies, automatic model download or automatic context reduction are introduced by these tiers.

**0.51.65 — A fourth memory tier and workload fixes.** Tier 4 raises the permitted generation slowdown to 75% in the short comparison, applies the same prefill limit when normal timing is available, and adds bounded combinations of Q4 cache and eligible CPU blocks. It preserves existing tier identities, model/context choices and the host-memory ceiling. Screenshot target validation now distinguishes visible content from layout-only containers, supports current-page references beyond the first 100 elements and preserves a healthy model when a completed tool request has correctable arguments. The measured workload report records task results separately from these implementation changes.

Newly authored line-counted JavaScript uses its actual saved line count; a missing separator between complete, safely terminated sections can be repaired without rewriting accepted bytes. Exact-copy work is excluded from that repair. Direct-source 403/429 observations survive compaction as trusted per-turn guidance, allowing a different publisher or an already authorized normal browser route. These are bounded recovery changes, not a promise that every website or file task will complete.

Combined screenshot research retains a bounded phase for explicitly requested search and source reads after image capture. Per-turn receipts distinguish retrieved page text, indexed fallback excerpts and search leads. A source count does not establish independent ownership or factual accuracy. Screenshot instructions prefer readable viewport/element captures unless full-page capture is requested, and ask vision to admit unreadable text instead of substituting familiar examples. The final report records mechanical completion and manual accuracy review separately.

The capture implementation enforces that readability policy for untargeted visual-research requests and top-level document roots; saved receipts identify the actual viewport. Explicit whole-page captures retain their requested scope and can still have legibility limits. Replies deliver the verified saved image card and associate vision observations with that capture, rather than using a webpage URL as an image. Final composition preserves substantive explanations and allows one bounded, evidence-only retry for an answer that is confidently just another plan. That retry cannot run tools or take new screenshots; a persistent plan is incomplete with existing evidence preserved.

Long-file continuation also recognizes a replayed accepted section when the current file identity/hash, raw section and nonempty checkpoint are unchanged. It returns the saved receipt and exact ending without appending the same bytes twice. This is a narrow retry guard, not a ban on intentional repeated code with advancing progress or exact-copy work. Source-reading recovery retains unavailable-host and exact failed-page observations, permits one bounded alternate search when appropriate, and preserves relevant source passages and qualifications through compaction. Those mechanisms neither bypass access restrictions nor grade the model's final factual claims.

Six new completion guides provide 8,100 words of full reference material covering source verification, readable screenshots, combined research/image delivery, checked file chunks, debugging and evidence-based resumption. Automatic selection loads compact task/check/stop recipes within the existing context budget; longer documents can be read when needed. The guides use existing tools and permissions. Their number and length are not proof of increased intelligence or accuracy, and the workload comparison does not isolate their causal effect from the accompanying implementation fixes.

Implementation references: `electron/services/low-vram.cjs`, `inference-policy.cjs`, `inference-optimizer.cjs`, `runtime.cjs`, `electron/scripts/inference_profile.py`, `voice_text_worker.py` and `strata_setup.py`. Detailed operating notes remain in `docs/AI_PERFORMANCE.md`, `docs/OUTPUT_CONTINUITY.md`, `docs/PROJECT_BUILDS.md` and the corresponding release notes.
