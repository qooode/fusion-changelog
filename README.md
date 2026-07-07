# Fusion — What's New

Builds 31 to 147 · 30 March to 7 July 2026

A summary of what changed across this development cycle: new capabilities, improvements, fixes, and a few things that were removed or reverted.

## Playback and Video

- DV. Profile 5 / 7 / 8.x plays with correct colors, with native passthrough on DV displays.
- HDR10 and HDR10+ metadata. static mastering-display and content-light-level metadata, plus dynamic per-frame HDR10+ metadata, are preserved and passed through, so HDR content renders with correct brightness and tone-mapping.
- Press-and-hold fast-seek on Apple TV, with variable glide speeds.
- Stream self-healing. transient errors (5xx and network hiccups) silently reload at your exact position and re-apply your audio, subtitle and speed choices instead of throwing a fatal error, with a manual retry button as a fallback.
- Up Next. an autoplay-next-episode countdown with next-episode pre-fetch, including handoff to external players.
- Video display modes. cycle Fit / Fill / Zoom to control how the picture fills your screen, per title.
- Picture in Picture on macOS, with a fullscreen toggle (keyboard shortcut and double-click), a volume slider and mute.
- Live TV and IPTV support. adaptive and extension-less streams bypass the internal bridge and hand off to FFmpeg, and live streams auto-start.
- Playback diagnostics. an optional, off-by-default capture of detailed stream and session info to help troubleshoot playback problems.
- tvOS scrubbing overhaul. holding the remote accelerates continuously, with a real-time ghost playhead preview and a customizable commit interval.
- Controls behaviour. on Apple TV, controls stay visible while paused and only auto-hide during playback, never while a menu is open.
- High-bitrate 4K stability. per-device-tier buffer and cache profiles with backpressure tuning remove micro-stutters without out-of-memory kills on lower-end Apple TVs.
- Network resilience. smarter end-of-stream detection separates a real stream-end from a dropped connection, with silent retries and longer recovery windows to stop false Playback Unavailable alerts.
- Match-Content switching. HDR and DV display-mode switches now apply promptly mid-playback instead of only after backgrounding the app, with a clean flush and re-prime to remove judder.
- Frame timing. presentation timestamps are snapped to ideal frame boundaries with refined vsync nudging, for smoother playback on refresh-matched displays, especially MKV.
- Fixed the fast-forward runaway loop where a recovery could send the player racing through the whole title; the stall watchdog now refuses to chase a runaway clock and clamps to the last good position.
- Fixed inline episode switching black-screening with audio only, and a freeze at the wrong position after a failed seek.
- Fixed stutter on quick stop-then-replay and on media switches, by fully resetting renderer state per session.
- Fixed a broad class of crashes, freezes, deadlocks and memory leaks across the pipeline.
- Fixed stuck loading spinners after playback actually starts, and a landscape lock stuck when the player is dismissed early on iOS.

## Audio

- Dynamic surround output. multichannel is enabled based on your connected receiver's capabilities and re-requested after audio-route changes, so surround survives switching outputs.
- Wider codec support. FLAC and Opus added to the audio-track fallback list.
- Fixed audio gaps on resume and reduced underrun stutter, with safer audio-track switching.

## Subtitles

- Live subtitle translation. translate subtitles on the fly with up to 32 concurrent workers and a 60-second look-ahead, so translated lines appear with almost no lag.
- Right-to-left rendering. Arabic and Hebrew subtitles render with correct bidirectional layout and punctuation placement, including ASS and SSA.
- Non-Latin font fallback. subtitles in non-Latin scripts automatically fall back to a suitable system font instead of showing missing glyphs.
- Per-media subtitle delay. an offset that persists across sessions, plus forced-track support and automatic character-encoding detection.
- Disable subtitles in Dub mode, so dubbed-anime watchers don't get mismatched subbed-source subtitles.
- Subtitle-language options. a No Preference option and a Translated option that auto-picks the best subtitle for your target language, and skips translating when a matching track already exists.
- Best-match selection rewritten to prefer full tracks over forced ones by language, with consistently sorted online picker lists.
- Fixed a memory kill from translation flooding, by only pre-translating cues within about 12 seconds, capping in-flight requests, and cancelling on stop.
- Fixed external ASS files being wiped by the internal flush, with a clean subtitle reset between episodes.

## Add-ons and Sources

