# Review before replying

Choose **Think deeply → High or Ultra**, then open the thinking-level menu.
**Review before replying** is enabled by default and can be turned off there.
Its preference is stored with Arc's other protected settings. Instant, Medium
and voice do not run these additional calls.

High reviews a buffered final draft against the request and available evidence.
Ultra makes a second review, including after a correction when one was needed.
One correction is allowed per turn: at most two extra calls for High and three
for Ultra. The model is told to look for missing requirements, contradictions,
wrong assumptions, calculation errors, repetition and unsupported claims—not
rewrite a good answer just to change its style.

Arc also adds a short outcome checklist to eligible parent work calls: establish
the required deliverables and constraints, identify what would demonstrate
success, challenge an assumption, then choose the next useful permitted step.
This is an instruction to the existing model, not a change to model weights or
a new native reasoning engine. It supplements the model's existing thinking
budget and Arc's task, source and artifact checks.

## What the checks can and cannot do

The final draft stays buffered until review finishes. Internal review output is
not streamed into chat. The interface shows brief progress and a small summary
of checks/corrections, without exposing hidden reasoning. A check is a model
opinion about the available text, not proof of correctness.

Reviews and the single correction run with **no executable tools**. They cannot
repeat a post, send an email, alter a saved file, authorize a new action, bypass
UAC or change plugin/sandbox permissions. The original tool loop performs actual
work. Its deterministic completion and evidence gates run after review and still
control whether the task is incomplete. Saved code needs real syntax/behavior
tests; screenshots need their separate vision analysis receipts.

The same selected model is reused serially in the existing inference lane; Arc
does not load a second reviewer model. More calls consume time and tokens. The
selected context window and inference residency rules remain unchanged. The
separate optional CPU code reviewer remains independent.

## Bounds and failure behavior

- Up to two review verdicts and one correction; no recursive review loop.
- A total 90-second review deadline, with at most 45 seconds per call. Stop is
  forwarded immediately; Arc waits for native cancellation cleanup before
  releasing inference ownership.
- Review output is strictly parsed as a small JSON verdict and at most four
  short concrete issues. Tool calls, malformed or truncated JSON, and a runtime
  context-trimming signal cannot count as a successful review.
- Keep the original request, retained evidence and full candidate intact. If
  that plus the review/output reserve will not fit, skip review instead of
  silently dropping requirements. Native token checks remain authoritative;
  the conservative estimate is not a larger context window.
- Invalid, unavailable or timed-out reviews preserve the usable draft and show
  that the extra check did not finish. An invalid/truncated correction is discarded.
- Already blocked, visibly streamed or incomplete drafts are not restarted for
  review. Large truncated replies and subsequent continuation sections are not
  certified as fully reviewed. Dedicated project-file generation retains its
  own checks; this feature does not claim to review every saved file.

The final status says **draft review**, not “verified correct.” Remaining review
gaps are displayed as such. Real source reads, tests, exact file versions and
the user's requested result remain the basis for assessing completion.

## Validation

`tests/answer-review.test.cjs` exercises wrong-answer correction, a second check,
call bounds, strict verdict parsing, context preservation, cancellation cleanup,
timeouts, durable metadata validation and the actual agent finalization path.
Settings tests cover defaults, persistence and legacy saves. UI tests cover
honest labels. `scripts/answer-review-live-check.cjs` explicitly evaluates neutral
fixtures with the installed Arc Focus model and shared GPU coordinator; it does
not use private files, web research or external mutations.
