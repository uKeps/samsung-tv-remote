# Samsung TV Remote — Roadmap

## Now
<!-- In progress. At most 3. Each names its branch/PR when it has one. -->
- [ ] N1 — Pre-fill the video link input when a Stremio torrent stream is active on `:11470` (`tv` `stremio_stream()`, `page.html`, commit 4b8c8a2, 2026-10-31) — next step: try with Stremio open
- [ ] N2 — Mirror back on the physical monitor HDMI-A-1 (`gpu-screen-recorder`): the headless output crashed Quickshell (NVIDIA allocation failures, 2026-11-01 18:30). Sound follows what HDMI-A-1 shows, normalized; auto-stop after `MIRROR_IDLE` (`tv` `mirror()`/`route_audio()`, 2026-11-01) — simulated routing test passes; next step: send a window from the remote and switch workspaces on HDMI-A-1

## Next
<!-- Ordered; the top item is what starts when Now frees up. At most 10. -->
1. Confirm app launch on the real TV — why: `ed.apps.launch` (Apps tab) was built from Home Assistant's format but never tried on the UN50KU6000; the list itself works (30 apps, 2026-10-17).

## Debt
<!-- Known problems we are living with. Never deleted silently: fixed, or moved to Done. -->
| ID | Problem | Risk if ignored | Fix idea |
|----|---------|-----------------|----------|
| D1 | App list is cached until the service restarts (`tv` `apps()`) | Apps installed on the TV later don't show up until `systemctl --user restart tv-remote` | Refresh button on the Apps tab, or drop the cache after N minutes |
| D2 | Picking a non-default audio track remuxes live to MPEG-TS, so seeking is off (`tv` `MediaHandler.pipe`) | Can't scrub when watching with another audio track | Remux to a temp file and serve it with Range support |
| D3 | Volume slider goes to 100, the TV's full scale | A stray tap near the end of the bar blasts the TV | Cap the slider `max` (e.g. 60) if it happens in practice |
| D5 | TV sound is routed per process, not per window (`tv` `route_audio()`) | Every window of the same browser (Zen) plays on the TV, not just the one sent | Match the stream's `media.name` (tab title) to the window title; fails for embedded players, so only if it bothers |

## Decisions pending
<!-- Questions only a human can settle. Each states the options and a recommendation. -->

## Ideas
<!-- Suggestions not yet committed to. Cheap to add, cheap to drop. -->
- `tv app <name>` in the CLI — value: open Prime/HBO/Globoplay from the terminal like `tv netflix`
- UPnP event subscription (SUBSCRIBE) for the now-playing bar — value: instant updates instead of 1s polling; only if polling feels slow
- Home Assistant `rest_command` pointing at port 8765 — value: HA automations can drive this remote without porting it; only once HA runs at home
- Token auth on port 8002 (WSS) — value: needed only if a 2018+ Samsung TV replaces the UN50KU6000

## Done (recent)
<!-- Last ~10 items, newest first, each with its PR/commit. Older ones: delete; git has them. -->
- 2026-11-23 — Magnet links play on the TV through Stremio's streaming server (`tv` `magnet_url()`), started headless from the Flatpak or `/opt/stremio` when the app is closed; biggest video in the torrent wins. Checked with the Sintel torrent: cold start 1.6 s, stream answers on the LAN IP, `/tracks` probes it; not yet cast to the real TV
- 2026-11-23 — Public on GitHub with a fresh one-commit history (the old history had a personal email and the TV's LAN IP/MAC; it stays in the private repo `samsung-tv-remote-private`). Shorter README, clear error when no TV is found, new screenshots. Checked from a fresh clone with no config, no Hyprland and no `~/Downloads`: page, status and file list work, CLI errors are readable (was O1)
- 2026-11-22 — Ready to go public: MIT license; settings from env or `~/.config/tv-remote.env` instead of hardcoded IP/MAC (the TV is found via SSDP with no config); CLI, server and page in English; README with screenshots, config, optional Hyprland deps and a security note; web remote redesigned: on a phone the remote fills the screen and Apps, Media and Trackpad are drawers from the header (drag down to close); Media is one list (a field that plays a pasted link or filters the videos, the PC window as the first row); on a PC, Media and Apps stay open on the right and Trackpad is a dialog. Checked against a mock server at 360/390 px and 1280 px, no console errors or sideways scroll (branch `chore/public-release`) — next step: try it on the real TV
- 2026-11-22 — ±5 s and ±30 s jumps live in the now-playing card under the progress bar (quick taps are summed and sent once, so 3× +5 = +15; Shift+J/L = 5 s); `/seek` takes any whole jump up to 10 min. Phone: now playing sits on top, iPhone notch/home-bar safe areas, bigger seek bar, a Stremio file index ("0") shows as "Vídeo na TV" (PR #1, branch `feat/seek-5s-mobile`; checked against a mock server at 393 px wide, no console errors) — next step: try +5 on the real TV, then commit
- 2026-11-01 — Window mirroring finds the running Hyprland session on each call, so it works after a reboot or re-login without restarting the service (was D4)
- 2026-11-01 — Send a PC window to the TV: Hyprland headless output `TV` recorded by `wf-recorder` (NVENC), only that window's app sound via a `tv_mirror` null sink, private windows hidden from the picker; confirmed on the real TV (commit 4b8c8a2)
- 2026-11-01 — Page links (Twitch and other yt-dlp sites) play on the TV via yt-dlp + live MPEG-TS remux; confirmed with Twitch on the real TV (commit 4b8c8a2)
- 2026-10-18 — Pixel art app icon, transparent favicon, Apple Touch Icon, and web app manifest support
- 2026-10-17 — PC layout (remote + panel side by side) with keyboard shortcuts; mobile overflow fixed; Enter after a mouse click sends OK instead of re-pressing the clicked button
- 2026-10-17 — Apps tab (installed apps list + launch), volume slider with real level and mute state, power button shows whether the TV is on
- 2026-10-17 — Power the TV on with Wake-on-LAN (df15a53)
