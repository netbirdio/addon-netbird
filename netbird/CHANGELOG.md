# Changelog

## [v0.70.5] - 2026-05-05

### Changed
- Updated to NetBird v0.70.5

### Upstream Release Notes
## Release Notes for v0.70.5

### What's New
#### Client Improvements
- Added **packet capture to debug bundle and CLI**.  
  https://github.com/netbirdio/netbird/pull/5891
- Advertised **relay server IP via signal for foreign-relay fallback dial**.  
  https://github.com/netbirdio/netbird/pull/6004
- Released **Status.mux before invoking notifier callbacks**.  
  https://github.com/netbirdio/netbird/pull/6039
- Used **ctx.Err() instead of gRPC codes.Canceled to detect shutdown**.  
  https://github.com/netbirdio/netbird/pull/6019
- Used **atomic write/rename pattern for SSH config**.  
  https://github.com/netbirdio/netbird/pull/5867
- Replaced **WG interface polling with netlink subscription on Linux**.  
  https://github.com/netbirdio/netbird/pull/5857
- Displayed **QR code for device auth login URL**.  
  https://github.com/netbirdio/netbird/pull/5415
- Bumped **go-netroute to v0.4.0 and dropped fork**.  
  https://github.com/netbirdio/netbird/pull/6062
- Used **fwmark-aware route lookup for raw socket UDP checksum source**.  
  https://github.com/netbirdio/netbird/pull/6070

#### Management Improvements
- Added **monitoring for nmap update source**.  
  https://github.com/netbirdio/netbird/pull/6036
- Enabled **PAT creation during setup**.  
  https://github.com/netbirdio/netbird/pull/6003
- Added **public IPv4/IPv6 posture checks**.  
  https://github.com/netbirdio/netbird/pull/6038
- Tracked **pending approval in peer event metadata**.  
  https://github.com/netbirdio/netbird/pull/6040
- Fixed **proxy reconnect issues**.  
  https://github.com/netbirdio/netbird/pull/6063
- Mapped **Entra OID claim as Dex user ID**.  
  https://github.com/netbirdio/netbird/pull/6067
- Fixed **flaky invite token test**.  
  https://github.com/netbirdio/netbird/pull/6077

#### Proxy Enhancements
- Consolidated **mapping updates**.  
  https://github.com/netbirdio/netbird/pull/6072

#### Miscellaneous
- Disabled **govet inline analyzer**.  
  https://github.com/netbirdio/netbird/pull/6066
- Updated **discussions and issues templates**.  
  https://github.com/netbirdio/netbird/pull/6073

### New Contributors
- @lotheac made their first contribution in https://github.com/netbirdio/netbird/pull/5867
- @alexsavio made their first contribution in https://github.com/netbirdio/netbird/pull/5857
- @typhoon1217 made their first contribution in https://github.com/netbirdio/netbird/pull/5415

**Full Changelog**: https://github.com/netbirdio/netbird/compare/v0.70.4...v0.70.5
