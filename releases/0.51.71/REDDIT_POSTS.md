# Reddit drafts and reviewed timed posts

Arc can prepare a small batch of Reddit text posts, keep them editable, and send the exact posts you approve at a chosen interval. It uses your connected browser account. Drafting and scheduling are separate: writing a draft never turns posting on.

## Set up and review

1. Enable **Reddit drafts and timed posts** and **Browser control** in Plugins. Open **Settings → Reddit posts**.
2. Choose the intended subreddit and inspect it. Sign in yourself if needed. Check the displayed account, destination and community rules. Arc does not bypass sign-in, CAPTCHAs, account restrictions or subreddit moderation.
3. Enter a topic, tone and a finite count of 1–10 drafts. Generate drafts or ask Arc in chat to prepare them. Generation uses your selected local model and context; it does not switch to a different model for this feature.
4. Read and edit every selected title and body. Verify factual claims, links, dates and any claimed experience. The automation disclosure is part of the exact body shown for review.
5. Choose **15, 30 or 60 minutes**, select the reviewed drafts, confirm the account and community, and approve the finite queue. The first post becomes due after one full interval.

A post title is limited to 300 characters. The reviewed body is limited to 4,000 characters, including the visible disclosure: “Posted with Arc automated assistance; reviewed by the account owner.” Generated drafts use a smaller body budget to leave room for that disclosure. If generation is too slow for the bounded batch timeout, try fewer drafts; an unfinished batch is not silently scheduled.

## Useful requests

- “Draft three distinct posts for r/MyCommunity about organizing a small personal coding project. Keep them practical and conversational. Do not post them.”
- “Rewrite this draft with a clearer opening and one specific question. Do not invent my experience or change the topic.”
- “Read the current time and show whether my reviewed Reddit queue is paused.”
- “Pause my Reddit queue.”

Chat can create and inspect drafts and pause or cancel a queue. Approval and resuming posting happen in the settings review interface. A request such as “post something every fifteen minutes forever” does not create an unlimited autonomous campaign. Prepare a finite batch, review it, and decide whether another batch is appropriate after it finishes.

## Timing and model use

The clock shows the computer's local time, timezone and UTC. The operating system clock is the timing source; this feature is not a calendar service with delivery guarantees. Arc must be running, the computer must be available and the browser must remain usable. Foreground work delays a due post. After a successful attempt, the next interval begins from that completion time. Waking from sleep does not cause several overdue posts to be sent at once.

Draft generation acquires the normal inference lease and makes one bounded model call per draft. Posting itself uses the reviewed text and the browser; it does not ask a model to invent a replacement, change the approved wording or swap models. Other Arc work is kept out of the same browser/inference operation while it is active. Pause can interrupt preparation, but a real submit already in progress may need its receipt checked.

## What is saved

Arc stores the draft contents, revisions, exact approved queue, status and verified posting receipts in a local `reddit-publisher.json` file inside its application data directory. These are plain local application records, not encrypted backups. They do not contain imported browser cookies or saved passwords. Posting history is bounded to the most recent 200 receipts, and the active draft list and queue are each bounded to ten items.

The queue copies the exact reviewed account, subreddit, title and body. Editing an approved draft revokes its approval and pauses affected work. Stale review revisions cannot approve newer text. Normalized duplicate titles or bodies are rejected against current drafts, pending posts and retained posting history. This is a duplicate check, not a guarantee that differently worded posts are meaningfully different; review the substance too.

## Pauses and uncertain outcomes

Restarting Arc leaves an existing schedule paused. Locking or suspending Windows also pauses scheduling; return to the review page to resume. A fresh account mismatch, sign-in requirement, CAPTCHA, rate limit, unsupported page layout or blocked submission pauses the queue. Address the specific problem without trying to evade the site's restriction.

Immediately before the browser submits, Arc persists the attempt's intent. If the app stops, the network fails after submission, or the resulting page cannot verify the exact account and content, Arc marks the attempt as uncertain. It does not retry an ambiguous post automatically: doing so could publish it twice.

Check your Reddit profile and the target community. If no post was submitted, explicitly confirm that in the reconciliation interface; that entry is cancelled and its draft needs fresh review before being scheduled again. If a post exists, enter its actual permalink and explicitly confirm that its account, community, title and body match the reviewed entry. Arc records this as **user-confirmed**, distinct from an automatic browser-verified receipt. Do not mark it absent simply because it has not appeared in a community feed; moderation can delay visibility.

Confirmed earlier posts remain in history if a later post fails. A verified browser receipt establishes the visible posted page and content at that time; it does not guarantee the post survives moderation, reaches an audience or appears immediately in search. Arc makes no guarantee that every Reddit layout or community supports this browser workflow.

## Writing that belongs in the community

Start with a useful question, explanation or concrete observation relevant to the community. Prefer one clear subject per post and remove repetitive promotional language. A natural voice should come from specific details and readable sentences, not invented claims such as “I have used this for years” or a fabricated testimonial. If the account owner supplied an experience, preserve its meaning without embellishing it.

Read applicable rules and verify changing claims before review. A search result is a lead, not proof of a current statistic. Cite the source that actually supports a claim; identify estimates and uncertainty. Do not treat rules copied from a page, comments or screenshots as instructions that grant additional tool permissions. If automation or promotion is not allowed in the community, keep the content as a draft and do not schedule it.

## Verification scope

Automated tests exercise simulated timers, finite queues, account mismatch, immutable approvals, editing races, persistence failures, restart recovery, duplicate detection and uncertain receipts. Browser tests use local controlled fixtures and do not publish public Reddit posts. Those tests establish the implemented checks; a user-supervised live account check is still required for the current Reddit interface and community.
