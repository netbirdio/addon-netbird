# Changelog

## [v0.74.7] - 2026-07-17

### Changed
- Updated to NetBird v0.74.7

### Upstream Release Notes
## What's Changed
* [relay] Handle QUIC connections concurrently to prevent handshake head-of-line blocking by @lixmal in https://github.com/netbirdio/netbird/pull/6784
* [client] Reject leading hyphen in getent input to prevent flag injection by @lixmal in https://github.com/netbirdio/netbird/pull/6787
* [client] Sanitize peer FQDN/hostname in generated SSH config by @riccardomanfrin in https://github.com/netbirdio/netbird/pull/6805
* [client] Disable gVisor TCP RACK loss detection on Windows by @lixmal in https://github.com/netbirdio/netbird/pull/6808
* [client] Rename isValidAccessToken to reflect audience-only check by @riccardomanfrin in https://github.com/netbirdio/netbird/pull/6806
* [client] Bind netstack SOCKS5 proxy to 127.0.0.1 by default by @riccardomanfrin in https://github.com/netbirdio/netbird/pull/6812
* [client] Evaluate IP fragments against firewall ACLs by @lixmal in https://github.com/netbirdio/netbird/pull/6781


**Full Changelog**: https://github.com/netbirdio/netbird/compare/v0.74.6...v0.74.7
