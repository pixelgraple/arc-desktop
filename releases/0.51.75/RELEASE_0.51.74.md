# Arc 0.51.74 — questionnaire-based email assistance

- Added Settings → Email assistant and a Plugins entry for browser-connected Gmail, Outlook and Proton Mail adapters.
- Added a questionnaire for the observed mailbox, nine optional categories, per-category instructions/examples, general tone, allowed/excluded senders and a 30–3,600-second pace. One explicit review starts one inbox pass.
- Added sequential local-model classification/reply drafting with a fresh context per message, no model tools, no cross-email history and bounded input/output.
- Added persistent delivery identities, atomic submit-intent recording, exact recipient/body checks and sent-message verification. Uncertain attempts are held without automatic retries; restart requires review.
- Added current-inbox progress, pause/stop, competing-work gates, login/CAPTCHA handling and existing-draft preservation.
- Added four detailed email/Reddit workflows, including timed background posts and duplicate recovery. Chat can check or pause email work, but cannot approve a run.
- No new dependencies. Existing Reddit posting approval remains unchanged.

Initial browser compatibility uses fixed English DOM patterns and supported paginated inbox views. Unknown layouts fail closed. Tests use synthetic mailboxes; no real email was sent. Live account compatibility and delivery need a supervised trial on the user's actual provider. See [Email assistant](EMAIL_ASSISTANT.md) for setup, retained state and limits.
