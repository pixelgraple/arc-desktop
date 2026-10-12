# Twitch alerts

Available in **Settings → Twitch alerts**, with a shortcut in **Settings → Plugins → Twitch live alerts**.

Arc can receive Twitch stream-online, stream-offline and title/category events while its window is minimized or hidden in the tray. The listener is an Electron main-process service; it does not load a local AI model, inspect the screen, play a stream or record audio. The PC must remain awake, Arc must be running, and an internet connection is required.

## One-time application setup

This build does not include a registered Twitch Client ID. No account has been connected or registered on your behalf.

1. Open [Twitch's application console](https://dev.twitch.tv/console/apps) using the Developer console button. Follow Twitch's account, email verification and two-factor requirements.
2. Register a suitable application name/category, with **Client Type: Public**. If the form requires a redirect URI, enter `http://localhost`. Arc uses device authorization, not a local redirect server.
3. Copy the public **Client ID** into Arc's application setup field and save. Do not enter a client secret, password, or access token.
4. Select **Connect Twitch**. Your normal browser opens Twitch's activation page. Confirm the displayed code and allow the `user:read:follows` permission. Arc waits for approval; cancellation or expiry stops the sign-in attempt.
5. Choose channels from your follows, or search for a streamer. Adding a channel does not turn on alerts: enable **Enable Twitch alerts** when ready.

For a distributed release, the Arc maintainer can put Arc's registered public Client ID in `electron/data/twitch.json` before packaging. This identifier is public. Never ship a client secret. Once configured in the distribution, ordinary users only need to authorize their Twitch account.

## Controls

- Up to 20 selected channels; independent on/off switches and optional case-insensitive game/category substring filters. Twitch applies its own subscription-cost limits, so the supported active number can be lower. Per-channel failures are shown; the UI does not promise every saved channel is subscribed.
- Going-live alerts default on; stream-ended and title/category alerts default off. The global Twitch switch defaults off.
- A category filter also allows a live channel moving into that category to notify you when Going live is enabled. Unknown category data does not pass a filter.
- Quiet hours use the PC's local clock and support overnight periods. Equal start/end times mute all day. Matching events remain in recent activity but create no Windows banner or speech during those hours.
- Spoken alerts use the device's speech synthesis, with no AI inference. Announcements are skipped while Arc is generating a response, playing a voice reply, or in a voice session. Available voices depend on the OS.
- Test notification clearly labels itself as a test. It does not require Twitch sign-in and ignores Twitch quiet hours. Arc's global notification master switch still applies.
- Native banners and saved inbox items use Arc's existing notification service. Privacy previews, sound, Windows notification settings and Do Not Disturb remain authoritative. A successful submission is not proof that Windows displayed a banner. Enable previews in Notification preferences to see the streamer name on desktop banners.
- Click an actual channel notification to open that channel in the default browser without bringing Arc's main window forward. The recent-activity list and watchlist also have open-channel buttons.
- Disconnect stops listening, removes local encrypted credentials and attempts remote revocation. If the network prevents revocation, Arc explains how to remove authorization in Twitch Settings → Connections. The watchlist/preferences remain for reconnecting.

## Reliability and data handling

Only Twitch HTTPS API/authentication hosts and the Twitch EventSub WSS host are used. HTTP redirects are rejected. Tokens remain in the main process and are saved with Electron OS-protected storage, never returned through renderer IPC. An unavailable secure storage provider fails closed. Device credentials are held only in memory; only the user-facing activation code reaches the UI. No secret is included in logs or visible API errors.

Token validation runs on startup and at least hourly while connected. Public-client refresh-token rotation is serialized and saved before reuse. EventSub reconnect handoff keeps the old socket until the new socket receives its welcome; unexpected disconnects use backoff and rebuild subscriptions. Lost keepalives trigger reconnects. Revoked authorization stops listening and requires reconnection.

Message IDs are deduplicated across restarts, with an additional stream-ID check for duplicate live events. Title/category changes are limited to one per channel per minute. Storage is bounded to 20 channels, 60 recent events and 1,000 message IDs. An already-live stream is shown in current status without pretending a new live event occurred. Twitch does not replay events missed while Arc was offline. Corrupt connection files are preserved and reported instead of silently replaced.

No Twitch agent tool, chat bot, moderation, automatic playback, video download, full chat archive or private creator analytics is added. Existing browser research and explicitly enabled Show Arc sharing remain available independently.

## Sources and validation

Implementation reviewed against Twitch's [device authorization flow](https://dev.twitch.tv/docs/authentication/getting-tokens-oauth/#device-code-grant-flow), [token validation](https://dev.twitch.tv/docs/authentication/validate-tokens/), [WebSocket handling](https://dev.twitch.tv/docs/eventsub/handling-websocket-events/), [event subscriptions](https://dev.twitch.tv/docs/eventsub/eventsub-subscription-types/), and [API reference](https://dev.twitch.tv/docs/api/reference/).

Tests use isolated encrypted test storage, deterministic OAuth/API responses, and controllable WebSocket/timer fixtures. They cover connection, cancellation, refresh, revocation, subscription failures, quiet hours, filters, duplicate suppression, payload validation, persistence and existing notification behavior. UI tests use the actual settings component with a fixture bridge. Live account authorization and delivery from a real subscribed channel require a registered public Client ID and user sign-in; fixture tests do not establish that end-to-end result.
