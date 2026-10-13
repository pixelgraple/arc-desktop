# AI Performance: implementation and operating reference

Arc's local optimizer, introduced in 0.48.0 and extended through 0.51.65, chooses among installed models and verified inference engines. It measures a bounded set of configurations, applies a successful result, and can reuse the measurements on later launches. The implemented optimizer paths are Windows x64 llama.cpp CPU, CUDA and Vulkan. The separately installed Strata engine has its own setup and memory policy; it does not participate in the llama.cpp optimizer or lower-VRAM tiers.

The default optimized text-inference RAM ceiling is **32 GiB**, even on a computer with substantially more RAM. The effective allowance can be lower to preserve memory for Windows and other applications. This bounds the owned text worker/server, not the combined memory of Electron, browser tabs, speech, vision and every other application. Strata's upstream low-RAM setup is separate and does not currently consume this optimizer preference. A GPU name, an executable on disk, or an advertised precision capability never establishes that a model works: file verification, engine initialization, memory eligibility and actual inference are separate gates.

## Using it

After an approved first-run model installation, the setup flow opens the performance check before the final welcome step. Tools-only setup skips it. An existing installation without a reusable performance profile can also open the check. If models are installed but there is no valid reusable result, initialization suspends legacy inference until a configuration passes under the current policy. Cancelling keeps the installed models but leaves chat waiting for a successful rerun. The check uses installed models; it does not download additional weights to improve its score.

Open **Settings → AI Performance** to see the selected model, engine, context, measured reply speed, warm time to first token, and memory observations. Opening Settings alone does not start a benchmark. **Run check again** requests a fresh comparison. Stop waits for owned benchmark cleanup; once applying the result begins, the brief commit stage cannot be cancelled. The interface permits continuing without a result, but that does not make an untested inference configuration ready.

Automatic mode is the default. Advanced choices allow selecting a configuration that already passed, changing the relative scoring weights, or changing the RAM ceiling. A new RAM ceiling invalidates the current measurements and suspends the selected inference profile until it is tested under the new policy. Manual selection cannot accept an arbitrary executable, an untested result or a new memory budget without testing.

## Lower VRAM usage (0.51.65)

**Settings → AI Performance → Lower VRAM usage** is an optional immediate-save switch outside Advanced. It defaults off, with Tier 1 as the initial enabled level. Save other preference edits first. The model, weight quantization, chosen context and RAM ceiling are preserved. Testing temporarily reloads the model; the desktop restores the final selection through its resident-model service. No idle unloading is added.

| Tier | Measured generation speed loss allowed; prefill checked when the normal timing is available | Additional tests |
| --- | --- | --- |
| 1 · Light | Up to 20% | Smaller physical batches, Q8 K/V and mixed Q8 K/Q4 V caches with verified Flash Attention |
| 2 · Balanced | Up to 35% | One or two CPU blocks with the output projection left on GPU, where tensor placement is supported |
| 3 · Maximum | Up to 50% | Four or eight CPU blocks, plus Q4 K/V cache |
| 4 · Extreme | Up to 75% | Earlier trials plus supported Q4 K/V combined with two, four or eight CPU blocks |

Without tensor-placement support, tiers 2–4 test conventional partial GPU-layer offload instead. Tier 4 combines Q4 K/V with approximately 87.5%, 75% or 50% of blocks on the GPU. It adds at most three combinations to the existing alternatives and measures each combined configuration; separate cache and CPU measurements cannot establish how the combination performs. Tier 4 permits generation throughput as low as 25% of normal in the short comparison; the same floor applies to prefill when the normal prefill timing is available. It does not promise 75% less VRAM. GGUF layer counts are read from at most 4 MiB of bounded metadata. Invalid, absent or oversized metadata disables partial-offload trials. No external model code runs. All flags pass the existing host and Python validation boundary; tensor patterns are generated solely from validated integers.

Each trial uses native inference, existing memory guards and owned-worker cleanup. All five basic response checks and measured generation speed must satisfy the selected tier. Prefill is compared when the normal result has a positive finite prefill rate; the candidate must then provide an acceptable measured rate. If the normal prefill rate is unavailable, that comparison is skipped. When both first-token timings are available, candidate delay must not exceed the larger of baseline/(1−allowed slowdown) or baseline plus 250 ms. Missing first-token timing means that guard is not evaluated, not that latency passed. With GPU telemetry, at least 32 MiB of observed whole-device savings is required. The largest saving wins; within a 128 MiB noise band, the existing quality/performance score decides. Unknown GPU usage is explicitly unmeasured. These short checks cannot guarantee speed or quality on all tasks.