- Search across installed Stremio addon catalogs alongside TMDB, with results streaming in as they load.
- Custom source names. assign your own display names to source providers, shown consistently across the app.
- Addon-native content IDs. custom or private catalogs can fetch metadata using their own IDs without needing a TMDB or IMDb mapping.
- Genre filtering for Stremio addon catalogs, to narrow catalog content in the grid.
- Source filtering UI. a content-filter and group-selection UI on the sources list with regex-based filtering and per-field show/hide toggles on source cards.
- Addon-supplied logos. title-treatment logos from add-ons are used as a fallback across home, detail and widget views when TMDB has none.
- More external players. Infuse, SenPlayer, VidHub, CineUltra and Moon Player as handoff targets, with the picker only listing players you actually have installed.
- Self-healing add-ons. a manifest that failed to load retries automatically on demand, throttled and honoring Retry-After and 429 rate limits.
- Resilient fetching. a circuit breaker and negative cache for failing or slow add-ons, so metadata recovers quickly and stops hammering broken servers.
- Lenient manifest parsing. off-spec manifests and a single malformed stream entry no longer drop valid sources.
- Provider resolution by exact URL rather than manifest ID, fixing conflicts between add-ons that share an ID.
- Catalog pagination aligned to Stremio's 100-item spec, so large catalogs load fully.
- Combined skip markers. intro and credits markers from different community submissions are merged for more complete skip coverage.

## Sync, Accounts and Continue Watching

- Cross-device sync via PublicMetaDB (PMDB). a watch-progress backend with its own settings screen for connection status and sync frequency, keeping Continue Watching consistent across devices.
- Local watchlist. an on-device watchlist (default provider local) that syncs over iCloud.
- Watchlist import and export to a JSON file, with import merging non-destructively and only updating newer entries.
- Two-way resume with external players. Fusion passes your position out and applies the position returned when you come back, for example with Infuse.
- Local watched history. mark and unmark watched, integrated into Continue Watching, with auto-advance to the next episode.
- Hide items at the show level, so hidden shows aren't resurrected by a later sync.
- Detail-screen tracker status. PMDB and movie watched-status controls now appear in the detail screen alongside other trackers, and pick the right one based on your active backend.
- Anime tracking rebuilt. multi-source resolution (AniList, Trakt, PMDB, then local) with TVDB and TMDB coordinate translation, so Continue Watching shows the right season and episode, including split seasons, specials and franchise groupings, and it is reliable on a cold launch.
- Continue Watching intelligence. optimistic instant progress saves with background reconcile, recency-aware merges so stale data never overwrites a recent watch, and a daily auto-advance from a season finale to the next-season premiere even without activity.
- Continue Watching integrity. in-progress entries are preserved on mark and unmark, stale placeholders and impossible entries are cleaned up, and completed items no longer show a progress bar.
- Multi-profile isolation. per-profile caches, secrets and API keys, with iCloud account changes auto-detected, so switching profiles never needs a restart.
- Trakt reliability. full pagination of large watch histories, transient token-refresh failures no longer sign you out, and re-auth is handled reactively on request failure.
- AniList compatibility. kept working through stricter new API limits, with a lower request rate, a required User-Agent header, and temporary outages no longer forcing a disconnect.
- iCloud sync hardening. timestamped setting pushes, sticky consent handling, and loading guards to prevent freezes during sync.
- Upcoming accuracy. Trakt look-ahead extended to 90 days, next-season premieres only appear once you finish the current season, and upcoming anime is pulled directly from AniList.
- Scrobbling is on by default the moment you connect PMDB or Trakt.
- Finished movies and episodes now reliably leave Continue Watching and are marked watched on Trakt, while ongoing and anime series correctly stay.
- A transient empty server response no longer wipes your Continue Watching row, and next episodes no longer wrongly resume at 80 percent.
- Progress and watch data migrated to UserDefaults on tvOS, to survive system storage purges.
- Fixed tvOS WebDAV servers disappearing after relaunch; they now sync over iCloud and work behind reverse proxies.

## Interface, Home and Catalog

