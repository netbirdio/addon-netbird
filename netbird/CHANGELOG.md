# Changelog

## [v0.79.0] - 2026-09-18

### Changed
- Updated to NetBird v0.79.0

### Upstream Release Notes
## Release Notes for v0.79.0

### New Feature: Desktop Light Mode

The desktop app now gives you three appearance options: System, Light, and Dark. Follow your operating system's theme or choose the look you prefer. The new light theme covers the connection view, settings, profiles, and dialogs, with matching native window appearance on Windows, macOS, and Linux. [#7344](https://github.com/netbirdio/netbird/pull/7344) by @TechHutTV

<img width="2022" height="1542" alt="image" src="https://github.com/user-attachments/assets/2081f103-f766-4f2d-92df-b304eaaa5acb" />

This release also brings shared MDM policy enforcement to the mobile SDKs, a rootless Red Hat UBI container image, and improvements to DNS, relay connections, and reverse proxy access controls.

### What's Changed

#### Desktop Client Improvements

- Fixed a Windows tray deadlock that could freeze the app when a double-click opened a window while another window was still being created. [#7449](https://github.com/netbirdio/netbird/pull/7449) by @pappz
- Fixed the MDM settings snapshot so the UI correctly reports when remote jobs are managed by policy. [#7485](https://github.com/netbirdio/netbird/pull/7485) by @pappz

#### Client Improvements

- Added MDM policy bridges and shared enforcement for the iOS and Android SDKs, keeping managed settings and profile restrictions consistent with the desktop client. [#6435](https://github.com/netbirdio/netbird/pull/6435) by @riccardomanfrin
- Accepted MDM boolean values delivered as JSON numbers, so policies using `0` and `1` are applied correctly. [#7471](https://github.com/netbirdio/netbird/pull/7471) by @riccardomanfrin
- Compared MDM-managed URLs by their normalized endpoints, avoiding false conflicts between equivalent URLs. [#7472](https://github.com/netbirdio/netbird/pull/7472) by @riccardomanfrin
- Refreshed system information on every management sync reconnect, keeping local network addresses and posture information current after network changes. [#7409](https://github.com/netbirdio/netbird/pull/7409) by @pappz
- Used host prefixes for Android TUN addresses so local network protection does not classify the entire overlay as a local network. [#7414](https://github.com/netbirdio/netbird/pull/7414) by @pappz
- Replaced the eBPF DNS forwarder with UDP and TCP DNAT rules when the resolver cannot listen on port 53, with rollback and cleanup for incomplete redirects. [#7439](https://github.com/netbirdio/netbird/pull/7439) by @lixmal
- Fixed a relay address race that could advertise a URL and IP from different connections during a reconnect. [#7498](https://github.com/netbirdio/netbird/pull/7498) by @pappz
- Returned the context cancellation or timeout error when an SSH handshake is interrupted. [#7426](https://github.com/netbirdio/netbird/pull/7426) by @pappz
- Updated wireguard-go to `8bf8fa968f1a`, fixing keepalive buffer-pool stalls, keeping timer paths non-blocking, and making netstack interface shutdown idempotent. [#7532](https://github.com/netbirdio/netbird/pull/7532) by @pappz
- Allowed buffer-pool limits to be adjusted while a device is stalled, and bounded proxy-wide updates so one stuck client does not block the others. [#7452](https://github.com/netbirdio/netbird/pull/7452) by @riccardomanfrin

#### Management Improvements

- Restored networks using individual peers as routers in the SQLite network map when `peer_groups` is empty or null. [#7418](https://github.com/netbirdio/netbird/pull/7418) by @mlsmaycon
- Fixed SQLite network-map reads for users with null automatic groups and expanded coverage for empty and null router groups. [#7425](https://github.com/netbirdio/netbird/pull/7425) by @dmitri-netbird
- Included offline peers when scheduling login expiration and ensured expired peers are disconnected, while protecting peers that have just logged in again. [#7467](https://github.com/netbirdio/netbird/pull/7467) by @pascal-fischer
- Applied duplicate-key sync protection to user-owned peers as well as peers registered with setup keys. [#7427](https://github.com/netbirdio/netbird/pull/7427) by @pascal-fischer
- Validated that a peer exists before adding it to a group. [#7486](https://github.com/netbirdio/netbird/pull/7486) by @pascal-fischer
- Prevented other users from deleting the account owner. [#7456](https://github.com/netbirdio/netbird/pull/7456) by @pascal-fischer
- Hardened OIDC issuer validation by requiring HTTPS, rejecting credentials, query strings, and fragments in issuer URLs, refusing discovery redirects, and limiting discovery response size. [#7435](https://github.com/netbirdio/netbird/pull/7435) by @bcmmbaga
- Cleaned up resources when WebSocket-to-gRPC proxy connections close. [#7484](https://github.com/netbirdio/netbird/pull/7484) by @dmitri-netbird

#### Agent Network

- Added managed proxy provisioning endpoints and response types to the API specification. [#7433](https://github.com/netbirdio/netbird/pull/7433) by @bison
- Prevented deletion of groups referenced by Agent Network budget rules, preserving the rules' spending limits. [#7450](https://github.com/netbirdio/netbird/pull/7450) by @Tyagiquamar

#### Reverse Proxy Improvements

- Added `NB_PROXY_UPSTREAM_HTTP_VERSION` with `auto`, `1.1`, and `2` options. The default `auto` mode negotiates with HTTPS upstreams and falls back to HTTP/1.1 when an upstream's negotiated HTTP/2 connection fails at the protocol level. [#7410](https://github.com/netbirdio/netbird/pull/7410) by @lixmal
- Enforced group access both when issuing session cookies and when accepting existing sessions. [#7240](https://github.com/netbirdio/netbird/pull/7240) by @lixmal
- Required custom domain validation before creating a service or moving one to a different custom domain. [#7341](https://github.com/netbirdio/netbird/pull/7341) by @mlsmaycon
- Added a 48-hour validation window for custom domain registrations. Existing pending registrations receive a fresh window on upgrade; expired registrations are removed unless they still have services attached. [#7497](https://github.com/netbirdio/netbird/pull/7497) by @mlsmaycon
- Rejected unsupported direct-upstream IP addresses, including loopback, multicast, link-local, and IPv6 addresses with zone identifiers. [#7400](https://github.com/netbirdio/netbird/pull/7400) by @dmitri-netbird
- Validated domain names before creating certificate lock files. [#7501](https://github.com/netbirdio/netbird/pull/7501) by @pascal-fischer

#### Self-Hosting Improvements

- Added a rootless Red Hat UBI image for AMD64 and ARM64, published with the `0.79.0-rootless-ubi` tag. [#7469](https://github.com/netbirdio/netbird/pull/7469) by @jnfrati
- Supported arbitrary non-root UIDs without a passwd entry, as used by OpenShift. [#7440](https://github.com/netbirdio/netbird/pull/7440) by @jnfrati
- Added RPM dependencies, license and documentation files, a generated changelog, and an example `/etc/sysconfig/netbird` to meet Red Hat software certification packaging requirements. [#7562](https://github.com/netbirdio/netbird/pull/7562), [#7573](https://github.com/netbirdio/netbird/pull/7573) by @mlsmaycon
- Kept deployments using the embedded identity provider on a single account, with stricter configuration checks and migration handling. [#7380](https://github.com/netbirdio/netbird/pull/7380) by @bcmmbaga
- Passed the combined server's TLS configuration through to the management listener. [#7499](https://github.com/netbirdio/netbird/pull/7499) by @pascal-fischer
- Updated peer connection-IP extraction to honor configured `TrustedPeers`, with `X-Forwarded-For` taking precedence over `X-Real-IP`. The final release preserves trust in all IPv4 and IPv6 sources when `TrustedPeers` is empty; configure your reverse proxy's address or network to restrict which sources can supply forwarded-IP headers. [#7454](https://github.com/netbirdio/netbird/pull/7454), [#7589](https://github.com/netbirdio/netbird/pull/7589) by @bcmmbaga; [#7561](https://github.com/netbirdio/netbird/pull/7561), [#7577](https://github.com/netbirdio/netbird/pull/7577) by @dmitri-netbird and @bcmmbaga

#### Internal, CI, and Docs

- Added atomic `SetNX` and `GetDel` cache operations. [#7084](https://github.com/netbirdio/netbird/pull/7084) by @bcmmbaga
- Allowed binaries embedding management to extend its command tree. [#7483](https://github.com/netbirdio/netbird/pull/7483) by @bison
- Extracted peer update handling and added test coverage. [#7338](https://github.com/netbirdio/netbird/pull/7338) by @dmitri-netbird
- Replaced a hard-coded temporary directory in WebSocket adapter tests with the platform's temporary directory. [#7503](https://github.com/netbirdio/netbird/pull/7503) by @dmitri-netbird
- Switched the MinIO test image to `quay.io` after it became unavailable on Docker Hub. [#7516](https://github.com/netbirdio/netbird/pull/7516) by @Silex
- Preserved image variant suffixes in snapshot tags. [#7511](https://github.com/netbirdio/netbird/pull/7511) by @jnfrati
- Skipped the protobuf breaking-change check on branch-creation pushes, which have no previous commit to compare against. [#7411](https://github.com/netbirdio/netbird/pull/7411) by @bison

---

**Full Changelog:** [v0.78.0...v0.79.0](https://github.com/netbirdio/netbird/compare/v0.78.0...v0.79.0)


