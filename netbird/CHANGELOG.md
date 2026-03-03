# Changelog

## [v0.66.1] - 2026-03-03

### Changed
- Updated to NetBird v0.66.1

### Upstream Release Notes
## Release Notes for v0.66.1

### What's New
#### Client Improvements
- Fixed **server mutex being held across `waitForUp` in `Up()`**, preventing potential blocking behavior.  
  [#5460](https://github.com/netbirdio/netbird/pull/5460)
- Fixed **close-of-closed-channel panic** in `ConnectClient` retry loop.  
  [#5470](https://github.com/netbirdio/netbird/pull/5470)
- Fixed **deadlock in route peer status watcher**.  
  [#5489](https://github.com/netbirdio/netbird/pull/5489)
- Fixed **profile config directory permissions**.  
  [#5457](https://github.com/netbirdio/netbird/pull/5457)
- Lowered socket auto-discovery log level from **Info to Debug**.  
  [#5463](https://github.com/netbirdio/netbird/pull/5463)

#### Management Improvements
- Prevented deletion of **groups linked to flow groups**.  
  [#5439](https://github.com/netbirdio/netbird/pull/5439)
- Fixed **user update permission validation**.  
  [#5441](https://github.com/netbirdio/netbird/pull/5441)
- Added **reverse proxy services REST client**.  
  [#5454](https://github.com/netbirdio/netbird/pull/5454)
- Added explicit **target deletion on service removal**.  
  [#5420](https://github.com/netbirdio/netbird/pull/5420)

#### Proxy Improvements
- Flushed buffer immediately to improve **gRPC support**.  
  [#5469](https://github.com/netbirdio/netbird/pull/5469)

#### Self-Hosted Enhancements
- Added support for **Embedded IdP PostgreSQL database**.  
  [#5443](https://github.com/netbirdio/netbird/pull/5443)
- Allowed specifying **SQL file locations** for auth, activity, and main stores.  
  [#5487](https://github.com/netbirdio/netbird/pull/5487)

#### Security
- Upgraded **Alpine Linux** from 3.23.2 to 3.23.3.  
  [#5217](https://github.com/netbirdio/netbird/pull/5217)

### New Contributors
- @artivis made their first contribution in [#5457](https://github.com/netbirdio/netbird/pull/5457)

**Full Changelog**: [v0.66.0...v0.66.1](https://github.com/netbirdio/netbird/compare/v0.66.0...v0.66.1)
