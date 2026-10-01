# ReChrome

A portable Windows browser built on the Chromium engine, with ad blocking that
actually handles **server-stitched Twitch ads** — the kind that are spliced into
the video stream itself, where a normal blocker has nothing to block.

**[Download the latest release →](../../releases/latest)**

One `.exe`. No installer, no admin rights, no runtime to install. It keeps its
profile in a `VeilData` folder beside itself, so it runs from a USB stick.

---

## The Twitch ad problem

Most ad blockers work by cancelling network requests. That fails on Twitch,
because the ad is not a separate request — it is cut into the same video
stream as the broadcast, segment by segment. There is no URL to block.

ReChrome reads the stream manifest and removes the ad segments before the
player sees them, then repairs the timeline so playback does not stall.

Measured on real streams, same machine, comparable windows:

| Build | Duration | Ad breaks | Ads that got through |
|---|---|---|---|
| 1.4.0 | 4h 23m | — | **120** |
| 1.5.2 | 4h 22m | 226 | **0** |
| 1.5.3 | 6h 54m | 156 | **0** |

Those numbers come from the browser's own logs over multi-hour sessions on
live channels, not from a test page.

**What it does not do:** sponsor reads inside the video, where the streamer or
creator talks about a product. That is the content itself — no blocker removes
it. YouTube, Kick and ordinary web ads are handled too, by the more conventional
request-blocking and page-cleaning layers.

---

## Everything else

**Browsing**
- Full Chromium engine (WebView2 / Edge Chromium) — H.264, HEVC, AV1, VP9,
  WebM, AVIF, plus Widevine DRM, so Netflix- and Spotify-class sites work
- Frameless Chrome-style window: tabs at the very top, drag by the strip,
  double-click to maximise
- Pinned tabs, session restore, named tab sets you can save and reopen
- Picture-in-picture, bookmarks bar with real favicons, tab search

**Privacy**
- Private windows (`Ctrl+Shift+N`) keep history, cookies and cache **in memory
  only** — nothing is written to disk, and the profile folder is deleted on
  close
- Normal and private windows run side by side in one process
- Cookie and consent banners are auto-dismissed, choosing *reject* where offered
- "Please disable your ad blocker" walls are removed and page scrolling restored
- **No telemetry** — nothing reports your browsing anywhere. The app does make a
  few ordinary service calls, all of them listed below under *What it talks to*

**Bringing your stuff across**
- Imports bookmarks and history from Chrome, Edge, Brave, Vivaldi and Opera
- Imports Chrome's Google-account bookmarks, which modern Chrome keeps in a
  separate file most importers miss
- Loads Chrome extensions from disk, including ones already installed
- Passwords and cookies are **not** imported — they are encrypted to the source
  browser and cannot be moved reliably

**Nice to have**
- Six themes plus a full colour editor
- Video grabber — downloads video from a page; fetches `yt-dlp` and `ffmpeg`
  itself on first use (~100 MB, once), since they are third-party binaries not
  bundled into the exe
- Background tabs are suspended after a few idle minutes to free memory, and
  wake instantly on click

---

## Honest limitations

- **No Chrome Web Store.** WebView2 has no store integration. Extensions load
  from an unpacked folder or a `.crx`/`.zip` you supply — most work, but there
  is no store UI and no auto-update for them.
- **Passwords and cookies do not import.** They are encrypted to the source
  browser with keys ReChrome cannot read.
- **Windows only.** It is built on WebView2.
- **In-video sponsor reads are not removed.** They are part of the video.

---

## Install

Download `ReChrome.exe` from [Releases](../../releases/latest) and run it.
Windows 11 already has the WebView2 runtime; on Windows 10 it installs with most
recent Edge versions.

### Verifying your download

Every release ships the exe and its SHA-256 digest. ReChrome checks the digest
automatically when it updates itself and deletes the file if it does not match.
To check by hand:

```powershell
(Get-FileHash .\ReChrome.exe -Algorithm SHA256).Hash
```

Compare that to the contents of `ReChrome.exe.sha256`.

---

## About this repository

This repo holds **release downloads only** — the source is private. It exists so
the in-app updater can check for new versions over a plain anonymous HTTPS
request, with no GitHub account and no token.

The update check sends no token, no account, no machine or install identifier,
and no telemetry — only a generic `User-Agent: ReChrome`, identical for every
copy. Nothing runs unless you press **Settings → Check for updates**.

Your browsing data stays in the `VeilData` folder next to the exe.

## What it talks to

No analytics, no crash reporting, no identifiers. Besides the pages you visit,
the browser makes these calls — all of them ordinary browser plumbing:

| Endpoint | Why | When |
|---|---|---|
| `api.github.com` | Update check | Only when you press **Check for updates** |
| `easylist.to` | Ad/tracker filter lists | Refreshed periodically |
| `google.com/s2/favicons` | Site icons for bookmarks | Once per site, then cached |
| `clients2.google.com` | Extension updates | For extensions you loaded |
| `virustotal.com` | Look up a download's **hash** (never the file) | Only when you click *Scan*, and only with your own API key |
| `github.com` | Fetch `yt-dlp` + `ffmpeg` | First time you use the video grabber |

None of these carry an account, a token, or a machine identifier.

## Keyboard shortcuts

| Shortcut | Action |
|---|---|
| `Ctrl+T` | New tab |
| `Ctrl+W` | Close tab |
| `Ctrl+Shift+N` | New private window |
| `Ctrl+L` | Focus address bar |
| `Ctrl+D` | Bookmark this page |
| `Ctrl+Shift+B` | Toggle bookmarks bar |
| `Ctrl+Tab` / `Ctrl+Shift+Tab` | Cycle tabs |
| `Ctrl+1`–`9` | Jump to tab N |
| `Ctrl+R` / `F5` | Reload |
