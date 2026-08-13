# Changelog

## [v0.77.0] - 2026-08-13

### Changed
- Updated to NetBird v0.77.0

### Upstream Release Notes
## Release Notes for v0.77.0

### What's New

#### Agent Network

- Reworked Agent Network endpoint identity and settings bootstrap.
  https://github.com/netbirdio/netbird/pull/7085

- Added a proxy-connect authorizer seam for Agent Network.
  https://github.com/netbirdio/netbird/pull/7136

- Added reverse proxy usage accounting for activity tracking.
  https://github.com/netbirdio/netbird/pull/7116

#### Client Improvements

- Added strict anonymization level and MAC address anonymization to debug bundles.
  https://github.com/netbirdio/netbird/pull/7102

- Declared the `xdg-utils` dependency for NetBird UI packages.
  https://github.com/netbirdio/netbird/pull/7126

- Fixed credentials used for GTK3 package uploads.
  https://github.com/netbirdio/netbird/pull/7125

- Prevented WireGuard packets from being misrouted to the STUN handler.
  https://github.com/netbirdio/netbird/pull/7059

- Updated the NetBird Wails fork to remove the native WebView2 dependency.
  https://github.com/netbirdio/netbird/pull/7128

- Migrated the relay QUIC tracer to qlog and upgraded `quic-go` to v0.59.1.
  https://github.com/netbirdio/netbird/pull/7124

- Derived Windows SSH privilege checks from the user token and group membership.
  https://github.com/netbirdio/netbird/pull/6966

- Adjusted the GTK3 release job.
  https://github.com/netbirdio/netbird/pull/7163

- Fixed a macOS DNS panic caused by malformed `scutil` output.
  https://github.com/netbirdio/netbird/pull/7180

- Added a fallback to per-IP ACL rules when `ipset` is unavailable.
  https://github.com/netbirdio/netbird/pull/6332

- Gated IPv6 forwarding on overlay IPv6 while preserving host Router Advertisement acceptance.
  https://github.com/netbirdio/netbird/pull/6221

- Removed installer registry handlers for Windows autostart Run keys.
  https://github.com/netbirdio/netbird/pull/7183

#### Infrastructure & Documentation

- Added Crowdin configuration for UI translation synchronization.
  https://github.com/netbirdio/netbird/pull/7155

- Fixed Crowdin export paths and export options.
  https://github.com/netbirdio/netbird/pull/7162

- Updated the documentation to direct translation contributions to Crowdin.
  https://github.com/netbirdio/netbird/pull/7161

- Allowed external test suites to reuse the NetBird end-to-end test harness.
  https://github.com/netbirdio/netbird/pull/7176

- Improved the release pipeline to build release branches and delay marking releases as "latest" until signing is complete.
  https://github.com/netbirdio/netbird/pull/7171

**Full Changelog**: https://github.com/netbirdio/netbird/compare/v0.76.3...v0.77.0
