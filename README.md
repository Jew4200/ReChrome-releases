# ReChrome — Releases

Download the latest build from the [Releases](../../releases) page.

## What this repo is

Release downloads only. The source is private; this repo exists so ReChrome's
in-app updater can check for new versions **without a GitHub account and
without any token**.

## Verifying a download

Every release ships two files:

- `ReChrome.exe` — the portable browser, no install needed
- `ReChrome.exe.sha256` — its SHA-256 digest

ReChrome verifies the digest automatically when it downloads an update, and
deletes the file if it does not match. To check by hand:

```powershell
Get-FileHash .\ReChrome.exe -Algorithm SHA256
```

Compare that against the contents of `ReChrome.exe.sha256`.

## Privacy

The update check is a single anonymous HTTPS GET to GitHub's public releases
API. It sends no token, no account, no machine or install identifier, and no
telemetry — only a generic `User-Agent: ReChrome`, identical for every copy.
Nothing runs unless you press **Settings → Check for updates**.

Your browsing data lives in the `VeilData` folder next to the exe and is never
transmitted anywhere.
