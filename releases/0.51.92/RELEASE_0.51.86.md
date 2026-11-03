# Arc 0.51.86 — Computer use and checked task completion

## Desktop controls

Settings > This PC now includes **Mouse, keyboard and screen**, initially off.
After connecting with that permission, Arc can list visible Windows applications,
inspect their controls, focus a window, click, replace text in editable fields,
use supported keyboard shortcuts, scroll and capture a window. Screenshots can
be analyzed by the installed local vision component. No new cloud service is
required.

Each input consumes a fresh window observation. Arc must inspect the window again
before treating the interaction as checked. Supported controls use accessibility
IDs instead of guessed coordinates. Native Windows EDIT/BUTTON fallback handles
applications whose accessibility provider lacks useful control information.
Desktop captures are bounded to twelve recent images, pruned after 24 hours on
the next capture.

These are Windows tools, not a promise that every application is supported.
They operate standard-user windows in the current session. Arc control windows,
terminal windows, credential dialogs and protected password fields are excluded.
Custom canvases, inaccessible editors and drag-and-drop may need user help.

## More reliable task completion

An additional outcome ledger tracks explicitly requested files and applications
separately. Saving one file cannot complete a two-file request. Opening one app
cannot complete a request for two apps. The model gets concise reminders about
unfinished steps, with bounded continuation and an honest incomplete result when
the required evidence is missing.

Existing High/Ultra draft review remains available. These changes improve tool
use and verification; they do not modify model weights, expand context or
guarantee correct reasoning. Clarifications and missing permissions remain
visible rather than causing endless retries.

## SSH repairs

- A valid pasted private key with surrounding notes now loads correctly. Only
  one complete key block is accepted; ambiguous multiple keys are rejected.
- Arc can connect an already saved, trusted SSH profile for a requested server
  task without exposing credentials to the model. Missing setup is explained.
- Fresh saved connections start read-only. Editing and command permissions stay
  explicit session switches; a stopped session is not silently restarted.
- Requests such as “on my connected SSH server, find the project and create a
  file” correctly route to SSH rather than the local Created Files plugin.
- Existing exact paths, current-file hashes, verified original backups, atomic
  writes and post-save verification are retained.
- Explicit named-project requests now block changes in a different active
  folder. Verified discovery and selection must establish the requested scope.
  A successful save in the wrong project cannot complete the task. Quoted file
  content and negative instructions do not introduce extra project targets.
- Connection feedback distinguishes already confirmed status from unfinished
  file work, reducing repeated status checks. Failure and cancellation limits
  remain bounded.
- Compound app launches such as “Open Notepad and Calculator” now recognize
  both explicitly requested apps.

## Verification

Actual Windows desktop acceptance passed nine checks, including typing, mouse
save, independent saved-file verification, keyboard input and a screenshot.
Actual SSH service acceptance passed fourteen checks on a disposable server
project, including discovery, create, edit, backup, stale-hash rejection, PHP
lint, readback, download and saved reconnect. No live website files were edited.

Prompt-driven Arc Focus tests passed local create/edit/readback, native desktop
typing/clicking/save verification, and actual SSH discovery/create/edit/backup/
readback. An independent read of the test files checked the exact final bytes.
Calculator was exercised through real controls (2 + 2 = 4), and the installed
local vision model read the neutral native screenshot's message and Saved label
exactly. Detailed attempts, repairs and regression results are recorded with
the release QA report; these bounded cases are not a general success-rate claim.
