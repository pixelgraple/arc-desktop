# Arc 0.51.90 — Model-aware image prompt writing

Image-prompt requests now have a dedicated authoring route. Arc detects targets such as Krea2, FLUX, SDXL and Illustrious and writes ready-to-paste prompt text instead of turning the task into a scene-analysis essay or a browser-research workflow.

- Krea2 receives cohesive natural-language prompts; SDXL and illustration checkpoints receive appropriately compact phrasing or tags. Distinct Krea/FLUX and Pony versions remain separate.
- Pure prompt requests skip unrelated task interpretation, optional guides, PC checks and browser tools. Descriptive scene words no longer produce an unrequested “Visual research is incomplete” warning.
- Explicit prompt counts and word requirements are checked. Batches are written one prompt at a time with bounded correction and continuation, preserving completed work if a later item cannot finish.
- Reference-image requests retain visible observations and the user's latest corrections. Requested research, browser inspection, file saving and prompt-generator coding keep their existing tools and evidence requirements.
- Existing system instructions and content boundaries remain in force. No model weights or LoRAs are trained by this update.

Validation on November 2, 2026: 133 related unit/integration checks and four Electron IPC routing checks passed; TypeScript and the production frontend build passed. Installed Arc Focus (Qwen3 4B Instruct 2507 Q4_K_M) also passed CPU-only live checks: a 118-word Krea2 paragraph, a 49-word SDXL tag prompt, and a three-prompt Krea2 batch measuring 100, 100 and 90 words within the requested 60–100 range. The live batch test exposed a parser bug where “one paragraph per prompt” overrode the batch count; this was fixed and the failed case rerun successfully.

The two unrelated broader-suite failures found during investigation (a task-memory checkpoint fixture and a slide question routed to public-fact research) also reproduce with the runtime changes reversed. This release does not claim the entire legacy test suite passes. Vision handoff is covered by controlled tests; no new image was generated, and the live text checks used no GPU inference.

See [Image prompt authoring](https://github.com/pixelgraple/arc-desktop/blob/v0.51.92/releases/0.51.92/IMAGE_PROMPT_AUTHORING.md) for route behavior, limits and primary model-documentation sources.
