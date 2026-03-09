# Changelog

## [v0.66.3] - 2026-03-09

### Changed
- Updated to NetBird v0.66.3

### Upstream Release Notes
## Release Notes for v0.66.3

### What's New
#### Client Improvements
- Fixed **"reset connection" error after wake from sleep** to improve reconnection stability.  
  https://github.com/netbirdio/netbird/pull/5522
- Fixed **exit node menu not refreshing on Windows**.  
  https://github.com/netbirdio/netbird/pull/5553

#### Management Improvements
- Added **per-target options to the reverse proxy** (management + proxy).  
  https://github.com/netbirdio/netbird/pull/5501
- Avoided breaking **single account mode when switching domains**.  
  https://github.com/netbirdio/netbird/pull/5511
- Added **stable domain resolution for the combined server**.  
  https://github.com/netbirdio/netbird/pull/5515
- Fixed **domain uniqueness validation**.  
  https://github.com/netbirdio/netbird/pull/5529
- Added **activity events for domain operations**.  
  https://github.com/netbirdio/netbird/pull/5548
- Switched proxy registration to use **real IP address**.  
  https://github.com/netbirdio/netbird/pull/5525
- Cached **PKCE state** to improve login flow reliability.  
  https://github.com/netbirdio/netbird/pull/5516
- Count **login request duration metrics only for successful logins**.  
  https://github.com/netbirdio/netbird/pull/5545
- Aggregated **gRPC metrics by account ID**.  
  https://github.com/netbirdio/netbird/pull/5486

#### Proxy Improvements
- Refactored **proxy metrics** and added **usage logs**.  
  https://github.com/netbirdio/netbird/pull/5533

#### CI & Misc
- Added **PR title validation workflow**.  
  https://github.com/netbirdio/netbird/pull/5503

**Full Changelog**: https://github.com/netbirdio/netbird/compare/v0.66.2...v0.66.3
