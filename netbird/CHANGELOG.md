# Changelog

## [v0.75.0] - 2026-07-23

### Changed
- Updated to NetBird v0.75.0

### Upstream Release Notes
## Release Notes for v0.75.0

### New Feature: Redesigned Desktop Client

This release ships a complete rewrite of the desktop client. We went ahead and replaced the old Fyne-based tray application with a new Wails v3 app backed by a React and TypeScript frontend, and it is a massive upgrade. You get a proper main connection view, an exit-node switcher, a networks and peers browser with detail panels, profile management, full settings, debug-bundle creation, and a first-run welcome flow, all in one place instead of buried in a tray menu. [#6473](https://github.com/netbirdio/netbird/pull/6473) by @pappz and @heisbrot 

<img width="1280" alt="peers-view" src="https://github.com/user-attachments/assets/557a2e35-9ead-40ea-b30b-748765d83547" />

The new UI is also translated into 10 languages now, and session handling got a lot smarter, so you actually know when your session is about to expire instead of finding out the hard way. Do note that launch-on-login is now enabled by default on fresh GUI installs, so if you manage devices through MDM, the `disableAutostart` setting is enforced on every launch to keep that under your control.

<img width="1280" alt="settings-language" src="https://github.com/user-attachments/assets/54298c90-747a-465e-b328-a5fead16bded" />

- Rebuilt the desktop client as a **Wails v3 application with a React + TypeScript frontend**, replacing the Fyne UI. [#6473](https://github.com/netbirdio/netbird/pull/6473)
- Added **internationalization with 10 locales** (English, German, Spanish, French, Hungarian, Italian, Japanese, Portuguese, Russian, and Simplified Chinese), shared between the tray and the frontend. [#6473](https://github.com/netbirdio/netbird/pull/6473), [#6790](https://github.com/netbirdio/netbird/pull/6790) by @s-shimizu-clpl
- Added a **new system tray** with per-platform theme-aware icons, including a native XEmbed host and theme watcher on Linux. [#6473](https://github.com/netbirdio/netbird/pull/6473)
- Improved **session handling** with an auth session watcher, pending login flow, session-expiration dialog and tray notifications, and `netbird login` improvements. [#6473](https://github.com/netbirdio/netbird/pull/6473)
- Extended the **daemon API** with status stream subscription, an event stream, networks and exit-node selection endpoints, and richer full status, with probe throttling to protect the daemon from UI-driven request storms. [#6473](https://github.com/netbirdio/netbird/pull/6473)
- Enabled **launch-on-login by default on fresh GUI installs**, managed through the daemon as the single source of truth (HKCU on Windows). [#6738](https://github.com/netbirdio/netbird/pull/6738) by @mlsmaycon
- Enforced **MDM `disableAutostart` on every GUI launch**, not just fresh installs. [#6782](https://github.com/netbirdio/netbird/pull/6782) by @riccardomanfrin

Learn more:
- Desktop app overview: https://docs.netbird.io/client/desktop-app
- Profiles: https://docs.netbird.io/client/profiles
- Deep dive on the new app: https://netbird.io/knowledge-hub/netbird-v0-75-new-desktop-app

### What's Changed

#### Desktop Client Improvements

- Made the client **connect immediately on profile selection**, except while managing profiles. [#6838](https://github.com/netbirdio/netbird/pull/6838) by @pappz
- Brought the **connection up in Go after SSO login** for a faster, more reliable post-login connect. [#6744](https://github.com/netbirdio/netbird/pull/6744) by @mlsmaycon
- Kept the **session deadline visible across reconnects**. [#6847](https://github.com/netbirdio/netbird/pull/6847) by @pappz
- Fixed the **browser dialog not closing** during the renew-session flow. [#6745](https://github.com/netbirdio/netbird/pull/6745) by @heisbrot
- Disconnected the **daemon on GUI quit** via an async Down call. [#6796](https://github.com/netbirdio/netbird/pull/6796) by @pappz
- Restored **residual state in foreground mode** before login. [#6707](https://github.com/netbirdio/netbird/pull/6707) by @dfry
- Clarified the **outdated client overlay** wording. [#6718](https://github.com/netbirdio/netbird/pull/6718) by @heisbrot
- Used **menu-bar wording on the macOS welcome screen**. [#6810](https://github.com/netbirdio/netbird/pull/6810) by @heisbrot
- Added **SSO login flow timing instrumentation**. [#6717](https://github.com/netbirdio/netbird/pull/6717) by @mlsmaycon
- Updated **Wails to v3.0.0-alpha2.117**. [#6837](https://github.com/netbirdio/netbird/pull/6837) by @pappz

#### Client Improvements

- Added a **JSON gateway for the NetBird daemon**, exposing the daemon API over HTTP/JSON. [#6272](https://github.com/netbirdio/netbird/pull/6272) by @jnfrati
- Introduced **client-side event aggregation**. [#6627](https://github.com/netbirdio/netbird/pull/6627) by @dmitri-netbird
- Offloaded **client config generation to the client**, reducing work on the management server. [#6711](https://github.com/netbirdio/netbird/pull/6711) by @dmitri-netbird
- Warmed **lazy connections from the DNS resolver** so lazily-connected peers come up faster. [#6854](https://github.com/netbirdio/netbird/pull/6854) by @mlsmaycon
- Fixed **forwarder peers never being excluded from lazy connections**. [#6674](https://github.com/netbirdio/netbird/pull/6674) by @riccardomanfrin
- Fixed **WGWatcher silently failing to restart** on fast disconnect/reconnect. [#6664](https://github.com/netbirdio/netbird/pull/6664) by @riccardomanfrin
- Cleared **stale UDP checksums in the eBPF XDP proxy** after port rewrite. [#6861](https://github.com/netbirdio/netbird/pull/6861) by @lixmal
- Raised the **relay early-message buffer cap to 10,000** to avoid dropping relayed handshakes. [#6752](https://github.com/netbirdio/netbird/pull/6752) by @riccardomanfrin
- Fixed a **nil-context panic in the iOS dynamic route resolver**. [#6848](https://github.com/netbirdio/netbird/pull/6848) by @pappz
- Fixed a **DNS probe listener panic** on unparseable local addresses. [#6797](https://github.com/netbirdio/netbird/pull/6797) by @pappz
- Fixed the **browser (WASM) relay WebSocket close** and raised the RDP dial timeout. [#6684](https://github.com/netbirdio/netbird/pull/6684) by @lixmal
- Included **system events in status conversion**. [#6746](https://github.com/netbirdio/netbird/pull/6746) by @lixmal
- Refreshed **WireGuard stats in mobile debug bundles**. [#6814](https://github.com/netbirdio/netbird/pull/6814) by @pappz
- Distinguished **empty vs. corrupt state** in debug diagnostics. [#6816](https://github.com/netbirdio/netbird/pull/6816) by @pappz

#### Management Improvements

- Added the **`dashboard_features` account setting** [#6742](https://github.com/netbirdio/netbird/pull/6742) and the **`agent_network_only` account setting** [#6736](https://github.com/netbirdio/netbird/pull/6736), with `agent_network_only` requiring `dashboard_features.agent_network` to be enabled [#6750](https://github.com/netbirdio/netbird/pull/6750) — all by @mlsmaycon
- Added **traffic filters for source and destination ID**. [#6697](https://github.com/netbirdio/netbird/pull/6697) by @pascal-fischer
- Allowed **disabling the device code flow when using Dex**. [#6809](https://github.com/netbirdio/netbird/pull/6809) by @pascal-fischer
- Propagated **auth grant types for the combined server**. [#6817](https://github.com/netbirdio/netbird/pull/6817) by @pascal-fischer
- Built **routes for the peer cache on network map components** [#6780](https://github.com/netbirdio/netbird/pull/6780) and added **component types** [#6866](https://github.com/netbirdio/netbird/pull/6866) — both by @pascal-fischer
- Fixed **fetching of missing settings in the GetAccount call**. [#6800](https://github.com/netbirdio/netbird/pull/6800) by @dmitri-netbird
- Fixed a **duplicate operationId in the OpenAPI spec**. [#6734](https://github.com/netbirdio/netbird/pull/6734) by @CoderSufiyan
- Enabled **pprof via an environment variable**. [#6778](https://github.com/netbirdio/netbird/pull/6778) by @pascal-fischer
- Added **logging to ephemeral peer deletion**. [#6747](https://github.com/netbirdio/netbird/pull/6747) by @pascal-fischer

#### Agent Network

- Added **Kimi (Moonshot AI)** to the provider catalog. [#6853](https://github.com/netbirdio/netbird/pull/6853) by @mlsmaycon
- Added **Bedrock cost-allocation metadata** plus a per-provider `metadata_disabled` option. [#6791](https://github.com/netbirdio/netbird/pull/6791) by @mlsmaycon
- Matched **Bedrock provider models against the normalized request model**. [#6773](https://github.com/netbirdio/netbird/pull/6773) by @mlsmaycon
- Probed the **agent-network endpoint with a GET instead of getent**. [#6867](https://github.com/netbirdio/netbird/pull/6867) by @mlsmaycon
- Fixed the **proxy multi-stage Docker build**. [#6864](https://github.com/netbirdio/netbird/pull/6864) by @mlsmaycon

#### Relay Improvements

- Trusted **`X-Real-Ip` headers only from configured trusted proxies**. [#6833](https://github.com/netbirdio/netbird/pull/6833) by @pappz
- Removed the **deprecated Hello handshake and gob token decode**. [#6783](https://github.com/netbirdio/netbird/pull/6783) by @lixmal

#### Self-Hosting Improvements

- Added a **unified admin CLI for self-hosted helpers**. [#6507](https://github.com/netbirdio/netbird/pull/6507) by @jnfrati
- Simplified the **enterprise bootstrap**. [#6869](https://github.com/netbirdio/netbird/pull/6869) by @bcmmbaga

#### Internal, CI, and Docs

- Copied the **trustedproxy package into the Docker build context**. [#6851](https://github.com/netbirdio/netbird/pull/6851) by @pappz
- Ran **pnpm install with `--ignore-scripts`** in frontend CI. [#6859](https://github.com/netbirdio/netbird/pull/6859) by @pappz
- Re-generated **gateway proto files**. [#6696](https://github.com/netbirdio/netbird/pull/6696) by @jnfrati
- Fixed **flaky tests** in account settings and event aggregation [#6811](https://github.com/netbirdio/netbird/pull/6811), [#6710](https://github.com/netbirdio/netbird/pull/6710) by @dmitri-netbird, and in the peer-connect handshake wait [#6871](https://github.com/netbirdio/netbird/pull/6871) by @riccardomanfrin
- Updated the **Agent Network readme**. [#6699](https://github.com/netbirdio/netbird/pull/6699) by @braginini

### New Contributors

- @CoderSufiyan made their first contribution in [#6734](https://github.com/netbirdio/netbird/pull/6734)
- @s-shimizu-clpl made their first contribution in [#6790](https://github.com/netbirdio/netbird/pull/6790)

---

**Full Changelog**: https://github.com/netbirdio/netbird/compare/v0.74.7...v0.75.0
