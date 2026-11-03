# Image prompt authoring in Arc

Arc distinguishes writing an image-generation prompt from generating an image, describing a reference, researching a model, or operating an image editor. A request such as “Write a Krea2 prompt for a lighthouse at dusk” should return usable prompt text directly. It should not produce a scene-analysis essay, a tool plan, or a warning about screenshots that were never requested.

Krea2 prompts are themselves descriptive prose. The distinction is the deliverable: direct visual instructions ready to paste into the target workflow, rather than commentary explaining what the imagined image would contain.

## Target model and output

`electron/services/image-prompt-authoring.cjs` recognizes explicit model families and supplies scoped formatting guidance. A conversion destination takes priority over a source model. Related follow-ups can retain the previous requested model; quoted example content does not choose a new target. Fresh requests do not automatically inherit an earlier batch size or word requirement. Missing model details use portable natural language instead of an invented checkpoint-specific recipe.

| Target | Default authoring guidance |
| --- | --- |
| Krea 2, including RAW/Turbo names | One cohesive natural-language paragraph describing the subject, action, composition, materials, light and setting. Preserve supplied details without expanding an already detailed source into an essay. |
| FLUX.1 Krea | Natural-language visual description. This is a separate model family from Krea 2. |
| FLUX / FLUX.2 | Clear subject, action, style and spatial context. FLUX.2 defaults to positive wording without an appended negative-prompt field. |
| SDXL / Stable Diffusion 1.5 | Compact descriptive phrases or tags, with important subject/action/composition first and no redundant quality-keyword stack. |
| Illustrious / WAI-Illustrious | Concrete illustration tags and descriptive phrases grouped by subject, pose, composition and style. Do not import Pony score tags automatically. |
| Explicit Pony version | Preserve version distinctions. Pony V7 is not SDXL; avoid inventing version-specific score tags or settings. |

These are authoring defaults, not fixed model input limits. The user's explicit subject, style, format, count and length still control the request. Settings such as sampler, steps, CFG and resolution belong outside the prompt and are included only when requested. Arc does not invent LoRA names or claim that a LoRA trained for one architecture works with another. Frontend weighting syntax is not universal model syntax.

## Runtime routes

| Request | Route |
| --- | --- |
| Write, convert, or improve ordinary image-prompt text | Dedicated text-authoring path, without optional guide selection, task interpretation, browser access, desktop inspection, or unrelated tool loops. |
| Write a prompt from an attached image | Vision supplies reference observations; the text author then applies the requested model and output contract. Visible details and uncertainty remain reference evidence. |
| Research a topic, then write a prompt | Existing research agent with additional prompt-authoring guidance. Research remains subject to its real tools and evidence. |
| Inspect the current browser page, then write a prompt | Existing browser/vision path. The explicit inspection is retained and must return actual visual evidence. |
| Write a prompt and save it to a file | Existing file-authoring path, preserving file permissions and saved-file receipts. |
| Request JSON, tables, sections or another structured format | Existing general agent with authoring guidance, rather than forcing the paragraph-only batch formatter onto that structure. |
| Write code for a prompt generator | Existing coding/file path. A programming request is not mistaken for a request to author a scene prompt. |
| Describe an image without requesting a generation prompt | Normal visual description; no forced prompt conversion. |

The pure text route starts before PC readiness checks and semantic task interpretation. Its guide preflight also exits before loading optional recipes. Existing system instructions and content boundaries remain present. Image observations are untrusted evidence and cannot authorize tools or change the requested model.

For mixed tasks, the agent retains requested work. Generic scene words such as “appearance,” “composition,” and “describe” no longer make prompt completion depend on an unrequested browser screenshot. Explicit screenshot/visual-research requests continue to use the existing evidence checks.

## Count, length and correction

The dedicated path supports batches of 1–32 prompts. It writes one requested prompt per model call, then adds numbering when assembling a multi-prompt result. Each next prompt receives short openings from completed prompts to reduce repeated scenes.

After each draft, Arc checks for:

- An empty result or known explanatory preambles and completion claims.
- Extra headings, bullets or paragraphs when a single paragraph was requested.
- Explicit word-count requirements, including ranges and total batch words.
- An exact duplicate of an already completed prompt.
- Reported output truncation.

Word checks use the application's Unicode-aware word counter, not a model's claimed count. Total batch word budgets are distributed across the individual prompts. This is not a tokenizer and does not promise an exact model-token count.

An invalid draft gets at most two correction attempts. A truncated individual prompt can receive at most four continuation calls per attempt. Corrections and continuations have no tools available, cannot silently start image generation, and remain cancellable. The application preserves completed prompts and reports incomplete work if the requested contract still cannot be satisfied. A refusal or necessary clarification is not repeatedly rewritten to get around the model's content boundaries.

The dedicated checker is intentionally narrow. It cannot prove that a scene is visually coherent, that every constraint will be followed by the image model, or that a generated image will match a reference. Exact duplicate detection is not a full semantic similarity check. Structured/mixed requests use the general agent rather than this paragraph/batch validation path.

## Implementation and validation

- `electron/services/image-prompt-authoring.cjs`: task resolution, family guidance, draft checks and bounded prompt generation.
- `electron/services/tool-agent.cjs`: prompt-only route, optional-guide bypass, mixed-task instructions and visual completion gating.
- `electron/services/task-intent.cjs`: prompt authoring distinguished from visual research and executable work.
- `electron/main.cjs` and `electron/services/live-context-guidance.cjs`: eager-guide selection and image-to-text handoff.
- `tests/image-prompt-authoring.test.cjs`: model resolution and output-contract unit coverage.
- `tests/image-prompt-agent.test.cjs`: integrated prompt-only, research, file, coding and browser-inspection cases.
- Existing image, task-intent, visual-research and workstation tests protect neighboring behavior.

These changes alter application routing and instructions. They do not train a LoRA, modify model weights, or establish that every local text model will follow the instructions equally well. Automated tests use controlled model responses; installed-model quality checks should be recorded separately from those tests.

## Primary-source guidance

The model-family distinctions and authoring guidance were reviewed on November 2, 2026. Model cards and frontend conventions may change.

- [Krea 2 official prompting guide](https://github.com/krea-ai/krea-2/blob/main/docs/prompting.md) and [prompt expansion instructions](https://github.com/krea-ai/krea-2/blob/main/docs/expansion.txt): coherent descriptions, preservation of supplied detail and restrained expansion.
- [Krea 2 official repository](https://github.com/krea-ai/krea-2): RAW and Turbo inference differ; do not present a single negative-prompt/CFG rule as universal to every Krea2 workflow.
- [FLUX.1 Krea model card](https://huggingface.co/black-forest-labs/FLUX.1-Krea-dev): separate FLUX.1-based model identity.
- [FLUX.2 official prompting guide](https://docs.bfl.ai/guides/prompting_guide_flux2): natural-language subject/action/style/context and positive phrasing.
- [Diffusers SDXL reference](https://huggingface.co/docs/diffusers/api/pipelines/stable_diffusion/stable_diffusion_xl): prompt and negative-prompt inputs and their guidance behavior.
- [Illustrious author model card](https://huggingface.co/OnomaAIResearch/Illustrious-xl-early-release-v0): illustration prompting conventions and conflicting framing considerations.
- [Pony V7 author model card](https://huggingface.co/purplesmartai/pony-v7-base): version-specific architecture and prompting guidance.
- [AUTOMATIC1111 attention documentation](https://github.com/AUTOMATIC1111/stable-diffusion-webui/wiki/Features#attentionemphasis): weighting syntax belongs to the frontend rather than every model.
