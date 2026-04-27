# Changelog

## [v0.70.0] - 2026-04-27

### Changed
- Updated to NetBird v0.70.0

### Upstream Release Notes
## Release Notes for v0.70.0

### What's New
#### Client signatures
We've updated our Windows and MacOS installers and binary signatures. This means your users might be prompted again, but we expect minimum impact for most organizations. 

#### Client Improvements
- Suppressed **ICE signaling**.  
  https://github.com/netbirdio/netbird/pull/5820
- Prefer **systemd-resolved stub over file mode** regardless of resolv.conf header.  
  https://github.com/netbirdio/netbird/pull/5935
- Trusted **wg interface in firewalld to bypass owner-flagged chains**.  
  https://github.com/netbirdio/netbird/pull/5928
- Added **TTL-based refresh to management DNS cache via handler chain**.  
  https://github.com/netbirdio/netbird/pull/5945
- Increased **gRPC health check timeout to 5s**.  
  https://github.com/netbirdio/netbird/pull/5961
- Improved test stability and reliability:  
  https://github.com/netbirdio/netbird/pull/5953  
  https://github.com/netbirdio/netbird/pull/5951  
  https://github.com/netbirdio/netbird/pull/5950  

#### Management Improvements
- Replaced **mailru/easyjson with netbirdio/easyjson fork**.  
  https://github.com/netbirdio/netbird/pull/5938
- Checked **policy changes before database updates**.  
  https://github.com/netbirdio/netbird/pull/5405
- Propagated **context changes to upstream middleware**.  
  https://github.com/netbirdio/netbird/pull/5956
- Added **changeable PAT rate limiting**.  
  https://github.com/netbirdio/netbird/pull/5946
- Excluded **already expired peers from expiration job**.  
  https://github.com/netbirdio/netbird/pull/5970
- Unified **peer-update test timeout via constant**.  
  https://github.com/netbirdio/netbird/pull/5952

#### Proxy Enhancements
- Set **session cookie path to root**.  
  https://github.com/netbirdio/netbird/pull/5915

#### Self-Hosted Improvements
- Added **reverse proxy retention fields to combined YAML**.  
  https://github.com/netbirdio/netbird/pull/5930
- Used **cscli lapi status for CrowdSec readiness check in installer**.  
  https://github.com/netbirdio/netbird/pull/5949

#### Infrastructure & Misc
- Updated **sign pipeline version**.  
  https://github.com/netbirdio/netbird/pull/5981
- Updated **release pipeline version**.  
  https://github.com/netbirdio/netbird/pull/5995

### New Contributors
- @alsruf36 made their first contribution in https://github.com/netbirdio/netbird/pull/5915

**Full Changelog**: https://github.com/netbirdio/netbird/compare/v0.69.0...v0.70.0
