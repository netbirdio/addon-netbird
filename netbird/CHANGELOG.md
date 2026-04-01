# Changelog

## [v0.67.2] - 2026-04-01

### Changed
- Updated to NetBird v0.67.2

### Upstream Release Notes
## Release Notes for v0.67.2

### What's New
#### Client Improvements
- Added **Expose support to embed library**.  
  https://github.com/netbirdio/netbird/pull/5695
- Persisted **service install parameters across reinstalls**.  
  https://github.com/netbirdio/netbird/pull/5732
- Fixed **Exit Node submenu separator accumulation on Windows**.  
  https://github.com/netbirdio/netbird/pull/5691
- Fixed **Android DNS routes lost after TUN rebuild**.  
  https://github.com/netbirdio/netbird/pull/5739
- Fixed **flaky TestUpdateOldManagementURL in CI**.  
  https://github.com/netbirdio/netbird/pull/5703
- Fixed **path join issue in Windows tests**.  
  https://github.com/netbirdio/netbird/pull/5762
- Fixed **IPv6 address handling in QUIC server**.  
  https://github.com/netbirdio/netbird/pull/5763
- Refactored **Android PeerInfo to use ConnStatus enum**.  
  https://github.com/netbirdio/netbird/pull/5644
- Added support for **embed.Client on Android with netstack mode**.  
  https://github.com/netbirdio/netbird/pull/5623

#### Management Improvements
- Added **notification endpoints**.  
  https://github.com/netbirdio/netbird/pull/5590
- Added **terminated field to services**.  
  https://github.com/netbirdio/netbird/pull/5700
- Extended **blackbox tests**.  
  https://github.com/netbirdio/netbird/pull/5699
- Updated to latest **gRPC version**.  
  https://github.com/netbirdio/netbird/pull/5716
- Prevented **events for temporary peers**.  
  https://github.com/netbirdio/netbird/pull/5719
- Persisted **proxy capabilities to database**.  
  https://github.com/netbirdio/netbird/pull/5720
- Added **FleetDM API spec support**.  
  https://github.com/netbirdio/netbird/pull/5597
- Added **target user account validation**.  
  https://github.com/netbirdio/netbird/pull/5741
- Improved **permission validation for posture check delete**.  
  https://github.com/netbirdio/netbird/pull/5742
- Removed **client secret from gRPC auth flow**.  
  https://github.com/netbirdio/netbird/pull/5751
- Fixed **panic on management reboot**.  
  https://github.com/netbirdio/netbird/pull/5759
- Added **legacy to embedded IdP migration tool**.  
  https://github.com/netbirdio/netbird/pull/5586
- Fixed **race condition in setup flow allowing multiple owners**.  
  https://github.com/netbirdio/netbird/pull/5754

#### Proxy Enhancements
- Added **pprof support** for proxy debugging.  
  https://github.com/netbirdio/netbird/pull/5764

#### Security & Stability
- Added **path traversal and file size protections**.  
  https://github.com/netbirdio/netbird/pull/5755

#### Self-Hosted Improvements
- Added **self-hosted scaling note**.  
  https://github.com/netbirdio/netbird/pull/5769

#### Miscellaneous
- Added **missing OpenAPI definitions**.  
  https://github.com/netbirdio/netbird/pull/5690
- Updated **Contributor License Agreement document**.  
  https://github.com/netbirdio/netbird/pull/5131
- Set **permissions on env file for getting started scripts**.  
  https://github.com/netbirdio/netbird/pull/5761

### New Contributors
- @tobsec made their first contribution in https://github.com/netbirdio/netbird/pull/5691
- @iakshayubale made their first contribution in https://github.com/netbirdio/netbird/pull/5644

**Full Changelog**: https://github.com/netbirdio/netbird/compare/v0.67.1...v0.67.2
