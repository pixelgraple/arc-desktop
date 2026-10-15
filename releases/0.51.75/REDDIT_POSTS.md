# One reviewed Reddit post, now or later

Arc can prepare a Reddit text post and submit the exact post you approve once, either immediately or at a chosen date and time. Approval happens in advance. A request in chat creates a draft and proposed time, not permission for the model to publish without review. There are no recurring schedules or multi-post batches.

## Setup and review

1. Enable **Reddit drafts and timed posts** and **Browser control** in Plugins. Choose and connect your normal browser in **Settings → Browser**.
2. Open **Settings → Reddit posts**, enter the community, and inspect it. Check the observed signed-in account and community rules. Sign in yourself if needed.
3. Type the exact title and body or generate one draft with your selected model. Chat can also prepare it: “Make a post in three hours in r/MyCommunity with this title and description…”
4. Select one saved draft and review its full text. Choose **Post once now** or an exact future local time. Review the timezone and UTC offset as well as the account, community and wording.
5. Confirm the review and approve that one submission. Arc keeps the schedule locally and handles it while open and awake. Pause or cancel before submission whenever needed.

The title allows 300 characters. The final body allows 4,000 characters including the existing visible disclosure, “Posted with Arc automated assistance; reviewed by the account owner.” Editing content or its proposed time invalidates prior approval. No new text is generated at execution time.

## Relative times and the clock

The chat tool can save `requestedDelayMinutes:180` for “in three hours.” Arc resolves that delay once when saving and returns a concrete timestamp. Editing a draft without resupplying its time preserves that timestamp. Absolute requests use a UTC epoch timestamp and an IANA timezone; the model reads the fresh computer clock first. An ambiguous date or AM/PM requires clarification.

The review page shows the resolved local date and time, including timezone and offset. Nonexistent daylight-saving times cannot be approved; repeated local times require choosing the intended offset. A missing or past timestamp never silently becomes Post now. That is a separate explicit choice.

The timer checks the system clock at least every thirty seconds while waiting, without running a model or keeping generation active. Timing is best effort. Other Arc work may delay a due post briefly, with a maximum five-minute lateness allowance while running, including browser preparation. Beyond that, Arc pauses for a newly approved time rather than publishing unexpectedly.

## Background browser behavior

Arc uses the personal browser selected in Settings and searches the tabs exposed by that connected session. It reuses an appropriate Reddit tab, preserves unfinished editors and creates a new background tab when needed. Selection and creation for scheduled posting do not call the browser's foreground activation command. The initial browser launch, remote-debugging permission or sign-in can still require visible user interaction.

Private tabs work only if the selected, authorized connection exposes that context. Arc does not copy cookies or passwords, close a private window, or silently switch to a different profile to make a post work. An inaccessible session pauses the task. The built-in separate research browser is not a silent fallback for authenticated publishing.

Arc can remain minimized, but it must remain running and the PC awake. Do not change the specific posting editor while Arc is submitting. Sign-in, CAPTCHA, account restrictions, rate limits, unsupported layouts and required unsupported fields stop the attempt. There is no CAPTCHA bypass or automatic retry after an uncertain submit.

## Persistence and interruption

The approved copy binds account, subreddit, title, body, draft revision, exact run time and timezone. Only one active post is allowed. The editable library holds up to ten drafts and history retains up to two hundred receipts. State is saved atomically in local `reddit-publisher.json` application data; it is plain local application data, not encrypted credential storage.

An active, valid future schedule survives an Arc restart. An already missed time requires fresh review. A deliberately paused schedule stays paused. Windows lock or suspension pauses scheduling; return to the review page afterward. Resume preserves a still-future approved time, while a past time requires a new approval. Old interval queues migrate paused and cannot resume as batches; cancel them and review one draft.

Before clicking Submit, Arc saves a durable submit intent. A fresh check verifies account, community, exact fields, revision and timing. The browser clicks once. Success requires a matching posted page and verified Reddit permalink. If a connection fails or verification is ambiguous, the post might exist, so Arc preserves the attempt as uncertain and does not retry.

Use reconciliation after checking Reddit. If no post exists, explicitly confirm that and approve a fresh attempt separately. If it exists, supply the real permalink and confirm the account and exact text. That receipt is labeled user-confirmed, separately from automatic browser verification. Moderation delays and absent search results do not prove submission failed.

## What to ask

- “Make a post in three hours in r/MyCommunity. Title: Weekend project notes. Body: Here is what I built…”
- “Prepare one post for tomorrow at 3pm about my release. Keep the claims limited to these measured results.”
- “Show the time my Reddit post is scheduled for.”
- “Pause my Reddit post.”
- “Cancel my scheduled Reddit post.”

The model can inspect, save, read status, pause and cancel. It cannot approve, resume, publish through generic browser controls or substitute a different scheduler. A saved draft is not a published post; the final report uses actual receipts.

## Verification scope

The current source includes 120 additional [Reddit posting playbooks](EMAIL_REDDIT_TIMED_GUIDES.md), alongside 120 email and 120 timed-work guides. They cover research, writing, review, one-shot timing and recovery; they do not enable recurring posts or bypass the exact post review.

Automated tests use synthetic state, controlled clocks, temporary directories and isolated browser fixtures. They cover timing, single submission, immutable approval, stale revisions, account changes, CAPTCHA stops, restart and missed-time handling, migration, storage failures, background tab control and uncertain receipts. These tests do not publish a real Reddit post and do not establish compatibility with every live Reddit layout or community. A user-supervised live account test remains necessary for the current site.