- Upcoming row. tracks upcoming episodes with countdowns, with its own settings screen for enable, days ahead and item limit.
- TODAY badge on Continue Watching for episodes that aired today, with a one-time recency bump.
- Collection Preview Packs. collections can show custom trailers, background art and logos, with an audio toggle and a fade-through-black transition.
- Custom content filters and tags. build your own filters to organize results, with customizable tag colors (including alpha hex) and dynamic borders, synced via iCloud.
- Shuffle button plays a random episode.
- AI-powered search. vague or misspelled queries are resolved into real titles.
- Haptic feedback. tactile response across around 70 touchpoints, including player controls, card swipes, tab switches, watchlist toggles, account connections and destructive confirmations.
- Expandable synopsis. a two-line preview that opens the full scrollable text on detail pages, with tvOS parity including an episode synopsis sheet, age ratings and a selectable cast header.
- Watchlist context menus throughout search, home, grids, detail and discover, for quick add and remove.
- Ranked row widget. a numbered widget with rank badges, plus combined Movies-and-Series widgets and vote-average and vote-count query filters.
- More quick actions. optional home-hero cast display, plus Go To Detail, Browse Sources, and Skip details on Continue Watching.
- Scroll and focus restoration. grids return to your position and focused item instead of resetting to the top.
- Calmer detail loading. a solid black backdrop with fade-in artwork and content, and deferred loading, with leading fade overlays that keep hero text readable over bright art.
- Live-refreshing home. hero and rows update when watchlist items or data sources change, and the hero no longer flickers between items during Continue Watching updates.
- Diverse hero carousel. a round-robin mix across all your data sources instead of only the first.
- Continue Watching cold launch streams cards in incrementally with accurate loading indicators, so there are no misleading empty states.
- iOS search field auto-focuses on appear, and long-press lifts a clean still-and-title preview card.
- Fixed fully-watched and ended shows lingering, while ongoing and anime series stay, and unaired episodes never appear as next.
- Fixed anime episode labels and numbering across Continue Watching, Upcoming and detail views.
- Fixed swipe-back on a pushed detail view dismissing the full-screen player.
- Fixed editing a home widget's data source not saving; it now refreshes live with stale data cleared.
- Fixed home-row expansion showing permanent spinning placeholders or losing the See All tile.

## Settings

- New settings screens for PMDB, Filters, Upcoming, and local progress and watchlist, tailored for tvOS.
- Auto cache mode. a bitrate-aware, disk-backed sliding window that caches around five minutes ahead and two back, rests the Wi-Fi radio when full, and throttles when the device heats up, now the default on fresh installs.
- AI model selection for search and translation.
- External-player picker lists only installed players, with per-provider display names and a logo-display toggle.
- More subtitle and audio languages, including Haitian Creole, and an SDH auto-select toggle.
- Tag size stays device-local for ergonomic sizing, while tag appearance still syncs via iCloud.

## Performance and Stability

- Fixed recurring out-of-memory crashes on Apple TV, by unifying memory-pressure detectors, cutting the pinned seek-back cache from around 500 MB to 128 MB, and adding an independent reclaim timer.
- Fixed mid-playback video freezes caused by hardware decoder-pool exhaustion, using frame-pool and backpressure limits.
- Thermal and battery gains. adaptive read throttling, dynamic sleep intervals, less tvOS busy-looping, and resting the radio when the cache is full, for cooler long sessions.
- Fixed overheating and crashes in crop and zoom mode, by removing an off-screen compositing buffer.
- Eliminated a one-to-two-second freeze on wake, by moving around 20,000 anime-mapping entries off the main thread.
- Smoother scrolling from memoized grid results, lighter cells, per-session row caching, and background-thread backdrop decoding.
- Faster PMDB lists, by batch-resolving items into real cards before display, with disk-cached ID maps that persist across restarts.
- Fixed trailer memory leaks on Home, and trailers now stop before a new one starts.
- Sanitized duplicate or empty filter and group IDs to prevent tvOS crashes.

## Removed, Changed and Reverted

- The default player no longer silently switches to Infuse.
- Mark Unwatched was removed from the Continue Watching context menu.
- The context menu on media detail was removed for a cleaner two-column layout, with more room for Play and secondary actions.
- Trakt Continue Watching no longer double-syncs through iCloud, since Trakt's own server now handles cross-device progress, which fixes resurrected deleted items.
- A dynamic filter-tag feature that resolved names from regex capture groups was added and then removed.
- Several intermediate Dolby Vision routing rules were tried and then reverted once better root causes were found, leaving the current stable HDR and Dolby Vision routing.
- Future-air-date caching for aired-today detection was replaced by a simpler local-calendar comparison.
