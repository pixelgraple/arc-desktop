# Arc 0.51.87 — Clearer desktop setup and usable controls in smaller contexts

Release candidate. Final acceptance, packaging and installation are pending;
see `QA_0.51.87.md` for the evidence checklist and current verification status.

## Computer-use setup

An explicit computer-use request checks the current This PC connection and
permissions before starting the model. If Arc is disconnected, or the requested
app and desktop permissions are missing, the reply points to Settings > This PC
and explains the needed switches. It does not silently connect the computer or
enable a permission.

## Natural launch requests

Direct requests such as “use my computer and open Calculator” retain the
explicitly requested app when launch permission is checked. The existing
restrictions on quoted instructions, explanations and negated requests remain
in place.

## Smaller desktop observations

Straightforward native desktop tasks initially receive the three PC tools and
tool discovery, instead of unrelated web and file schemas. Other enabled tools
remain available on demand; mixed desktop, browser and file tasks keep their
broader tool selection.

Desktop observations keep compact, actionable controls when the model has a
small context budget. Calculator's keypad and result display are preserved
instead of dropping the controls at the end of the accessibility tree.
Actionable element IDs remain tied to the original native observation. The
result states when structural details or controls were omitted and still
requires a fresh inspection after input.

## Model startup and resource feedback

Known startup, model, configuration and memory failures have specific user-facing
messages rather than falling through to a generic unfinished reply. Diagnostics
retain the failure category, while the UI uses fixed application messages.

Temporary RAM or VRAM pressure leaves the user's selected model and successful
benchmark records intact. It does not automatically replace the selected model
with another model. Selecting an untested model from a protected cached profile
now refreshes current resource availability before planning its compatibility
test; cached free-memory measurements no longer stand in for current capacity.
Verified engine capabilities are reused without repeating the full backend
discovery just to refresh available memory.

The while-away recap preserves the unfinished status of a reply and separates
that status from individual tool steps that completed. A completed housekeeping
step does not make an unfinished user task complete.

Model cancellation and unloading now have bounded acknowledgments. An
unresponsive Arc-owned worker receives a graceful shutdown, then termination
of that exact worker if needed. Replacement is blocked when its exit cannot be
confirmed. Unrelated applications and their processes are not targeted.

These changes do not alter the model's weights or guarantee that every desktop
application can be controlled. Existing foreground, observation, target and
permission checks remain active. The exact Calculator task that prompted this
release must pass live acceptance before the update is marked ready.
