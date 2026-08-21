# Changelog

## [v0.77.1] - 2026-08-21

### Changed
- Updated to NetBird v0.77.1

### Upstream Release Notes
## Release Notes for v0.77.1

### What's New

#### Client Improvements

- Fixed session extension and SSH authentication always using the device code flow on Linux.
  https://github.com/netbirdio/netbird/pull/7187

- Added Windows DNS configuration to the debug bundle.
  https://github.com/netbirdio/netbird/pull/7196

- Added a CI check for translation key parity.
  https://github.com/netbirdio/netbird/pull/6852

- Preserved the account email on Android logout while removing it when a profile is deleted.
  https://github.com/netbirdio/netbird/pull/7200

- Ranked Windows route candidates using combined route and interface metrics.
  https://github.com/netbirdio/netbird/pull/7210

- Skipped IPv6 route tests when the default next hop is unusable.
  https://github.com/netbirdio/netbird/pull/7212

- Passed the stored email as a login hint from the UI and preserved it on logout.
  https://github.com/netbirdio/netbird/pull/7199

- Fixed inconsistencies in the CI `gomobile init` process.
  https://github.com/netbirdio/netbird/pull/7229

- Deleted Windows NRPT rules by enumerating the registry instead of relying on a rule count.
  https://github.com/netbirdio/netbird/pull/7195

- Declared multi-buffer support for the loopback XDP program.
  https://github.com/netbirdio/netbird/pull/7230

- Exposed SSH functionality on Android.
  https://github.com/netbirdio/netbird/pull/7156

- Handled Android network changes without restarting the engine.
  https://github.com/netbirdio/netbird/pull/7144

- Cleared stale installer results before starting updates.
  https://github.com/netbirdio/netbird/pull/7204

- Stopped the UI before silent Windows updates and suppressed installer reboots.
  https://github.com/netbirdio/netbird/pull/7209

- Reported network addresses on Android for posture checks.
  https://github.com/netbirdio/netbird/pull/7235

- Restarted the UI using the user's environment block after updates.
  https://github.com/netbirdio/netbird/pull/7245

- Switched client tests to `go.uber.org/mock`.
  https://github.com/netbirdio/netbird/pull/7253

- Renamed TURN-specific WireGuard proxy terminology to relayed connections.
  https://github.com/netbirdio/netbird/pull/7231

- Fixed staticcheck findings after upgrading `golangci-lint`.
  https://github.com/netbirdio/netbird/pull/7266

- Added missing anonymization and SSH privilege translations.
  https://github.com/netbirdio/netbird/pull/7269

- Added a lazy connection override and device name reporting to the WASM client.
  https://github.com/netbirdio/netbird/pull/7276

#### Management Improvements

- Documented the mutual exclusivity of `ports` and `port_ranges` in policy rules.
  https://github.com/netbirdio/netbird/pull/7158

- Refused usage limits that one-off setup keys cannot honor.
  https://github.com/netbirdio/netbird/pull/7220

- Switched management tests to `go.uber.org/mock`.
  https://github.com/netbirdio/netbird/pull/7253

- Suppressed staticcheck warnings for deprecated protobuf fields.
  https://github.com/netbirdio/netbird/pull/7261


#### Infrastructure & Miscellaneous

- Added support for non-interactive, environment-driven installations in `getting-started.sh`.
  https://github.com/netbirdio/netbird/pull/7168

- Updated the Agent Network documentation.
  https://github.com/netbirdio/netbird/pull/7020

- Prevented overriding the dashboard image in Enterprise migrations.
  https://github.com/netbirdio/netbird/pull/7206

- Skipped store migrations for PostgreSQL deployments.
  https://github.com/netbirdio/netbird/pull/7207

### New Contributors

- @SunsetDrifter made their first contribution in https://github.com/netbirdio/netbird/pull/7158

- @znel2002 made their first contribution in https://github.com/netbirdio/netbird/pull/7168

**Full Changelog**: https://github.com/netbirdio/netbird/compare/v0.77.0...v0.77.1
