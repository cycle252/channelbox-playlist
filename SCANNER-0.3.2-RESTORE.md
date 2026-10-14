# ChannelBox Scanner 0.3.2 backup

This archive contains the source installed on Home Assistant on October 14, 2026:
`scanner.py`, `channelbox_feed.py`, `Dockerfile`, `config.yaml`, `numbers.json`,
and the scanner README. The GitHub token in the configuration template is blank.
It contains no private settings, saved runtime state, or stream playlist.

## Changes in this version

- Fixes the HLS probe incompatibility affecting Grit Xtra and similarly shaped URLs.
- Grit still must decode successfully; it is retried daily even when inactive.
- Adds a one-time manual full scan: increase `full_scan_request` by one in the
  app Configuration, save, and restart. Request 0 is the default. A completed
  request does not repeat on restart; failed uploads retry without rescanning.
- Preserves the existing channel registry and normal daily/weekly scheduling.
- 53 unit tests passed. The prior 0.3.1 probe passed Grit on Home Assistant;
  0.3.2's first manual full scan was started there on October 14.

## Verify the download

Download `channelbox-scanner-0.3.2.tar.gz` and
`channelbox-scanner-0.3.2.sha256` into the same directory, then run:

```sh
sha256sum -c channelbox-scanner-0.3.2.sha256
```

On macOS, use `shasum -a 256 -c channelbox-scanner-0.3.2.sha256` instead.

Expected SHA-256:
`cda2aca9ac8fe783589ecbcc85a031b0eee2db1f2b5395999b26af0532db1c36`

## Restore the source on Home Assistant OS

The archive has files at its root, so extract it **inside** the app directory.
From the Terminal & SSH app, after placing the archive in `/tmp`:

```sh
mkdir -p /local_apps/channelbox_scanner
tar -xzf /tmp/channelbox-scanner-0.3.2.tar.gz -C /local_apps/channelbox_scanner
ha store reload
```

If replacing existing source, first save a copy of that directory and make a
Home Assistant backup including ChannelBox Scanner. Install or update the local
ChannelBox Scanner app through Home Assistant. If the installed version is
already 0.3.2, rebuilding rather than updating may be needed to refresh its image.
Configure the GitHub repository and token through Home Assistant, then start the
app and verify its logs. Never commit the token to this repository.

Defaults: four workers; daily scan at 4 AM America/Chicago; weekly full scan.
Local Arkansas/Mid-South candidates are retried daily, with only successfully
decoded streams admitted. Existing channel numbers are preserved.

## Source backup versus full recovery

This package is a **source backup**, not a full Home Assistant app-data backup.
The app's persistent data contains its evolving number registry, recent scan
history and configured token. Restore that data from a private Home Assistant
backup to retain those exactly. The included `numbers.json` is the original
1,479-entry seed; it does not contain all numbers assigned by later scans.
Do not initialize a fresh scanner over the live hosted feed without recovering
its current registry: a fresh seed could otherwise assign later numbers
differently. Keep the existing app data when updating/rebuilding.

The separately published `channels.json` remains the current player feed. The
archive is versioned and does not change when daily channel scans run.
