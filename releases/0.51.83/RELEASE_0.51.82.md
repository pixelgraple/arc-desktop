# Arc 0.51.82 — Music stream resilience

- Finish and validate each audio segment in a small CPU/RAM buffer before delivering it to the player. A paused or slow player no longer keeps that upstream response open.
- Retry transient network failures, incomplete segments and temporary server errors twice, starting at the same byte position. Partial failed segments never reach the decoder, so retries do not duplicate audio.
- Bound connection and body waits. Stop and track changes still cancel outstanding work; access-denied responses are not repeatedly requested.
- Replace the generic playback warning with network, timeout, incomplete-segment, format or HTTP details. Store only the latest sanitized transport error locally for troubleshooting, without song names, account data or signed media URLs.
- Keep song/playlist search, voice/text controls and minimized playback. Music requires no inference model or GPU allocation and creates no saved music files.

Public YouTube stream availability remains experimental. An ad blocker does not repair direct audio transport errors; uBlock Origin has not been embedded in this audio-only path.
