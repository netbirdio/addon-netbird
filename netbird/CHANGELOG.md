# Changelog

## [v0.84.0.2] - 2026-11-28

### Added
- New `preshared_key` option to set a WireGuard pre-shared key (PSK). The add-on refuses to start if the value is not a 44-character base64 key. Leaving the option empty clears a key saved earlier, and the log warns when that happens.

### Changed
- The logged `netbird up` command now hides the setup key and the pre-shared key.

## [v0.84.0.1] - 2026-11-28

### Fixed
- Removed the `admin_url` option. NetBird v0.84.0 dropped the `--admin-url` flag, so the add-on failed to start when the option was set. A previously saved `admin_url` value is ignored: Home Assistant logs a warning and drops it.

## [v0.84.0] - 2026-11-27

### Changed
- Updated to NetBird v0.84.0

### Upstream Release Notes
# Release Notes for v0.84.0

## ⚠️ Breaking changes: CLI cleanup

The `netbird` CLI flags and environment variable handling were reorganized. Scripts and service definitions that pass a flag to a different command, use a removed flag, or rely on an environment variable that is no longer read can fail after the upgrade.

| What changed | Impact | What to do |
| --- | --- | --- |
| `--setup-key`, `--setup-key-file`, `--preshared-key`, `--hostname` and `-m` are no longer global flags | They are accepted only by `up` and `login` | Pass them to `netbird up` or `netbird login` |
| `--anonymize-level` is no longer a global flag | It is accepted only by `status` and `debug` | Pass it to `netbird status` or `netbird debug` |
| `--service` is no longer a global flag | It is accepted only by the `service` command | Pass it to `netbird service` |
| `--config` is removed from `login`, `up` and the SSH commands | It is accepted only by `run`, `install` and `reconfigure` | Set the config path when you install or run the service |
| Daemon options (for example the socket settings) are no longer exposed on the service-management subcommands | They are accepted only by `run`, `install` and `reconfigure` | Pass them to one of those three commands |
| Debug bundle upload options now require `--upload-bundle` | Upload options without it are rejected | Add `--upload-bundle` |
| `login --extend` now requires an active connection | It cannot be combined with `--setup-key` or `--setup-key-file` | Connect first, then run `login --extend` on its own |
| Several commands no longer load option values from environment variables automatically | A variable that used to set a flag on those commands is ignored | Pass the flag explicitly. See the PR for how variables map to flags |
| The deprecated `--admin-url` flag is removed | Commands that pass it fail | Remove it from your scripts |

Deprecated, still working: the SSH `--login` flag prints a deprecation notice. Use `--user` (`-u`) instead.

