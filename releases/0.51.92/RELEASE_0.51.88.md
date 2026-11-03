# Arc 0.51.88 — Desktop task continuity

This update addresses the connected computer-use failure reproduced in 0.51.87.
Verification evidence and any remaining limitations are recorded separately in
QA_0.51.88.md. Package availability alone does not establish native-task success.

## Continuing a desktop task

When the model answers before a requested PC action has completed, Arc keeps
the original request and verified tool evidence available for the next step.
Application continuation instructions use a valid conversation boundary instead
of producing an assistant-only tail rejected by the runtime.

Desktop working context retains the currently relevant tool schemas and
observed app/window identities. Input still requires fresh native observations;
retained history cannot authorize replaying a stale click or typing action.

Direct desktop tasks load their enabled app and desktop schemas before the
first model request. A focused prompt preserves Arc's protected machine and
content boundaries while leaving unrelated tool guides available on demand.
This reduces repeated preparation on smaller models and CPU configurations.
Housekeeping reports a context refresh only when it actually changes context.

For requests involving interaction inside an application, launching the app is
not sufficient evidence of completion. Arc must perform the requested work and
inspect the resulting state. Receipts establish what happened, and do not by
themselves prove that the result satisfies the user's request.

Existing session permissions, foreground checks and native action validation
remain in effect. These changes do not alter model weights or enable access
without the user's connection in Settings > This PC.
