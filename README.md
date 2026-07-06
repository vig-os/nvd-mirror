# nvd-mirror

A dedicated public mirror of the [NVD](https://nvd.nist.gov/) JSON **2.0** data
feeds, published to GitHub Pages for consumption by
[`vulnix`](https://github.com/nix-community/vulnix).

## Why

`nvd.nist.gov` is chronically throttled/unstable
([vulnix#171](https://github.com/nix-community/vulnix/issues/171)): cold vulnix
scans crawl the full NVD year-feed set for tens of minutes and fail with
`ReadTimeoutError` / `IncompleteRead`, blocking CVE gates and releases. This repo
isolates that one flaky dependency: [`.github/workflows/refresh.yml`](.github/workflows/refresh.yml)
is the only thing that talks to `nvd.nist.gov`. It downloads the feed files with
resumable retry, validates them, and deploys them to Pages. Consumers then fetch
from a stable, CDN-backed URL that never depends on NVD at scan time.

## Usage

```
vulnix --mirror https://vig-os.github.io/nvd-mirror/ --closure ./result
```

The mirror serves `nvdcve-2.0-<year>.json.gz` for a rolling 6-year window plus
`nvdcve-2.0-modified.json.gz`, refreshed every 6 hours.

## Consumers

- [`vig-os/devcontainer`](https://github.com/vig-os/devcontainer) — vulnix CVE
  gate (`vig-os/devcontainer#870`).
