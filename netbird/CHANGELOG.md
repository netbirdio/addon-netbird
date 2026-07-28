# Changelog

## [v0.75.1] - 2026-07-28

### Changed
- Updated to NetBird v0.75.1

### Upstream Release Notes
## Release Notes for v0.75.1

### What's New

#### Agent Network

- Added prompt cache token and cost accounting to Agent Network usage.
  https://github.com/netbirdio/netbird/pull/6900

- Added support for Claude Opus 5.
  https://github.com/netbirdio/netbird/pull/6895

- Scoped Agent Network model allowlists per policy, group, and provider.
  https://github.com/netbirdio/netbird/pull/6905

#### Client Improvements

- Reconcile routed AllowedIPs when a lazy connection becomes idle.
  https://github.com/netbirdio/netbird/pull/6863

- Fetch FreeBSD port files from the GitHub mirror instead of cgit.
  https://github.com/netbirdio/netbird/pull/6880

- Restored the missing `backup.Reset` behavior.
  https://github.com/netbirdio/netbird/pull/6883

- Made `Test_ConnectPeers` deterministic under Docker/eBPF kernel and Darwin CI.
  https://github.com/netbirdio/netbird/pull/6884

- Export agent version information for iOS.
  https://github.com/netbirdio/netbird/pull/6918

- Use platform-specific installer URLs for manual update downloads.
  https://github.com/netbirdio/netbird/pull/6922

- Exit the GUI immediately when the Windows session ends.
  https://github.com/netbirdio/netbird/pull/6878

- Fixed stale routing peers after removing overlapping-prefix networks.
  https://github.com/netbirdio/netbird/pull/6799

- Added `ReapplyMatching` support to the dedicated `AllowedIPsRefCounter`.
  https://github.com/netbirdio/netbird/pull/6935

#### Infrastructure & Miscellaneous

- Restored the `rootless-latest` Docker image tag.
  https://github.com/netbirdio/netbird/pull/6914

**Full Changelog**: https://github.com/netbirdio/netbird/compare/v0.75.0...v0.75.1
