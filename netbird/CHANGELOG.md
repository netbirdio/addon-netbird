# Changelog

## [v0.65.3] - 2026-02-19

### Changed
- Updated to NetBird v0.65.3

### Upstream Release Notes
## Release Notes for v0.65.3

🛡️ Security Fix: Race Condition in Role Update Validation





**What was affected**

A race condition in the user role validation logic could allow permission checks to succeed based on stale role data. Under very specific timing conditions, concurrent requests during a role change (e.g., while an admin was being demoted to user) could bypass role validation when changing another users role.

**Exploit Potential**

If an administrator account was being demoted while simultaneously performing acocunt ownership transfer actions, a race window existed where the system could treat the user as having elevated permissions to change owners.

In a coordinated scenario involving two administrator accounts, this could potentially allow privilege escalation — for example, promoting a user to Owner during the demotion window.

**Conditions Required**

Exploitation required:

- Two administrator accounts.
- One administrator being actively demoted.
- Concurrent ownership transfer requests executed precisely during the demotion process.
- Precise timing to trigger the race condition.

This issue required intentional coordination and timing, making it unlikely to occur accidentally and will require access to two admin accounts.

### What's New
#### Client & Mobile Improvements
- Batched **macOS DNS domains** to avoid truncation issues.  
  [#5368](https://github.com/netbirdio/netbird/pull/5368)
- Ensured **route settlement on iOS** before handling DNS responses.  
  [#5360](https://github.com/netbirdio/netbird/pull/5360)
- Added logging of **lock acquisition time** in message handling for improved observability.  
  [#5393](https://github.com/netbirdio/netbird/pull/5393)

#### Relay Improvements
- Reduced **QUIC initial packet size to 1280 bytes** (IPv6 minimum MTU) for better compatibility.  
  [#5374](https://github.com/netbirdio/netbird/pull/5374)

#### Management Improvements
- Fixed possible **race condition on user role change**.  
  [#5395](https://github.com/netbirdio/netbird/pull/5395)
- Added **docker login step** in management tests.  
  [#5323](https://github.com/netbirdio/netbird/pull/5323)

#### Self-Hosted Updates
- Added a **migration script** for upgrading from pre-v0.65.0 to post-v0.65.0 combined setup.  
  [#5350](https://github.com/netbirdio/netbird/pull/5350)
- Removed unused **configuration example** from self-hosted setup.  
  [#5383](https://github.com/netbirdio/netbird/pull/5383)

#### Miscellaneous
- Updated **timestamp format to include milliseconds**.  
  [#5387](https://github.com/netbirdio/netbird/pull/5387)

**Full Changelog**: [v0.65.2...v0.65.3](https://github.com/netbirdio/netbird/compare/v0.65.2...v0.65.3)
