# Thinking levels

Arc offers Medium, High and Ultra within Think deeply. Instant remains available for short, direct replies. A level is a work allowance for the selected local model, not a new model or a guarantee of better answers.

| Level | Intended use | Work behavior |
| --- | --- | --- |
| Medium | A focused answer or a smaller task | Keeps the established bounded tool loop and default reasoning allowance. |
| High | More investigation, coding and verification | Allows more tool work and a larger native reasoning budget where the model supports it. |
| Ultra | Longer tasks with several steps | Continues through bounded work stages while useful progress is being made, compacting working context as needed. |

Choose the level beside Think deeply in the composer; Arc saves it with your preferences. The work status shows the stage, actions and context refreshes. These are application status updates; Arc does not expose hidden model reasoning.

When Ultra is selected, the optional **Until done** control enables long-file delivery for the next message. Arc writes a requested new text or code file in chunks of at most 6,000 characters, verifies the file hash after each append, and keeps a short section checkpoint in the active context instead of carrying the full draft forward. The output remains in Arc Created Files and is never run. Stop, a repeated failure, or a 512 KiB / 128-chunk safety limit preserves any partial file and marks the task incomplete. The choice resets after the message starts; it does not change the selected model or context window. Existing-file repairs continue to require the exact original and do not use chunked replacement.

Ultra already uses compact checkpoints while it researches and acts through tools. A very long final chat message is still limited by the model's response budget; for a long report, ask Arc to save a `.md` or `.txt` file and enable **Until done**.

## How Ultra ends

Ultra continues while it is doing useful work. It ends when the model returns a final result, the user presses Stop, or a concrete blocker prevents further work. Missing access, repeated failures, repeated changes, no new useful evidence, runtime errors and context that cannot safely fit can stop the task. Arc reports an incomplete result when its tracked task requirements have not been met. A model's final response alone is not proof that all possible bugs or omissions were found.

Already saved files and confirmed action receipts are kept. Do not repeat an uncertain external action simply because its reply was interrupted. Closing or restarting the application does not automatically resume an unfinished Ultra turn; inspect the saved results and continue the remaining work in a new message.

Stop sends cancellation immediately, then Arc waits for its active native request and helpers to acknowledge cleanup before releasing task ownership. That cleanup can take a short time; it prevents another GPU job from starting while the cancelled request is still using the device.

## Context and originals

Ultra uses the existing saved-context system for retrieval and refreshes older tool exchanges in its active working context. The current request, application instructions, recent exact tool evidence and progress receipts remain available. Older originals and recorded observations remain on SSD when their normal persistence succeeds. Arc must read the current original again before an edit if its exact source has left working context.

Compaction does not enlarge the model's physical context window, preserve every detail simultaneously, or turn 16K active context into 128K active context. The configured model and context allocation remain unchanged. If protected instructions and the latest required evidence cannot fit, Arc stops with a context blocker instead of silently discarding them.

## Helpers and CPU review

Existing enabled helper tools can start up to 24 read-only helpers total in one parent turn, including cancelled or failed helpers. A queue admits at most five helpers at a time. Substantial High and Ultra tasks can plan a five-helper team across preparation and review; simple questions and fast voice replies do not need this overhead. Helpers can gather public research, inspect explicitly shared Created File IDs, and propose or review code. Their findings are evidence for the main task to verify. They cannot modify files, execute code, control private browser/SSH/PC sessions, or spawn more helpers.

Helpers share the selected model through Arc's inference queue. Some read-only tool work may overlap; Arc does not load one GPU model for each helper. Task records and reports use ordinary RAM and local storage; each inference request still needs working context and computation. Additional helpers can increase completion time. Turning on High or Ultra does not enable disabled plugins, establish connections, or turn on optional CPU review. CPU review keeps its separate toggle.

## Native reasoning budgets

For models that support native reasoning, the worker sets a per-request reasoning ceiling inside the existing output reserve:

| Request | Medium | High | Ultra |
| --- | ---: | ---: | ---: |
| Typed deep reply, 16K or larger context | 3,072 | 4,096 | 5,120 |
| Typed deep reply, 8K context | 1,536 | 2,048 | 2,560 |
| Required file creation, 16K or larger context | 1,024 | 1,536 | 2,048 |

These are token ceilings, not fixed token consumption or measured quality scores. File creation keeps more of its output reserve for complete source code and tool-call framing. A smaller per-request output cap scales the reasoning budget down; caps below 512 tokens disable native reasoning for that call. Total deep output reservation stays at most 8,192 tokens and at most half the selected context window. Reasoning and the final answer share that reservation.

Ultra is Arc's work-tier name. Native templates receive the supported `high` effort value for High and Ultra, with different numeric budgets. Non-thinking models keep native reasoning disabled and benefit only from the surrounding work orchestration; they do not gain a hidden reasoning mode. Voice keeps its existing Medium allowance and compact context regardless of the saved typed level.

The pinned llama.cpp b10590 server accepts the per-request `reasoning_budget_tokens` setting: [upstream implementation](https://github.com/ggml-org/llama.cpp/blob/b10590/tools/server/server-common.cpp#L1260-L1273). Arc does not need to replace the runtime or download another model to select a thinking level.

## Verification scope

Offline worker tests cover tier mapping, small-context and output-cap limits, source-code space, voice behavior, reasoning-disabled models and validation before loading. Orchestration tests use controlled fixtures for tool progress, compaction, Stop and incomplete results. These checks do not establish a universal coding-accuracy improvement or replace testing the selected model on the user's actual task.