Measurements are cached per model/backend/context/tuning identity. Changing tiers reuses them, including successful inference that previously exceeded a stricter speed limit. Crashes, OOM and later unhealthy results are never revived by increasing the tier. Turning off the switch restores normal settings; cancellation restores the previous preference/profile. Runtime capability probes occur only on explicit tuning when an old profile lacks required capability metadata. Normal startup does not benchmark again.

Partial offload conservatively budgets all weights, KV state and workspace in host RAM, within the existing effective memory ceiling. It does not use extra physical RAM merely because it exists. GPU admission estimates remain conservative. CPU and Strata keep their own engines; enabling this while Strata is selected cannot switch it to llama.cpp. No new dependencies or downloads are added.

Use `node scripts/benchmark-low-vram.cjs --sweep` for isolated real-inference QA with installed Arc Focus. It benchmarks all four tiers' alternatives together before selecting each level from the same measurements. To extend prior calibration without overwriting it, first copy its isolated QA folder to a new `low-vram-<timestamp>` folder, then run `node scripts/benchmark-low-vram.cjs --tiers=4 --resume=low-vram-<timestamp>`. The named folder stays under `work/qa`; the resumed profile reuses valid trials and records normal mode before testing the selected tier. It never changes the user's Arc profile or terminates user processes. Raw reports under `work/qa/low-vram-*/report.json` are private diagnostic evidence and may contain local paths or hardware identifiers; publish only reviewed, sanitized aggregates. Production comparisons are hardware-dependent; rejecting a configuration is an expected outcome, not permission to invent a result.

## Implementation map

| File | Responsibility |
| --- | --- |
| `electron/services/inference-hardware.cjs` | Bounded hardware inventory, capability metadata, local capability signature |
| `electron/services/inference-backends.cjs` | Trusted runtime file verification, bounded CLI initialization, runtime device mapping |
| `electron/services/inference-policy.cjs` | Preferences, memory estimates and reserves, candidate construction, score calculation |
| `electron/services/inference-benchmark.cjs` | Synthetic prompts, static checks and native timing summaries |
| `electron/services/inference-optimizer.cjs` | Run lifecycle, profile cache, selection, manual preferences and fallback |
| `electron/services/inference-recovery.cjs` | Bounded recovery before response output, without replaying tool actions |
| `electron/services/runtime.cjs` | Verified profile boundary, owned worker lifecycle and benchmark execution |
| `electron/scripts/inference_profile.py` | Worker profile validation, backend arguments, native timing extraction and memory guard |
| `electron/scripts/voice_text_worker.py` | Existing private text worker and authenticated owned llama.cpp server |
| `electron/scripts/text-models.json` | Pinned model identity plus explicitly labelled selection metadata |
| `scripts/inference-backends-artifacts.json` | Official runtime archive and per-file size/SHA-256 pins |
| `scripts/setup-components.py` | Approved component installation, verified bundle copying and archive fallback |
| `electron/services/setup-model-catalog.cjs`, `setup-model-fit.cjs`, `setup-planner.cjs`, `onboarding.cjs` | Backend-aware recipes, memory fit, approval plan and saved runtime hints |
| `src/PerformancePanel.tsx`, `src/performance-types.ts`, `src/performance.css` | Results, preferences, progress, comparison and responsive layout |
| `electron/main.cjs`, `electron/preload.cjs`, `src/App.tsx`, `src/FirstLaunchSetup.tsx` | Existing application lifecycle, guarded IPC, Settings and first-run integration |

The public state contains display metadata, opaque configuration IDs and measurements. Private server paths, device identifiers and environment variables remain on the host side. Renderer input cannot turn `verified: true`, a supplied hash or an arbitrary path into execution authority.

## Hardware detection and identity

`detectInferenceHardware({directory, ramBudgetGiB = 32, ...dependencies})` returns platform, architecture, CPU, RAM, disk, separate GPU records and a capability signature. Dependencies can be injected for tests; production uses bounded operating-system probes.

