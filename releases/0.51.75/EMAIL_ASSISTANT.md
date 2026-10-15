# Email assistant

Open **Settings → Email assistant**, or its entry in **Settings → Plugins**. Arc uses the browser selected in Browser settings and an accessible signed-in Gmail, Outlook or Proton Mail tab. It does not import cookies or passwords. This is a browser UI adapter, not an email API integration.

## Before the first reply

1. Open the intended inbox in your connected normal browser and select the provider. Inspect the mailbox so Arc can show the observed account. If the account address is hidden, open the provider's account menu yourself and inspect again.
2. Choose the email types you want considered: friends, school, work, bills, finances, updates, promotions, spam or other. All start unchecked. Promotions/spam are optional classifications of messages in the selected inbox; selecting them does not open the Spam folder.
3. Give each selected type instructions and optional examples. Add a general tone, exact allowed/excluded senders or `@domains`, and a delay of 30–3,600 seconds between messages (default 60).
4. Review the full account, rules, filters and pace. Check the approval box, then choose **Start replying** to authorize one inbox sweep under those rules. It is not a recurring subscription. Changes require fresh review; chat cannot approve its own sending.

Example rules:

| Type | Example rule |
| --- | --- |
| Friends | Write warmly in two to four sentences. Ask me if the answer needs my availability; do not invent plans. |
| School | Acknowledge routine notices. Ask for a missing deadline. Hold anything needing consent or personal records. |
| Bills | Acknowledge receipt or request an invoice copy. Never promise payment, accept terms or share account details. |
| Work | Give a concise response using only facts supplied in this questionnaire and the current message. Hold unknown project status. |
| Promotions | Politely decline selected offers; do not purchase, click links or unsubscribe automatically. |

Each email is read separately. Arc classifies it, applies the approved rules, drafts a bounded reply using the selected local text model, and verifies the recipient and filled body before one send attempt. The next message receives a fresh model request containing the rules and that message—not previous senders' mail, chat history or personal memory. The model remains subject to its normal context limit; oversized messages are held rather than silently clipped. “Fresh context” does not mean the model is unloaded between emails.

The run pauses around competing Arc work. Pause and Stop are available on the page. Disabling Email assistant or either browser-control plugin pauses the run immediately; re-enabling a plugin does not resume it. A restarted app stays paused and requires review to resume. Arc must stay open and the PC awake. Background tab selection uses Arc's existing personal-browser connection; initial connection/login may need your attention.

## Delivery and duplicate protection

Before clicking Send, Arc atomically saves a submit-intent record on local disk. It requires matching sent-message evidence before increasing the confirmed-sent count. A timeout, crash or ambiguous result is held as uncertain and is never automatically resent. Inspect the real thread or Sent folder yourself. Do not delete the cache to force another attempt.

Stop prevents further work; it cannot recall a Send command already dispatched to the browser. If that command's result is lost, Arc preserves the uncertain delivery record instead of trying again.

If the saved inbox position no longer matches the visible messages, resuming cannot repair that position. Choose **Stop this pass**, check the intended inbox, then review and choose **Start replying** for a new pass. The new scan retains confirmed and uncertain delivery records; it does not erase duplicate protection.

`email-assistant.json` in Arc's user-data directory stores the questionnaire/account, traversal references, compact outcomes and hashed delivery identities. It does not store a second archive of email bodies or generated replies. This local file is not advertised as encrypted. The ledger spans runs and keys messages by provider, account and stable inbound message ID. At its bounded capacity, work stops without silently deleting older duplicate protection.

The pass ends when the supported current inbox view reaches its observed end. It reports confirmed, skipped, held and uncertain outcomes separately. A traversal limit is a pause, not proof the inbox is empty. This does not continuously poll for future mail. Start another reviewed pass later; retained delivery records prevent re-sending to already handled messages. Held or skipped messages may be reconsidered under a newly approved run; confirmed or potentially sent messages remain protected across runs.

## Scope and compatibility

- Provider compatibility is in preview. Initial adapters target fixed **English** visible-DOM layouts for Gmail, Outlook web and Proton Mail, with complete paginated item counts and verified message controls. Provider layouts are not a stable API. Outlook's virtualized or infinite-scrolling inbox views are not supported; a provider appearing in the selector does not establish that its current layout will work. Unsupported account menus, editors, message identifiers or pagination stop with a reason.
- The scope is the current supported **Inbox view/category**, not every folder, attachment or message in the account. Opening an email may mark it read. Collapsed or unreadable message bodies are not guessed.
- Replies target the one original sender. Reply-all, forwarding, new recipients, attachments, purchases, payments and account/security changes are outside this feature. Existing drafts are preserved.
- Login, CAPTCHA, rate limits, changed account/recipient/thread or uncertain sends require attention. Arc does not bypass them.
- Incoming email is untrusted evidence. The local generator has no tools. Code checks and model instructions hold ambiguous/high-risk requests; classification and semantic judgement can still be imperfect. Narrow rules and sender filters reduce mistakes.

Automated tests use synthetic mailboxes and intercepted browser fixtures. They do not prove compatibility with every live provider layout or a successful delivery through a real account. No real email is sent by the test suite. A supervised trial on the user's specific mailbox is still needed before relying on unattended use.

Provider behavior references: [Gmail reply versus reply all](https://support.google.com/mail/answer/6585?co=GENIE.Platform%3DDesktop&hl=en), [Outlook web create and reply](https://support.microsoft.com/en-us/outlook/create-reply-to-or-forward-email-messages-in-outlook-on-the-web), [Proton conversation view](https://proton.me/support/disable-conversation-view). These explain provider concepts; they do not guarantee Arc's DOM selectors remain compatible.

## Guides and chat controls

The four original guides cover questionnaire setup, privacy/duplicate recovery, timed Reddit posting and drafting a verified Reddit post. The current source adds 120 email, 120 Reddit posting and 120 timed-work guides with concrete examples and recovery steps. Browse the [complete guide library](EMAIL_REDDIT_TIMED_GUIDES.md), or search Guides in Settings > Plugins. They are instructions for existing tools, not extra permissions or executable plugins. Email remains one approved inbox pass; a calendar reminder cannot start it automatically.

Try “Help me set up email replies for friends and school notices,” “Pause my email assistant,” or “Show my email assistant status.” Chat uses the narrow `email_assistant` status/pause/cancel tool. Configuration and approval remain on the questionnaire page. Reddit continues to use its separate exact one-post review in Settings → Reddit posts.
