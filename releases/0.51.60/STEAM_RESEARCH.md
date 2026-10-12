# Steam game research and SteamDB references

In **Settings → Plugins → Steam game research · SteamDB links**, enable **Find Steam games** and **Steam game stats · SteamDB links**. Both ship as built-in read-only research tools. Existing off switches remain off across updates. The example button starts a normal chat request; opening the section makes no network request.

Examples:

- Find Stardew Valley on Steam. Show its current players, US price, review totals and source links.
- Compare the current Steam player counts and review totals for Terraria and Stardew Valley.
- What are the global achievement percentages for this Steam game?
- Get the latest Steam news entries for Counter-Strike 2.
- Give me the SteamDB charts and price-history links for Elden Ring.

## Sources and limits

SteamDB explicitly disallows automated scraping and does not provide a public API: https://steamdb.info/faq/. Arc resolves games using Steam's public store search, reads statistics from Steam, and returns app-specific SteamDB reference links without fetching them. These are not SteamDB-provided statistics. Historical peaks, historical prices/lows, sales/owners estimates, private user statistics and depot/build history are not provided. Supplied screenshots/excerpts can still be discussed through Arc's existing image/text tools.

`search_steam_games` resolves a title to matching app IDs. The model must distinguish games, sequels, demos and similarly named editions rather than choosing an arbitrary first match. `steam_game_stats` accepts one app ID and metric: overview, players, price, details, reviews, achievements, news or history. Overview requests only three endpoints (details, current players, review totals); other metrics request one or zero. Compare games using the same country and comparable observation times.

- **Players:** Steam's concurrent connected players, not owners, sales, or daily active users. An unavailable count is never returned as zero.
- **Prices:** country defaults to US and is included alongside currency, formatted price and original minor-unit amounts. No price does not mean free. Prices may exclude bundles and differ by region/account. Store endpoints are public but not a stable versioned API contract.
- **Details:** Steam app metadata, short description, platforms, developers, publishers and store release date. A renamed app can retain an older store date; do not assume it is the launch date of the current edition.
- **Reviews:** the current documented `IUserReviewsService/GetAppReviews/v1` API, all languages and purchase types, off-topic filtering enabled. Totals and positive percentage are reviews, not sales. Steam can cache anonymous responses for ten minutes.
- **Achievements:** at most 20 public achievement percentages, most common first; internal achievement IDs can appear instead of display names. No personal Steam account is accessed.
- **News:** up to five bounded Steam news-feed excerpts with dates and links; entries can be third-party articles and are not necessarily official patch notes.
- **History:** reference links only, explicitly unverified, without an automated SteamDB request.

Every successful metric retains its own URL, retrieval time and cache flag. Failed metrics remain separately unavailable, preserving successful results. Search and stat sources appear in Arc's normal source cards. Retrieved names and content are untrusted data, not instructions.

No account, credentials, browser connection, API key, optional runtime or model download is needed. Fixed Steam endpoints use Arc's existing DNS-pinned public-web transport, bounded responses, same-origin redirects, cancellation and timeouts. A maximum 64-entry in-memory cache lasts five minutes; timestamps are not refreshed on cache hits. HTTP 429 triggers a cooldown. No polling, scheduled crawl, game installation, purchasing or private-account operations are implemented.

References checked October 9, 2026:

- https://steamdb.info/faq/
- https://partner.steamgames.com/doc/webapi/ISteamUserStats
- https://partner.steamgames.com/doc/webapi/IUserReviewsService

## Validation

`node --test tests/steam-research.test.cjs` covers data semantics, unavailable/partial responses, cache timestamps and regions, input/response bounds, rate limiting, cancellation, plugin migrations, chat routing/source cards, offline/disabled tools, and final synthesis. `python tests/steam_protocol_test.py` checks the actual native schemas and calls through the private model-worker protocol, including rejection of invalid and disabled calls. Live checks cover Stardew Valley, Counter-Strike 2 and Terraria, plus achievements, news and UK pricing. These checks do not claim SteamDB automation, private-account access or historical data coverage.
