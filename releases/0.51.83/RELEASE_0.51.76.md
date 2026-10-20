# Arc 0.51.76 — finish research with an answer

Fixed an Ultra failure in which an explanation question collected sources, repeatedly compacted context and ended with only “Work paused.” The affected installed 0.51.75 diagnostic recorded 15 tool-phase model attempts across approximately 215 seconds and no final composition attempt.

- When read-only research stops producing new evidence, Arc now makes one tool-free, evidence-based answer attempt. It retains the original question, source evidence and uncertainty rather than reopening the search loop.
- Read-only research uses a bounded work allowance even in Ultra; it proceeds to an answer instead of automatically starting another research epoch. File writing and other action workflows retain their existing continuation behavior.
- Permission, access, repeated-failure and repeated-mutation stops remain authoritative. An idle round can no longer overwrite one of those stops with a softer no-progress reason.
- A failed, empty or plan-only final attempt reports the specific composition failure. Cancellation still stops immediately. A truncated answer remains marked incomplete. Missing files, screenshots and explicitly requested source counts are not declared complete by this recovery.

Validation includes focused regression tests at 8K and 16K context and three canaries using the already-loaded local Qwen3.8-27B model with isolated documentation fixtures. The canaries cover two induced research stalls and an ordinary two-source research request. They verify the orchestration and final-answer path, not every factual claim or arbitrary live website behavior. No model files, account permissions or saved conversations are changed by this release.
