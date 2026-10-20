# Arc 0.51.80 — In-app audio player

Public YouTube video and playlist links can now play audio directly inside Arc. A persistent floating player provides Play/Pause, Previous/Next, seeking, volume and Stop, with a collapsible layout. Music continues when Settings closes, conversations change or Arc is minimized. Tracks advance automatically, and the queue stops at its end.

The Music section shows the current queue (up to 100 entries), accepts single-video links, and adds **Play in Arc** to saved playlists. Existing playlist shortcuts and the explicit official-browser fallback remain. Voice's existing pause and volume-duck actions also control Arc's own player. No AI model or inference lease is required for music playback.

Playback uses the existing managed Agent Reach/yt-dlp runtime and public audio-only streams. This is experimental, not an official or licensed YouTube integration. Private, sign-in-only, live and protected streams are unavailable. The app does not copy browser sessions or save music files. Inaccessible tracks show an error with Retry/Skip rather than looping through failures.

The host keeps signed media locations private, uses revocable local stream capabilities, validates public DNS and redirects, and cancels pending work on Stop/Skip. Bounded byte ranges address an observed YouTube failure with open-ended ranges. Existing model, research, playlist library and browser features remain.

Music includes its own checksum-verified yt-dlp 2026.08.19 wheel. A live check found that the older research extractor could return a stream that started but rejected later ranges; the newer resolver passed playback and seeking beyond that opening data. The shared research environment is preserved.

See [Music instructions](YOUTUBE_MUSIC.md) for setup, limits and the distinction between in-app audio and the official browser player.