[#8495](https://github.com/netbirdio/netbird/pull/8495) · [#8505](https://github.com/netbirdio/netbird/pull/8505) · [#8508](https://github.com/netbirdio/netbird/pull/8508) · [#8514](https://github.com/netbirdio/netbird/pull/8514) · [#8558](https://github.com/netbirdio/netbird/pull/8558) · [#8572](https://github.com/netbirdio/netbird/pull/8572)

## Security

- Login-expired peers are now handled everywhere the network map is built. Peers flagged as login-expired (a blocked user, inactivity, or the integrated validator) are no longer sent to other peers, even when account-level login expiration is disabled, and peers that reach a Networks router only through a resource policy are now checked for login expiry.
- DROP rules with the `netbird-ssh` protocol no longer enable the SSH server on the destination or authorize users. Only Accept rules do.

[#8382](https://github.com/netbirdio/netbird/pull/8382)

- On Linux peers, a peer ACL drop rule could open a published container port (Docker, Podman) instead of closing it. Traffic that DNAT diverts to the forward path was marked as accepted by a drop rule's counterpart in the same way as by an accept rule's, so a drop-only policy opened a port that the default deny would otherwise close. Drop rules now drop new traffic in prerouting, before DNAT, in both the nftables and iptables backends, and take precedence over overlapping allow rules. Established and related connections are not affected. On iptables, a drop rule whose counterpart cannot be installed now fails as a whole. Review ACLs on peers that publish container ports, because traffic that used to get through a drop rule is now blocked.

[#8433](https://github.com/netbirdio/netbird/pull/8433)

## Behavior changes

- **Private service denials return different status codes.** Refused requests to private services now return 404, 403 or 401 depending on the caller and the request, and tunnel-validation failures return 503. Checks or monitors that expect one fixed code need updating. [#8576](https://github.com/netbirdio/netbird/pull/8576)
- **Network map components are always on.** The option to turn them off is removed. When no sync message version is configured, the highest supported version is used, and an invalid configured version falls back to the base version. [#8662](https://github.com/netbirdio/netbird/pull/8662)
- **Peers with an expired login are omitted from the offline peers list** instead of being listed. [#8382](https://github.com/netbirdio/netbird/pull/8382)
- **Peer events carry more detail.** Events built from the peer meta now include the serial number, OS, OS version and hostname, plus the owner's user ID when the peer has an owner. [#8618](https://github.com/netbirdio/netbird/pull/8618)
- **Management adds a column on upgrade.** The peer system meta gets a hardware UUID column, which is migrated automatically. [#8541](https://github.com/netbirdio/netbird/pull/8541)
- **macOS installer scripts.** The `AGENT` variable in the `preinstall` and `postinstall` scripts is renamed `NETBIRD_BIN`. Custom packaging that sets it needs the new name. [#8678](https://github.com/netbirdio/netbird/pull/8678)

## Deprecations

- **Rosenpass** support is deprecated. `netbird up` prints a deprecation warning when it is enabled, the `--enable-rosenpass` and `--rosenpass-permissive` flags are deprecated with no replacement, and the desktop app now shows the notice in more languages. Keep it only for existing setups and do not enable it on new deployments. 📖 [Post-quantum cryptography](https://docs.netbird.io/client/post-quantum-cryptography) · [#8268](https://github.com/netbirdio/netbird/pull/8268)
- The SSH `--login` flag is deprecated. Use `--user` (`-u`).

## Highlights

### 🖥️ Embedded VNC server (opt-in)

The client can now run a built-in VNC server, so you can see and control a peer's screen from the Dashboard's browser viewer. Nothing needs to be installed on the machine you connect from, and the connection runs through your NetBird network.

**It is off by default.** Turn it on on the peer itself, with **Settings → VNC** in the desktop app or `netbird up --allow-server-vnc`. Enabling it needs administrator rights, or the `allowServerVNC` MDM key on managed machines.

- **Approval.** The person signed in on the peer approves each connection, and can allow it view-only. A connection nobody answers within 60 seconds is denied. Unattended machines can turn the prompt off with `--disable-vnc-approval`.
- **Access.** There is no VNC rule in access policies. The Dashboard grants temporary access for each session to the user who starts it, and withdraws it when the session ends. The built-in server works only with the Dashboard's browser viewer, and other VNC clients cannot connect to it.
- **Platforms.** Linux, FreeBSD, macOS and Windows. On Linux and FreeBSD you can view the current screen or open a separate desktop for a user (needs `Xvfb`, or `Xorg` with the dummy driver), and a machine with no graphical session shows its text console. On macOS the user has to grant Screen Recording and Accessibility. On Windows it follows the active session.
- **Versions.** The peer needs v0.84.0 or later, and so does the NetBird client your Dashboard serves to the browser. A self-hosted Dashboard on an older client shows "The NetBird client may not support VNC".

A peer can also reach an external VNC server that is already running on it. In that case NetBird does not check who connects and does not ask the peer's user. That depends on the server's own settings.

📖 [VNC access](https://docs.netbird.io/manage/peers/vnc)

[#6170](https://github.com/netbirdio/netbird/pull/6170)

### 🔐 Peer authentication for Signal and Relay (opt-in)

Management can now sign a short-lived token for each peer, bound to the peer's WireGuard key and a new per-peer auth key. Signal and Relay verify the token and the client's proof against Management's public keys, with no call back to Management and no database lookup.

**It is off by default, including on new installs.** Nothing changes until an operator sets a signing key on Management (`server.peerAuth.signingKeyFile` in the combined `config.yaml`; the key file is created if it does not exist yet). Install scripts and templates do not enable it. Peers and servers that do not use it keep working as before.

To turn it on, in this order:

1. Give every Relay and Signal Management's public key (`--peer-auth-public-keys`, or `NB_PEER_AUTH_PUBLIC_KEYS` in the environment). A Relay that does not have it refuses the tokens that upgraded clients present, and those clients lose every connection that goes through it, so do not skip this step.
2. Set the signing key on Management (`PeerAuth.SigningKeyFile` in `management.json`).
3. Optional, once every client is upgraded: refuse unauthenticated peers with `--peer-auth-required` (`NB_PEER_AUTH_REQUIRED`) on Relay and Signal. On a Relay this replaces `--auth-secret`: a Relay runs with one of the two, never both, and does not start with both or neither. Hostname restrictions (`NB_PEER_AUTH_HOSTNAMES`) and a proof timestamp check (`NB_PEER_AUTH_CHECK_TIME`) are optional on top.

On a combined server with its built-in Relay and Signal, setting `server.peerAuth.signingKeyFile` and restarting is enough: the key file is generated if it is missing, and the built-in Relay and Signal read the public key from it. If you use external Relays or Signal, generate the key yourself instead, because a combined server that generates it starts signing at once, before any external Relay has the public key. Keep the key file in your backups.

Older clients that do not authenticate keep working, but they can no longer take over the identity of a peer that is connected with authentication. Tokens expire after the relay credentials TTL (12 hours by default), so a Management outage longer than that stops clients from renewing them. 
📖 [Peer authentication](https://docs.netbird.io/selfhosted/maintenance/peer-authentication)

[#8493](https://github.com/netbirdio/netbird/pull/8493) · [#8747](https://github.com/netbirdio/netbird/pull/8747)

### 🌐 Network changes no longer restart the client on desktop

On Windows, macOS and Linux, a default route change used to restart the engine, which removed exit-node routes, DNS and the interface for a moment and could let overlay traffic go out over the physical network. The client now recovers the control plane and the relayed and direct connections in place, so the NetBird network stays up. Set `NB_DISABLE_NETWORK_CHANGE_REDIAL=true` on the service to bring back the restart on every change.

[#8288](https://github.com/netbirdio/netbird/pull/8288)

## What's New

### Client

- Fell back to the system hostname when the device name is empty. [#8553](https://github.com/netbirdio/netbird/pull/8553)
- Added Polish, Korean and Chinese (Hong Kong) to the language picker. [#8555](https://github.com/netbirdio/netbird/pull/8555)
- Reported the device hardware UUID in the peer system meta. [#8541](https://github.com/netbirdio/netbird/pull/8541)
- Stopped IPv6 STUN and TURN lookups from walking the DNS search list. [#8564](https://github.com/netbirdio/netbird/pull/8564)
- Sized nftables set batches to the address family. [#8578](https://github.com/netbirdio/netbird/pull/8578)
- Fixed an nftables inbound ACL install failure by using a dedicated connection for ipsets. [#7447](https://github.com/netbirdio/netbird/pull/7447)
- Dropped traffic that a peer ACL drop rule denies in prerouting, before DNAT diverts it to the forward path. [#8433](https://github.com/netbirdio/netbird/pull/8433)
- Fell back during user lookup on Windows. [#8594](https://github.com/netbirdio/netbird/pull/8594)
- Fell back when binding the SSO callback port fails. [#8712](https://github.com/netbirdio/netbird/pull/8712)
- Built Linux ARM64 packages for the UI. [#8589](https://github.com/netbirdio/netbird/pull/8589)
- Fixed the Linux amd64 UI failing to start on CPUs older than x86-64-v3, which affected releases since v0.82.1. [#8639](https://github.com/netbirdio/netbird/pull/8639)
- Sized the relay data-path socket buffers for throughput. [#6774](https://github.com/netbirdio/netbird/pull/6774)
- Added the `extraIFaceBlackList` MDM key. [#8603](https://github.com/netbirdio/netbird/pull/8603)
- Fixed the firewall config hash ignoring rules that are sent more than once. [#8450](https://github.com/netbirdio/netbird/pull/8450)
- Restarted the WireGuard watcher after an ICE handshake timeout. [#8544](https://github.com/netbirdio/netbird/pull/8544)
- Kept slow DNS upstream queries open while the next one starts. [#8643](https://github.com/netbirdio/netbird/pull/8643)
- Allowed the profile claim command when profiles are disabled. [#8677](https://github.com/netbirdio/netbird/pull/8677)
- Fixed relay connections that stayed dead after an early disconnect, and bounded relay writes with a timeout. [#8605](https://github.com/netbirdio/netbird/pull/8605) · [#8675](https://github.com/netbirdio/netbird/pull/8675)
- Centralized CLI environment variable initialization and added a developer guide for the CLI flags and variables. [#8514](https://github.com/netbirdio/netbird/pull/8514) · [#8637](https://github.com/netbirdio/netbird/pull/8637)

### Management

- Added a grace period for stale check-ins and for new peers to the EDR API. [#8549](https://github.com/netbirdio/netbird/pull/8549) · [#8653](https://github.com/netbirdio/netbird/pull/8653)
- Added device and owner fields to peer event meta. [#8618](https://github.com/netbirdio/netbird/pull/8618)
- Added the user email mapping account setting. [#8619](https://github.com/netbirdio/netbird/pull/8619)
- Recorded the source IP and location on dashboard login events. [#8626](https://github.com/netbirdio/netbird/pull/8626)
- Stored the email and name of external IdP users. [#8673](https://github.com/netbirdio/netbird/pull/8673)
- Carried the skip-auto-apply setting on network resources. [#8703](https://github.com/netbirdio/netbird/pull/8703)
- Fixed the update reason on affected network map calls, and a data race in the HTTP metrics middleware. [#8568](https://github.com/netbirdio/netbird/pull/8568) · [#8627](https://github.com/netbirdio/netbird/pull/8627)

### Relay and Signal

- Fixed a deadlock between the peer notifier and the store, closed a replaced peer outside the store lock, and stopped the healthcheck sender from blocking on an unread channel. [#8556](https://github.com/netbirdio/netbird/pull/8556) · [#8560](https://github.com/netbirdio/netbird/pull/8560) · [#8561](https://github.com/netbirdio/netbird/pull/8561)
- Sized peer state messages to the transport's message limit, and forwarded messages through a send queue per target peer. [#8573](https://github.com/netbirdio/netbird/pull/8573) · [#8679](https://github.com/netbirdio/netbird/pull/8679)
- Extended the Signal metrics. [#8680](https://github.com/netbirdio/netbird/pull/8680)

### Reverse proxy

- Returned 404, 403 or 401 for refused private service requests. [#8576](https://github.com/netbirdio/netbird/pull/8576)
- Kept gateway refusals out of the agent network log and named NetBird in the deny messages. [#8575](https://github.com/netbirdio/netbird/pull/8575)
- Reloaded proxy mappings without a request context. [#8658](https://github.com/netbirdio/netbird/pull/8658)

### Infrastructure and Miscellaneous

- Bumped the installer Traefik image to v3.7.18. [#8606](https://github.com/netbirdio/netbird/pull/8606)
- Mapped Chinese (Hong Kong) for Crowdin, spell-checked only English UI strings, and pulled in new translations. [#8554](https://github.com/netbirdio/netbird/pull/8554) · [#8268](https://github.com/netbirdio/netbird/pull/8268)
- Announced stable releases in Discord. [#8520](https://github.com/netbirdio/netbird/pull/8520)
- Called the NetBird client "client" instead of "agent", and replaced the Slack community links with Discord. [#8678](https://github.com/netbirdio/netbird/pull/8678) · [#8701](https://github.com/netbirdio/netbird/pull/8701)

**Full Changelog**: [v0.83.0...v0.84.0](https://github.com/netbirdio/netbird/compare/v0.83.0...v0.84.0)
