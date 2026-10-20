# YouTube audio in Arc

Open **Settings → Music · YouTube**, or **Settings → Plugins → YouTube → Audio player → Open Music settings**. Music also appears in quick navigation search.

## Listen inside Arc

1. Install Agent Reach from Plugins if it is not installed. Music uses its managed yt-dlp runtime; it does not load, unload or reserve an AI model.
2. Type **play Get Lucky by Daft Punk** or **play an 80s hits playlist** in chat, or say **Hey Arc** followed by that command. With Hey Arc enabled, a confident “Hey Arc, play…” utterance can carry the command directly into Voice. You can also use **Find and play inside Arc** in Music settings, or paste a public HTTPS YouTube link. Playlist links load the first 100 entries. Audio is resolved only for the selected track.
3. Use the floating audio player for Play/Pause, Previous, Next, seeking and volume. Collapse it to a small bar if needed. Closing Settings, changing chats and minimizing Arc keep the same player alive.
4. The next track starts when the current track ends. Playback stops at the end of the queue. An inaccessible track stops with an explanation, instead of silently skipping every failure. Retry or choose another track.
5. Stop removes the audio source and cancels the resolver/stream. Quitting Arc stops playback. The active queue is temporary and does not auto-play after startup.

This is experimental public-stream playback, **not an official YouTube embedded player or a licensed YouTube service**. Availability can change. Private, sign-in-only, live, protected and geographically unavailable streams are not bypassed. Tracks need a known duration of up to two hours and an accessible direct audio-only format. Arc does not copy cookies, import passwords, subscribe to Premium or save music files. In-app search needs no browser window or YouTube API key.

## Voice and text commands

From Home or any chat, a direct music request opens **Music · YouTube**, fills in the search, and starts the first match. It uses the same CPU/network player as the Music controls, not the chat model. Typed music receipts do not start text-to-speech. An already loaded model stays loaded for your other work; no extra model is loaded for music. The Arc interface may still use normal GPU compositing.

- **search for the song Take On Me on YouTube**, **find the song Get Lucky by Daft Punk**, or **search YouTube for Get Lucky and play it** searches and plays.
- **search YouTube for an 80s hits playlist** or **find a playlist of jazz** searches public playlists and starts the first match.
- **search YouTube Music for [title or artist]** explicitly selects music. General video requests such as “search YouTube for PHP tutorials” remain research.
- **play this song** without a title opens Music and asks for a name locally, without guessing or running inference. Existing playback continues until you choose a track.
- A music search received through Hey Arc opens the panel inside Arc without restoring or focusing a minimized/hidden native window. Pause, Skip and voice duck/restore do not interrupt your current panel.

- **play [song] by [artist]** searches YouTube and plays the first result. Up to five search matches form the queue; Skip moves to the next match, not a personalized recommendation.
- **play [name] playlist** first checks exact names in your saved Arc playlists. Otherwise it searches public YouTube playlists and loads the first match, up to 100 tracks. Add a creator or a more specific name if the first match is not what you intended.
- **pause music**, **resume music**, **skip this song**, **previous song**, and **stop music** control the current player. Repeating Pause or Resume does not toggle the opposite state.
- These direct single-action commands bypass research and text-model inference. They work in Instant or Ultra without changing the selected model. Ordinary questions, code, attachments and quoted examples continue through normal chat.
- Playback confirmation comes from the actual audio element. Finding a URL is insufficient to claim “Playing.” Commands leave a normal chat receipt; voice uses the existing optional Save to chat preference.
- Enable Hey Arc and microphone access for hands-free commands. If one utterance is not recognized, say **Hey Arc**, wait for the greeting, then give the command. The microphone and local speech engine are still needed for voice recognition; text music commands require neither.

## Playlist library and browser fallback

Save, rename, filter and remove up to 100 named playlist links. The existing `youtube-music.json` library remains compatible. Saving never starts playback; **Play in Arc** starts the public audio queue, and **Open playlist** explicitly opens the official browser player. The latter can use that browser's existing signed-in account. It reports only that the browser launch was requested, not that playback started.

Removing a shortcut does not edit YouTube's playlist or stop an already playing queue. Corrupt/oversized library records are preserved and reported, not overwritten. Tracking and unrelated query parameters are discarded.

## Voice and troubleshooting

Voice's existing media-pause/resume and duck/restore actions apply to Arc's own music as well as other Windows media. Ducking temporarily lowers the chosen volume. Pause/resume restores only playback that voice paused; an explicit music action overrides that temporary pause. If the stream has expired while paused, retry the track to resolve a fresh source.

If a public link fails, check connectivity and the Agent Reach installation, then retry or try another track. The official browser player remains available for account-only content. The player displays real HTML audio events; loading is not labeled as successful playback. Automated checks can verify decoding and advancing timestamps without establishing that speakers are audible.

Arc completes each <=1 MiB upstream segment in RAM before giving its bytes to the player. Incomplete segments, connection resets, timeouts and temporary server errors get at most two retries at the same byte position. Failed partial segments are discarded before decoding. Access denial is reported without those retries. Stop or changing the track cancels pending work. The latest exhausted transport failure is stored in `diagnostics/latest-music-error.json` with a timestamp and safe error category/status only; it contains no signed media URL, song title or account data. No GPU model is involved.

## Implementation and boundaries

- One persistent React audio element owns playback across settings navigation. No hidden video player is used.
- The main process validates YouTube IDs, resolves a selected queue member through fixed worker actions, and returns a random same-origin stream capability. Signed upstream locations remain in main-process memory.
- The renderer retains its existing content security policy. The `arc` protocol supports streaming and serves only the current capability; Stop or a new selection revokes the old capability.
- Audio transport accepts only public HTTPS Googlevideo hosts, validates every redirect, pins public DNS for the connection, verifies TLS, and forwards no browser cookies or authorization headers. Bounded byte-range requests support seeking and limit buffering; only audio MIME types and responses within 256 MiB are accepted.
- The transient player makes no disk music library and acquires no inference/GPU lease. Existing video research and Watch with Arc remain separate.
- Music imports a bundled SHA256-verified yt-dlp 2026.08.19 wheel through the managed Python environment. It does not replace the research runtime's installed yt-dlp. The newer resolver fixes the later-range access failure observed with 2026.07.04.

Spotify and iHeartRadio playback are not implemented. See the 0.51.83 release notes for the published installer and checks.

References checked 2026-10-19: [yt-dlp format selection](https://github.com/yt-dlp/yt-dlp#format-selection), [Electron streaming protocols](https://www.electronjs.org/docs/latest/api/protocol), [YouTube official audio mode](https://support.google.com/youtubemusic/answer/6313574), [YouTube API policies](https://developers.google.com/youtube/terms/developer-policies).
