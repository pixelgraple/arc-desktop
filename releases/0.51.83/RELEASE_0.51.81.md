# Arc 0.51.81 — Music by voice and text

- Ask Arc to play a song or named public YouTube playlist without pasting a link. The same command works in chat and Hey Arc.
- Pause, resume, skip, go to the previous track, and stop through direct commands. Controls use actual playback acknowledgments and do not ask the model to invent a plan.
- Search in Music settings now plays inside Arc. Exact saved playlist names take priority; public playlist matches load up to 100 entries. Song searches queue up to five matches.
- Keep the persistent audio player running while working, changing chats, closing Settings or minimizing Arc.
- Explicit Pause stays paused when Voice finishes. Arc now includes its own player when deciding whether a conversation paused media.
- Preserve commands spoken immediately after the wake phrase. Native recognition checks for additional speech after its keyword, and the CPU transcription worker gets a longer first-start allowance. Unrelated ambient transcripts are not forwarded or saved.
- Keep existing pasted links, saved playlists, browser fallback, model selections and speech privacy preferences.

Public YouTube stream access remains experimental. Some tracks may be inaccessible; Arc does not bypass sign-in, DRM, geography or live-stream restrictions. Audio transport and its pinned resolver remain unchanged. Automated decoding checks are performed with speakers muted; microphone recognition is tested with synthetic Windows speech and local CPU transcription.

Included in the cumulative public 0.51.83 release.
