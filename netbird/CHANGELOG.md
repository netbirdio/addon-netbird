# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

**Note:** Dates reflect when the upstream NetBird version was released, not when
it was integrated into this add-on. This aligns entries with the upstream release timeline.

## [v0.64.2] - 2026-01-27

### Changed
- Updated to NetBird v0.64.2

### Upstream Release Notes
## Release Notes for v0.64.2

### What's New
#### Client Improvements
- Consolidated **authentication logic** to improve maintainability and consistency.  
  [#5010](https://github.com/netbirdio/netbird/pull/5010)
- Added **IPv6 support** to the UDP WireGuard proxy.  
  [#5169](https://github.com/netbirdio/netbird/pull/5169)
- Fixed a **flaky JWT SSH test** to improve CI stability.  
  [#5181](https://github.com/netbirdio/netbird/pull/5181)
- Updated **Fyne UI** and added **retry handling** to the exit menu.  
  [#5187](https://github.com/netbirdio/netbird/pull/5187)
- Prevented **eBPF traffic** from being tracked in conntrack.  
  [#5166](https://github.com/netbirdio/netbird/pull/5166)
- Added support for **non-PTY, no-command interactive SSH sessions**.  
  [#5093](https://github.com/netbirdio/netbird/pull/5093)

#### Management & Identity
- Fixed **validator warning messages** to improve clarity.  
  [#5168](https://github.com/netbirdio/netbird/pull/5168)
- Improved **peer deletion error handling**.  
  [#5188](https://github.com/netbirdio/netbird/pull/5188)
- Included **default groups claim** in the CLI audience.  
  [#5186](https://github.com/netbirdio/netbird/pull/5186)
- Added **user invite link** support for the embedded IdP.  
  [#5157](https://github.com/netbirdio/netbird/pull/5157)

**Full Changelog**: [v0.64.1...v0.64.2](https://github.com/netbirdio/netbird/compare/v0.64.1...v0.64.2)

## [v0.60.2] - 2025-11-17

### Changed
- Updated to NetBird v0.60.2
- **BREAKING**: Removed support for armhf, armv7, and i386 architectures
- Only aarch64 and amd64 architectures are now supported

## [v0.54.2] - 2025-11-17

### Changed
- Updated to NetBird v0.54.2
- Improved security by masking sensitive setup key in logs
- Enhanced error handling in service startup script
- Better documentation for DNS resolver workaround

### Fixed
- Fixed inconsistent addon slug in documentation
- Fixed broken repository links
- Updated maintenance year to 2025
- Corrected default management URL across documentation

### Added
- AppArmor security profile
- Healthcheck configuration for monitoring addon status
- Improved file migration logic with better error handling
- Binary existence check before execution

### Security
- Removed sensitive credential logging
- Added AppArmor profile for enhanced security

## [Unreleased]

### Notes
- Based on hassio-addons base image 19.0.0
- Supports aarch64 and amd64 architectures only
- Requires privileged capabilities for VPN functionality