On Windows, CPU vendor and physical core count come from `Win32_Processor`; logical cores come from the OS. ISA flags come from `IsProcessorFeaturePresent`, including available SSE4.2, AVX, AVX2, AVX-512 and ARM feature flags. Marketing names are not an ISA detector. The [Microsoft API reference](https://learn.microsoft.com/en-us/windows/win32/api/processthreadsapi/nf-processthreadsapi-isprocessorfeaturepresent) defines those flags. Unsupported or failed probes preserve unknown values rather than inventing features.

RAM includes physical total, currently available memory and configured ceiling. Windows display and NVIDIA probes retain separate adapters, dedicated capacity, available capacity when measured, driver version and compute capability. Shared memory is not added to dedicated VRAM. Linux DRM and macOS display inventory paths exist as detection groundwork; their presence does not mean Arc ships or supports a Linux/macOS app or working inference backend there.

For NVIDIA, compute capability maps to architecture and SM metadata; for example CC 12.0 maps to Blackwell and `sm_120`. The CUDA driver-supported API version is recorded separately from an installed toolkit, which remains unknown. Architecture-level Tensor Core and precision metadata is labelled **advertised**, not a successful kernel/model result. NVIDIA's [compute-capability table](https://docs.nvidia.com/cuda/cuda-programming-guide/05-appendices/compute-capabilities.html) and [nvidia-smi reference](https://docs.nvidia.com/deploy/nvidia-smi/index.html) are the primary sources. This does not establish FP4 GGUF, NVFP4 or TensorRT compatibility.

The capability signature hashes capability-relevant fields such as CPU features, capacity, platform and driver versions. It excludes device UUIDs, serials, paths, hostnames, timestamps and volatile free-memory readings. Private device identity may be used locally to pin the correct adapter; it is not exported into the public hardware profile or used as a unique hardware fingerprint.

## Engines: availability is a sequence of checks

`verifyInferenceBackend(candidate)` looks up a fixed recipe in the shipped registry, checks platform/architecture and the server filename, rejects unsafe paths and unexpected adjacent loadable libraries, and verifies every pinned runtime file. Caller-supplied trust claims are ignored. Discovery cannot execute `--version`, `--list-devices` or `--help` until verification succeeds. Runtime configuration and later worker starts revalidate the selected backend.

`discoverInferenceBackends({hardware, runtimes, signal})` then checks the pinned build, required flags and device enumeration under bounded time/output limits. The CPU b10590 build legitimately returns `Available devices: (none)`; that is not a CPU failure. CPU eligibility uses the trusted CPU recipe, correct version and required flags. An initialized descriptor records `initializationScope: backend-device-enumeration` and `modelValidated: false`. The optimizer must still load and exercise a model.

| Engine path | Arc 0.48.0 status | Selection boundary |
| --- | --- | --- |
| llama.cpp CPU | Implemented; official Windows x64 runtime bundled | Verified CPU initialization and successful model benchmark |
| llama.cpp CUDA | Implemented; pinned existing/approved Windows runtime | Actual CUDA device, exact private UUID pin, memory fit and benchmark |
| llama.cpp Vulkan | Implemented; official Windows x64 runtime bundled | Actual enumerated device matched to hardware, memory fit and benchmark |
| llama.cpp HIP/ROCm | Unavailable placeholder | No trusted Arc runtime recipe or implemented execution adapter |
| llama.cpp SYCL | Unavailable placeholder | No trusted Arc runtime recipe or implemented execution adapter |
| llama.cpp Metal | Unavailable placeholder | No shipped Apple runtime/app validation |
| TensorRT-LLM | Unavailable placeholder | No integrated model adapter or trusted runtime recipe; native Windows is not the documented target |

llama.cpp upstream documents CPU, CUDA, HIP, Vulkan, Metal and SYCL build paths; upstream availability is broader than Arc's implemented adapters. Current SYCL documentation primarily targets Intel and records removal of the earlier NVIDIA/AMD plugin channels. See the official [llama.cpp build guide](https://github.com/ggml-org/llama.cpp/blob/master/docs/build.md) and [SYCL guide](https://github.com/ggml-org/llama.cpp/blob/master/docs/backend/SYCL.md).

TensorRT-LLM has its own OS, architecture, model and precision support matrix. It is not a drop-in loader for Arc's pinned GGUF files. A future integration needs a separate validated recipe and model path, not a GPU-brand switch. See the official [TensorRT-LLM platform matrix](https://nvidia.github.io/TensorRT-LLM/reference/support-matrix.html) and [supported-model matrix](https://github.com/NVIDIA/TensorRT-LLM/blob/main/docs/source/models/supported-models.md).

CUDA uses the selected physical UUID through `CUDA_VISIBLE_DEVICES` and the worker addresses logical `CUDA0`. Vulkan uses the index returned by that verified binary's enumeration, not a Windows display ordinal. Ambiguous identical adapters remain unavailable if they cannot be matched safely. GPUs are not pooled.

## Distribution and installation

CPU and Vulkan come from official llama.cpp **b10590**, commit `6657ded4faa3b8450221119fc6b4d002e35104a2`. The bundle contains 105 unchanged upstream files totalling **146,140,730 bytes unpacked**, plus the distribution documentation. The two source archives total **52,602,791 bytes**; this is not a claim about the final installer size after compression and shared-file handling.

| Archive | Bytes | SHA-256 |
| --- | ---: | --- |
| `llama-b10590-bin-win-cpu-x64.zip` | 18,132,388 | `98d942240a61a5c628c16d7951c041095e63a741916c74110e785129e10c2eaa` |
| `llama-b10590-bin-win-vulkan-x64.zip` | 34,470,403 | `ecc1a6f2f47d85086d69ed07f694f6ff1baa7c034ac46655333c3168316b38e9` |

The registry preserves per-file hashes as well as archive pins. `integrations/inference/{cpu,vulkan}` is included in the installer and unpacked outside ASAR. The full llama.cpp MIT license and upstream LLVM OpenMP notices accompany it. No npm dependency was added for this feature; existing Node, Python, Acorn and UI dependencies are reused. No model weights, CUDA toolkit or global driver changes are bundled with these two engines.

Setup compares its exact engine recipe with the shipped backend manifest and hashes the bundled files before copying them into an owned component directory. It verifies the destination files again. Missing or altered bundles fall back to the approved pinned archive path. Existing verified components are reused; unexpected old runtime directories are preserved before repair. Setup copying does not run a backend or load a model.

Non-CUDA setup starts from CPU and includes a Vulkan candidate when a Windows GPU is present. It does not fetch CUDA packages for CPU/AMD/Intel text setup. CUDA/Vulkan selections retain an independent CPU fallback. Main-process discovery also considers bundled CPU/Vulkan for upgrades whose component directory lacks them. Download totals remain maximum approved transfers; bundled copies and caches reduce the actual transfer. GPU speech and vision retain their existing CUDA restrictions and are not made compatible by text Vulkan support.

## Model registry and candidate search

The seven existing pinned text recipes remain the source of model identities. The added inference metadata describes family, architecture, format, supported quantization, context choices, estimated cache/workspace requirements, relative quality prior and coding/reasoning/tool suitability. Supported-backend metadata is an eligibility declaration, not evidence that every backend has run that model.

| Product label | Model ID | Pinned quantization | Curated quality prior |
| --- | --- | --- | ---: |
| Arc Core | `heretic-lite` | Q3_K_S | 0.85 |
| Arc Scout [Lite] | `heretic-moe-iq3-xxs` | IQ3_XXS | 0.72 |
| Arc Reason [Lite] | `heretic-moe-q4-k-m` | Q4_K_M | 0.83 |
| Arc Code [Lite] | `heretic-coder-30b-q4-k-m` | Q4_K_M | 0.82 |
| Arc Spark | `qwen35-2b-heretic` | Q4_K_M | 0.35 |
| Arc Swift | `qwen3-4b-2507-heretic` | Q4_K_M | 0.52 |
| Arc Focus | `qwen35-9b-heretic` | Q4_K_M | 0.68 |

These priors are authored heuristics, not benchmark scores. Spark, Swift and Focus retain their experimental registry flags. Only installed, load-enabled, compatible models become candidates. No quantization is invented, converted or downloaded by the optimizer.

The first-run search is capped at eight candidates. It includes the smallest fitting CPU fallback and samples up to three GPU model tiers. With more than three fitting models, it starts with the highest quality prior, then samples successively smaller weight-size tiers so balanced and small models are represented. Weight size is coverage guidance, never a claim about speed. Alternative usable backend/device pairs receive comparisons before eligible Flash Attention variants. Initial contexts are supported choices between 8,192 and 16,384 tokens; the highest fitting choice is used. This is not an exhaustive Cartesian search or a test of every model/context/engine combination. Later explicit model/context selection requires an existing successful matching profile or a fresh bounded compatibility test.

Initial GPU candidates use full offload; CPU uses zero GPU layers with operation and KV offload disabled. After a compatible normal configuration passes, optional lower-VRAM tiers 2–4 can test selective CPU blocks or conventional partial offload as described above. Multi-GPU splitting and thread-count searches are not implemented. Worker threads default to half the logical CPU count, capped at eight. Flash Attention is only attempted if the backend advertises its flag. The initial Flash Attention variant also changes K/V cache types from f16 to q8_0, so differences compare the whole configuration and cannot be attributed to Flash Attention alone. Speculative decoding remains disabled in this optimizer and there is no pinned draft model or FP4 model asset in its candidate matrix; Strata's separate engine is outside that statement.

## Memory policy: 32 GiB baseline

For finite physical/free RAM readings, the effective AI allowance is:

```text
RAM allowance = max(0, min(configured GiB ceiling,
                           physical RAM - 4 GiB,
                           currently available RAM - 2 GiB))
```

Unknown RAM produces no eligible allocation. A machine with exactly 32 GiB physical RAM therefore has an allowance below 32 GiB. The ceiling is not a reservation, a claim about total Arc/browser memory, or permission to consume all currently free RAM. Users may choose a whole-number ceiling from 4 to 1,024 GiB, but physical/free limits still apply and the new policy needs testing.

For a GPU with known dedicated capacity:

```text
VRAM allowance = max(0, min(90% of dedicated capacity,
                            available VRAM - 2 GiB))
unknown free VRAM: use at most 60% of dedicated capacity
```

Shared GPU memory is not substituted for dedicated capacity. Unified-memory planning groundwork uses part of the same RAM allowance; it does not count RAM twice, and has no shipped Metal execution path.

Planning estimates include weight bytes, context plus a 2,048-token state allowance, per-model cache estimate, workspace, and host memory. Where present, a context recipe minimum raises the GPU estimate. CPU RAM includes weights, state/cache, workspace and an additional 1 GiB allowance; GPU host RAM includes weights plus 2 GiB. These are conservative eligibility estimates, not measured allocation or a formal proof against OOM.

Before loading, the worker refreshes available RAM and, for CUDA, free VRAM. On Windows an owned Job Object bounds server memory; a guard samples the worker and server's working set and committed memory separately every 250 ms. CUDA whole-device telemetry is sampled about every two seconds. Memory pressure, unavailable required telemetry or loss of reserves stops the owned server. Sampled counters can miss brief peaks; Vulkan has no equivalent measured GPU-allocation guard in this release and relies on the conservative dedicated-memory estimate plus actual execution. It must not display unknown GPU telemetry as zero.

## Benchmark protocol and measurement semantics

Each candidate uses the existing owned text worker and private authenticated llama.cpp transport. Engine/model/profile checks precede execution. A warmup runs first and is excluded from scoring. Five short synthetic requests test arithmetic, exact instruction following, extraction, JSON structure and a tiny JavaScript function. JavaScript is checked with the Acorn AST; generated code is not executed. A separate continuous-list request supplies a sustained generation sample capped at 128 tokens. A valid speed score requires 96–128 native generated tokens with consistent positive native elapsed time.

The workload uses bounded output, instant mode, disabled tools and controlled benchmark requests. A candidate has a default 90-second limit, and the first-run search has an eight-minute overall budget. Candidates that fail, time out, exceed memory policy or lack a valid speed sample are never promoted to winners. `passed` means the inference run and measurement were valid; it does not mean all five quality checks passed.

Generation speed is native generated-token count divided by native generation time, not whitespace counting, characters per second or token count divided by model startup duration. Prompt speed is derived from native prefill timing. Reported TTFT is the median first generated content/reasoning latency of the post-warmup requests, excluding model load and native input fitting. **It is warm TTFT, not cold app-to-visible-answer latency.** The ordinary user-visible first-token metric can include model loading and other work and must not be substituted into this table.

RAM is sampled worker-plus-server working set and committed memory; these two counters are not added together. The public peak RAM value is working set. GPU telemetry, when available, is whole-device usage and can include Windows or other processes; it is not model-only VRAM. Vulkan/CPU GPU memory can be unknown. The short suite measures no long-context quality, tool success, sustained thermal behavior, energy use, cold-start distribution or statistical confidence interval.

## Scoring

The default relative weights are performance 30, quality 30, task suitability 20, memory 10 and stability 10. Users can set each between 0 and 100 provided at least one is nonzero. The normalized weighted result is reported on a 0–100 scale.

| Part | Current calculation |
| --- | --- |
| Performance | 50% normalized log generation speed, 25% normalized log prefill speed, 25% warm TTFT term |
| Quality | Half curated model quality prior, half the five synthetic checks' pass fraction |
| Task suitability | Mean of authored coding, reasoning and tools suitability values |
| Memory | One minus the greater of host-RAM pressure and estimated GPU-allocation pressure, clamped to 0–1 |
| Stability | 1 for a successful measured candidate; failed candidates are excluded |

Generation normalization is `clamp(log(1 + tokens/s) / log(201))`; prefill uses `log(4001)`; latency uses `1 / (1 + warmTTFTms / 1500)`. Host pressure uses the greater of measured committed and working-set peaks, falling back to the RAM estimate if committed telemetry is missing, divided by the RAM allowance. GPU pressure uses estimated candidate VRAM divided by its reserved allowance. Whole-device GPU telemetry is displayed separately and is not treated as the model allocation. Missing GPU telemetry therefore does not earn a scoring bonus.

This is a transparent selection heuristic. The quality/suitability terms contain curated priors; the five checks are too small to establish general intelligence or coding quality. The stability term is successful completion of one short run, not a long-term crash-rate measurement. Near-tied configurations can change order with normal measurement variation, available headroom or policy changes.

## Recorded local measurements

An isolated real Windows run completed all eight candidates in about 123.4 seconds on an AMD Ryzen 7 9800X3D, NVIDIA RTX 5090 (CC 12.0), and approximately 77.6 GiB reported physical RAM, while retaining the **32 GiB AI ceiling**. The separate AMD integrated adapter was inventoried; these GPU inference measurements used the NVIDIA adapter. AMD and Intel fixture tests are not real AMD/Intel inference measurements.

The stable sanitized inference evidence contains the eight rows, final scores, hardware/policy metadata and cache/chat verification. `.test-temp/optimizer-live-result.json` contains the original run; `.test-temp/optimizer-live-cached-result.json` reuses and rescores the same measurements with the final GPU-headroom memory term. The following scores are from that rescore. All models here are Q4_K_M; Spark uses 8,192-token context and Swift/Focus use 16,384.

| Model | Engine | FA/cache variant | Native tokens/s | Warm TTFT ms | Native prompt tokens/s | Peak RAM GiB | Whole-device GPU GiB | Checks | Final score |
| --- | --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Spark | CPU | off/f16 | 22.44 | 336.0 | 179.16 | 2.06 | unknown | 4/5 | 62.69 |
| Focus | CUDA | off/f16 | 192.34 | 94.0 | 723.63 | 5.58 | 11.32 | 5/5 | 83.24 |
| Swift | CUDA | off/f16 | 230.85 | 31.5 | 1,807.83 | 3.03 | 11.25 | 5/5 | 79.66 |
| Spark | CUDA | off/f16 | 397.98 | 78.0 | 702.30 | 1.97 | 7.07 | 3/5 | 67.36 |
| Focus | Vulkan | off/f16 | 153.16 | 78.0 | 879.07 | 5.15 | unknown | 5/5 | 82.84 |
| Focus | CUDA | on/q8_0 | 185.43 | 109.5 | 592.85 | 5.36 | 10.79 | 5/5 | 82.89 |
| Swift | CUDA | on/q8_0 | 215.84 | 32.0 | 2,278.49 | 2.82 | 8.91 | 5/5 | 80.03 |
| Spark | CUDA | on/q8_0 | 396.60 | 63.0 | 953.24 | 1.75 | 6.58 | 4/5 | 70.71 |

The original run's older memory score selected Focus/Vulkan at 84.03 versus Focus/CUDA at 84.01. The final rescore selects Focus/CUDA at 83.24 versus Focus/Vulkan at 82.84. The native metrics did not change during rescore. This is a close policy-dependent comparison, not evidence that one backend universally wins. Cached reuse and a subsequent normal chat were verified; the normal chat's cold/user-visible latency is deliberately excluded from the warm benchmark table.

## Cache, application and failure recovery

`hardware_profile.json` in Arc's local user-data directory stores public capability metadata, measured records, selected ID and public configuration summaries. `ai-performance.json` stores preferences. Writes use a fresh temporary file and atomic rename. Benchmark prompts are fixed synthetic text; the profile stores measurements and check outcomes, not conversations or generated answer text.

Full inspection computes a cache key from policy/benchmark version, app major/minor version, hardware capability signature, RAM ceiling, backend signatures/availability and installed model identity. It redetects hardware, verifies engines, rebuilds candidates against current memory limits and recomputes scores. A matching public cache can reuse measurements after this inspection; it does not trust a saved score or arbitrary saved executable path. Changed weights rescore existing measurements. Changed budgets, relevant hardware/driver/model/backend identity found during inspection, or invalid records require renewed eligibility/testing. Saved explicit contexts are reconstructed only if still supported and fitting.

Ordinary startup first attempts the operating-system-protected runtime profile, avoiding repeated full discovery and benchmarks. Saved measurements are revalidated and rescored; the runtime still verifies model/backend files and refreshes memory headroom before loading. The automatic catalog refresh waits for at least three observed app-version changes and a changed model catalog. An unchanged catalog resets that update counter. This is not a continuous background driver-change detector: a driver/hardware change can be discovered by an explicit check, full inspection or a runtime failure. Missing, invalid or unusable protected profiles fall back to full inspection. **Run check again** remains available for an immediate refresh.

Preference changes, candidate application and benchmark work are serialized. An active reply, voice session, installation or conflicting GPU work blocks optimization. CPU workers bypass the shared GPU lease; GPU trials use the existing coordinator. Cancellation cleans up only owned processes. A failed application restores the previous configuration when possible.

Recoverable load, resource, memory or backend failures quarantine the failed result and try another previously measured eligible result. Same-model generation can retry only before any public content or reasoning was delivered. A different model requires the user to resend the question; tool execution is outside this retry and is never replayed. Voice sessions, cancellation and validation errors are not automatically retried. Exhausting tested fallbacks suspends inference and requests a new check; an unavailable manual override returns to automatic mode rather than persisting a broken selection.

## Validation and evidence limits

The Python suite passed **249/249** (`.test-temp/all-python-tests-0.48.0.log`). Focused installer coverage includes all 36 installer tests, exact JS/Python recipe agreement, offline bundle copying, corruption fallback, cancellation and preservation. The complete Node suite passed with zero failures and five existing skips; the exact tally is in `.test-temp/full-tests-0.48.0.log`. Node coverage exercises hardware fixtures, trusted-file gates, CPU/CUDA/Vulkan profiles, scoring/cache behavior, concurrency, memory policies and bounded recovery.

UI QA uses the actual `PerformancePanel` and `FirstLaunchSetup` components with a synthetic backend: six interaction groups passed across eight layouts (standalone and first-run at 1536×864, 1280×720, 1024×576 and 390×844), with no renderer errors or document overflow. It does not execute models or prove a real driver's behavior. Evidence is `work/qa/performance-ui/results.json`.

The real eight-candidate run verifies Windows CPU, CUDA and Vulkan inference on the stated machine, not all GPU generations. Packaged verification is provided by `scripts/check-inference-package.cjs`: it checks shipped files and bounded initialization without model inference or downloads. Merely having that checker does not establish that every future installer has passed it; retain the build's actual report.

## Later phases

The current release does not implement TensorRT-LLM, HIP/ROCm, SYCL or Metal execution; FP4/NVFP4 model assets; speculative draft decoding; multi-GPU scheduling; automatic quantization conversion; exhaustive parameter tuning; or production macOS/Linux support. Architectural precision metadata does not remove these limits.

Later work needs separately pinned platform runtimes and model formats, per-adapter integration tests, native memory telemetry where absent, repeated/cold/long-context measurements, broader quality evaluations and confidence-aware ranking. More advanced offload, cache-precision and draft-model combinations must be tested as complete configurations within the memory policy. Additional downloads require their own reviewed recipes and approval. None of these later phases is represented as complete by 0.48.0.
