# Arc 0.51.79 — YouTube Music shortcuts

Music now has its own settings section and a discoverable entry in Plugins and quick navigation. Save, rename, filter and remove up to 100 playlist shortcuts, search YouTube Music, or open a playlist without saving it. YouTube and YouTube Music links are normalized to the official music player, with tracking and redirect fields removed.

Playback opens in the Windows default browser with that browser's existing session. It can continue while Arc is working, without loading an AI model. The official player handles sign-in, subscriptions, playback and available audio-only controls. Arc reports only that it requested the browser to open, not that playback or audio-only mode has been verified.

The library uses serialized atomic writes and preserves corrupt records instead of overwriting them. Only approved HTTPS YouTube playlist destinations are launched. The current beta does not extract audio, embed hidden video, copy cookies or edit YouTube playlists. Existing research and Watch with Arc features are unchanged.

See [YouTube Music instructions](YOUTUBE_MUSIC.md) for setup and limitations. Actual account playback needs a live check in the user's signed-in player; service and UI tests do not prove subscription or regional availability.
