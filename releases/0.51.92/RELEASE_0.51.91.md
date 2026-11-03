# Arc 0.51.91 — Image prompt runtime repair

Image-prompt requests at the normal 16,384-token context could fail immediately in 0.51.90. The authoring route requested 6,553 output tokens even though the native runtime accepts at most 4,096 per call. The request was rejected before inference, but the prompt writer incorrectly reported that the model had stopped responding.

- The prompt writer now shares the runtime's output limit. Drafts, corrections, continuation calls and later prompts in a batch all respect it. This does not reduce the selected context window or remove continuation support.
- Failures retain a safe error category and actionable explanation instead of incorrectly reporting every rejection as a stopped model.
- Local turn diagnostics now record prompt-writing progress and model-attempt categories. They omit prompts, raw exceptions, file paths and model outputs.

Regression coverage goes through the real tool agent, context-fallback adapter and native runtime validation at all supported context sizes, including the Qwen3 4B model currently labeled Arc Swift at 16,384 tokens with Extended work/Ultra. The native worker in these deterministic tests is a fixture; separate CPU acceptance uses those installed weights and the real Python worker. The exact reported beach prompt completed in one call with an 83-word result. The previous live test used an 8,192-token context and direct HTTP, which missed the default-context boundary.

Validation results and the live acceptance record are saved under `work/qa/` and `.test-output/` in the development workspace. No model weights, LoRAs, user inference preferences or GPU jobs are changed by this repair.
