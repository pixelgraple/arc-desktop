# Arc 0.51.73 — One approved Reddit post, now or later

Arc can prepare a single Reddit text post and submit it once at a reviewed date and time. Requests such as “make this post in three hours” save a concrete time proposal beside the draft. In **Settings → Reddit posts**, review the full title and body, account, community, timezone and time, then approve in advance. **Post once now** is also available.

## Changes

- Manual title/body entry alongside one-at-a-time drafting with the selected local model.
- One active post, replacing interval batches. Older batch queues are preserved paused for cancel/review; they cannot resume as batches.
- An application timer reads the system clock without keeping a model generating. Valid future schedules survive restart; missed times require fresh approval. Windows lock or suspend pauses work. Arc must remain open and awake.
- Relative delays resolve once when saved. Exact local date/time review handles timezone offsets, daylight-saving gaps and repeated local times. Edits invalidate review; stale draft revisions cannot authorize changed text.
- The connected personal browser is searched for an accessible Reddit tab. Dedicated background selection and creation avoid foreground activation. An unfinished editor is preserved. Initial browser launch, connection permission or sign-in can still require the user.
- Private windows are usable only when exposed by the authorized browser connection. No cookie/password copying or silent profile fallback.
- Reddit editor checks recognize ordinary paragraph and blank-line structure while verifying exact submitted text. User-written mentions of CAPTCHA do not masquerade as a site challenge; actual visible verification controls still stop automation.
- Fresh account, community, content and timing checks precede one durable submit boundary. Lost or ambiguous receipts require reconciliation rather than an automatic retry.
- Updated Plugins explanation and a substantial task guide describe proposal, review, background execution, timing limits and recovery.

## Limits and verification

There is no CAPTCHA bypass, recurring posting or multi-post queue. The feature supports text posts in recognized editors, not every possible Reddit layout or community requirement. An existing connected window stays in the background; the operating system or browser may display first-launch and permission UI.

Tests use synthetic clocks/state and controlled local browser fixtures. They exercise advance approval, exactly one submission, cancelled or changed approvals, timezone handling, restart/missed schedules, wrong accounts, real challenge detection, editor readback, background tab behavior, duplicate prevention and uncertain receipts. Desktop and mobile review layouts are checked without a live account. The packaged application verifies the actual saved timer/approval/receipt service offline.

No real Reddit post was published by these tests. Live account and community compatibility remain to be tested under user supervision. A verified receipt establishes the matching visible post at that time, not permanent acceptance by moderators.

See [setup, behavior and limits](REDDIT_POSTS.md).
