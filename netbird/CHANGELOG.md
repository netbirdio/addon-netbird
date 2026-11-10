# Changelog

## [v0.82.1] - 2026-11-10

### Changed
- Updated to NetBird v0.82.1

### Upstream Release Notes
## What's Changed
* [misc] Don't move latest for a patch on an older release line (backport #8215, #8466 to 0.82) by @bison in https://github.com/netbirdio/netbird/pull/8467
* [client] Refuse SSH JWT auth when the client changes the user name between auth requests on one connection (#8389) by @lixmal in https://github.com/netbirdio/netbird/pull/8482
* [client] Start ConPty SSH shells with bInheritHandles=FALSE so they no longer inherit the daemon's os/exec pipes of other users' sessions (#8391) by @lixmal in https://github.com/netbirdio/netbird/pull/8483
* [client] Close SSH connections still in the handshake on Stop and reject ones accepted by a stopped server (#8392) by @lixmal in https://github.com/netbirdio/netbird/pull/8484
* [client] Treat 0.0.0.0 and :: as local in the userspace firewall (#8432) by @lixmal in https://github.com/netbirdio/netbird/pull/8485
* [client] Close established SSH connections whose JWT user is no longer authorized when UpdateSSHAuth applies a new authorization list (#8393) by @lixmal in https://github.com/netbirdio/netbird/pull/8486
* [client] Start the SSH server's SFTP subprocess through su like session commands, so nologin and expired accounts are refused (#8394) by @lixmal in https://github.com/netbirdio/netbird/pull/8487
* [client] Remove a peer from the interface when it leaves the network map while its lazy connection is idle (#8449) by @lixmal in https://github.com/netbirdio/netbird/pull/8488
* [client] Default the SSH server to off for stored configs without a peer key (#8448) by @lixmal in https://github.com/netbirdio/netbird/pull/8489
* [client] Drop forwarded traffic that would leave through the interface it arrived on (#8440) by @lixmal in https://github.com/netbirdio/netbird/pull/8490
* [client] Match peer rule protocols behind IPv6 extension headers in nftables (#8451) by @lixmal in https://github.com/netbirdio/netbird/pull/8491
* [management] Enforce account id on proxy update (#8419), backport to 0.82 by @dmitri-netbird in https://github.com/netbirdio/netbird/pull/8519
* [proxy] Renew cached ACME certificates and report expiry (#8524) by @jnfrati in https://github.com/netbirdio/netbird/pull/8531
* [client] Use a destination Windows refuses in the UDP dial error test (#8480) by @lixmal in https://github.com/netbirdio/netbird/pull/8494


**Full Changelog**: https://github.com/netbirdio/netbird/compare/v0.82.0...v0.82.1
