# Changelog

## [v0.68.0] - 2026-04-08

### Changed
- Updated to NetBird v0.68.0

### Upstream Release Notes
## What's Changed
* [proxy] Update package-lock.json by @heisbrot in https://github.com/netbirdio/netbird/pull/5661
* [client] Unexport GetServerPublicKey, add HealthCheck method by @pappz in https://github.com/netbirdio/netbird/pull/5735
* [client] Fix mgmProber interface to match unexported GetServerPublicKey by @pappz in https://github.com/netbirdio/netbird/pull/5815
* [management] validate permissions on groups read with name by @pascal-fischer in https://github.com/netbirdio/netbird/pull/5749
* [management] Fix missing service columns in pgx account loader by @lixmal in https://github.com/netbirdio/netbird/pull/5816
* [client] Error out on netbird expose when block inbound is enabled by @lixmal in https://github.com/netbirdio/netbird/pull/5818
* [client] Skip down interfaces in network address collection for posture checks by @lixmal in https://github.com/netbirdio/netbird/pull/5768
* [client] Fix SSH server Stop() deadlock with active sessions by @lixmal in https://github.com/netbirdio/netbird/pull/5717
* [client] Add TCP DNS support for local listener by @lixmal in https://github.com/netbirdio/netbird/pull/5758
* [client] Fix iOS DNS upstream routing for deselected exit nodes by @mlsmaycon in https://github.com/netbirdio/netbird/pull/5803
* [client] Add NAT-PMP/UPnP support by @lixmal in https://github.com/netbirdio/netbird/pull/5202
* [relay] Replace net.Conn with context-aware Conn interface by @pappz in https://github.com/netbirdio/netbird/pull/5770
* [client] Fix SSH proxy mangling shell quoting in forwarded commands by @lixmal in https://github.com/netbirdio/netbird/pull/5669
* [client] Don't abort UI debug bundle when up/down fails by @lixmal in https://github.com/netbirdio/netbird/pull/5780


**Full Changelog**: https://github.com/netbirdio/netbird/compare/v0.67.4...v0.68.0
