# Changelog

## [v0.82.0] - 2026-10-29

### Changed
- Updated to NetBird v0.82.0

### Upstream Release Notes
# Release Notes for v0.82.0

## Highlights

### 🛣️ Latency-aware HA routing that holds stable routes

Routing peers in an HA group with equal metrics are meant to be chosen by latency, but the client used to sample latency only once, when the connection was set up, and moved the route on events that did not need it. Every switch between masquerading routing peers resets the TCP connections using the route, so SSH sessions and other long-lived flows dropped more often than they had to.

The client now samples the latency of connected routing peers continuously and smooths it, and moves a route for latency only when the gain clears a margin: at least 20ms, 20% of the current latency, and the measurement noise of both paths. After a latency-driven switch it keeps the new peer for at least 30 seconds. A routing peer that drops from direct to relay keeps the route for a grace period before it moves to an equivalent direct peer, and a peer that comes up over relay first is held the same way while the others turn direct. Failover, better metrics and connected-over-idle still switch immediately.

The grace period is set on the client with `NB_HA_RELAYED_SWITCH_DELAY` (a duration, `0`, or `never`; default `30s`). Routing peer switches are now logged at warning level with the reason and published as system events, so they show in `netbird status -d` and in debug bundles.

<!-- TODO: screenshot of netbird status -d showing a routing peer switch event -->

📖 [High availability for routing peers](https://docs.netbird.io/manage/networks/how-routing-peers-work#high-availability) · [#7689](https://github.com/netbirdio/netbird/pull/7689), [#8192](https://github.com/netbirdio/netbird/pull/8192)

### 📋 `netbird status --table`

`netbird status --table` (`-t`) lists peers in a table with their name, IP, status, connection type, latency and traffic, which is easier to scan than the detailed output on a large network. It works with the existing filter flags, so `netbird status -t -S connected -T P2P` shows only connected peer-to-peer peers.

```text
Peers (3/5 connected):
NAME          IP            STATUS       CONN      LATENCY   RX / TX
web-01        100.64.0.12   Connected    P2P       12ms      1.2 MiB / 3.4 MiB
db-01         100.64.0.15   Connected    Relayed   48ms      54.0 KiB / 12.1 KiB
ci-runner     100.64.0.40   Connecting   -         -         0 B / 0 B
```

`--table` cannot be combined with `-d`, `--json`, `--yaml`, `--ipv4` or `--ipv6`.

📖 [CLI: status, table view](https://docs.netbird.io/get-started/cli#table-view) · [#8305](https://github.com/netbirdio/netbird/pull/8305)

### 🗄️ A dedicated database for reverse proxy access logs

Reverse proxy access logs are now read and written through their own database connection instead of the main store's, and can live in a separate database. Nothing needs to change on upgrade: by default the access log store follows the main store engine, as a separate `access_logs.db` file in the data directory on SQLite, or the main store DSN through its own connection pool on PostgreSQL.

To point it elsewhere, set `server.accessLogStore` (`engine`, `dsn`, `file`) in the combined server's `config.yaml`, or the environment variables below:

| Variable | Purpose |
| --- | --- |
| `NB_ACCESS_LOG_STORE_ENGINE` | `sqlite` or `postgres` |
| `NB_ACCESS_LOG_POSTGRES_DSN` | a dedicated PostgreSQL database |
| `NB_ACCESS_LOG_SQLITE_FILE` | the SQLite file, `access_logs.db` by default |
| `NB_ACCESS_LOG_SQL_MAX_OPEN_CONNS` | pool size, falling back to `NB_SQL_MAX_OPEN_CONNS` |

📖 [Reverse proxy access logs](https://docs.netbird.io/manage/reverse-proxy/access-logs) · [#8303](https://github.com/netbirdio/netbird/pull/8303)

### Removal: MySQL store

The MySQL store engine for management, deprecated in v0.81.0, is removed. A management server configured with the `mysql` engine refuses to start on v0.82.0 and points to the migration guide.

**Migrate to PostgreSQL before upgrading.** The `netbird-mysql-migrate` tool is no longer part of the release, so run the migration while still on 0.81.x, with the tool from the [v0.81.5 release](https://github.com/netbirdio/netbird/releases/tag/v0.81.5), then upgrade.

📖 [Migrate from MySQL to PostgreSQL](https://docs.netbird.io/selfhosted/migration/mysql-to-postgresql) · [#7914](https://github.com/netbirdio/netbird/pull/7914)

### Deprecation: Rosenpass

Rosenpass support is deprecated. It keeps working for now, but it may be removed in a future release, so avoid making new deployments depend on it. `--enable-rosenpass` and `--rosenpass-permissive` are hidden from `netbird up --help` and print a deprecation warning whenever they are used, and the desktop app shows a notice under the quantum-resistance toggle in **Settings → Security**.

<!-- TODO: screenshot of the deprecation notice in Settings → Security -->

📖 [Post-quantum cryptography (Rosenpass)](https://docs.netbird.io/client/post-quantum-cryptography) · [#8372](https://github.com/netbirdio/netbird/pull/8372)

## What's New

### 🔒 Security and Access Control

* **Applied the SSH root-login setting to every uid 0 account**, not only to the account named `root`, so aliases such as FreeBSD's `toor` are covered; an account whose uid cannot be parsed is treated as privileged. [#8390](https://github.com/netbirdio/netbird/pull/8390)
* **Refused path-bearing `LANGUAGE`/`LC_*`, `TERM` and `TZ` values from SSH clients** before they reach the privileged `login`/`su` call for a session. [#8388](https://github.com/netbirdio/netbird/pull/8388)
* **Matched the account id from the request against the service configuration** in the reverse proxy's auth and access log middleware. [#8386](https://github.com/netbirdio/netbird/pull/8386)
* **Masked credentials in URLs written to the management logs**. [#8250](https://github.com/netbirdio/netbird/pull/8250)

### 🖥️ Client Improvements

* **Detected default route changes in the Linux network monitor**. [#8216](https://github.com/netbirdio/netbird/pull/8216)
* **Kept the `resolv.conf` watch armed while repairing DNS in file mode**. [#8207](https://github.com/netbirdio/netbird/pull/8207)
* **Fixed a deadlock when shutting down the Signal reconnect listener**. [#8257](https://github.com/netbirdio/netbird/pull/8257)
* **Called the disconnect callback on every connection teardown**. [#8178](https://github.com/netbirdio/netbird/pull/8178)
* **Skipped peers without a PTR record in the Kubernetes commands**. [#8360](https://github.com/netbirdio/netbird/pull/8360)
* **Stopped the service and the UI before the Windows installer extracts files**. [#8255](https://github.com/netbirdio/netbird/pull/8255)
* **Moved the macOS Homebrew cask to Homebrew install steps**. [#7477](https://github.com/netbirdio/netbird/pull/7477)
* **Migrated the desktop UI to Tailwind CSS 4** and fixed the frontend npm audit findings, and bumped the wails fork to v3.0.0. [#8277](https://github.com/netbirdio/netbird/pull/8277), [#8370](https://github.com/netbirdio/netbird/pull/8370)
* **Added Hebrew to the language picker**, and fixed the grammar of the post-install message. [#8102](https://github.com/netbirdio/netbird/pull/8102), [#8242](https://github.com/netbirdio/netbird/pull/8242)
* **Removed the non-functional `--admin-url` flag**; it is now deprecated and ignored. [#8267](https://github.com/netbirdio/netbird/pull/8267)
* Bumped wireguard-go and removed the replace directives from the embedded client module. [#8229](https://github.com/netbirdio/netbird/pull/8229), [#8280](https://github.com/netbirdio/netbird/pull/8280)

### ⚙️ Management Improvements

* **Moved network map data reads onto a PostgreSQL read replica** when `NB_STORE_ENGINE_POSTGRES_REPLICA_DSN` is set; without it, reads stay on the primary. [#8302](https://github.com/netbirdio/netbird/pull/8302)
* **Sped up common queries** with new indexes for groups, events, personal access tokens and access logs, an index-friendly inner groups query, a faster posture check cache and fewer last-usage writes for users and tokens. [#8289](https://github.com/netbirdio/netbird/pull/8289), [#8297](https://github.com/netbirdio/netbird/pull/8297), [#8366](https://github.com/netbirdio/netbird/pull/8366), [#8290](https://github.com/netbirdio/netbird/pull/8290)
* **Pushed network maps when an integrated validator changes a peer's approval** outside a login, for example during a periodic EDR sync, so a newly approved peer no longer waits for an unrelated update. [#8299](https://github.com/netbirdio/netbird/pull/8299)
* **Gave the `/api/setup` owner account the single account mode domain**, so the first JIT user joins it instead of getting an account of their own. [#8147](https://github.com/netbirdio/netbird/pull/8147)
* **Used the account owner as the inviter for service user invites**. [#8276](https://github.com/netbirdio/netbird/pull/8276)
* **Added pagination to the `GetAllPeers` call**. [#8190](https://github.com/netbirdio/netbird/pull/8190)
* **Skipped the inactivity expiration rescan on connect** when a job is already scheduled, and cancelled peer expiration for accounts that no longer exist. [#8287](https://github.com/netbirdio/netbird/pull/8287), [#8218](https://github.com/netbirdio/netbird/pull/8218)
* Handled a possible nil peer when returning an empty network map, and cleaned NUL bytes from peer metadata strings. [#8285](https://github.com/netbirdio/netbird/pull/8285), [#8219](https://github.com/netbirdio/netbird/pull/8219)

### 🔀 Reverse Proxy

* **Added `--external-ip-map` to the proxy's embedded client** (`NB_PROXY_EXTERNAL_IP_MAP`), so a proxy in a container or behind NAT can advertise its public address and peers reach it directly instead of over relay. It needs a fixed, forwarded `--wg-port`, so it applies to single-account deployments. [#8185](https://github.com/netbirdio/netbird/pull/8185)
* **Returned the reason when custom domain validation fails**. [#8122](https://github.com/netbirdio/netbird/pull/8122)
* **Fixed the proxy connection lifecycle during acknowledgements**. [#8220](https://github.com/netbirdio/netbird/pull/8220)

### 📡 Signal and Relay

* **The relay server now negotiates the protocol version** instead of rejecting clients that send a different one, and skips unknown message types after authentication. Current clients still get v1 and the same bytes as before; this lets later client versions change the protocol without upgrading every relay first. The handshake size limit grows from 212 to 8192 bytes. [#8231](https://github.com/netbirdio/netbird/pull/8231)

### 🏗️ Infrastructure and CI

* Restricted the FreeBSD port job to stable releases. [#8204](https://github.com/netbirdio/netbird/pull/8204)
* Raised the WASM build size limit to 70MB. [#8281](https://github.com/netbirdio/netbird/pull/8281)
* Announced tag-pushed releases on the forum. [#8265](https://github.com/netbirdio/netbird/pull/8265)
* Let test suites outside the module reach the e2e harness network, and fixed errcheck findings from the latest golangci-lint. [#8260](https://github.com/netbirdio/netbird/pull/8260), [#8251](https://github.com/netbirdio/netbird/pull/8251)

### 📝 Misc

* New translations from Crowdin. [#8101](https://github.com/netbirdio/netbird/pull/8101)

## Dashboard (v2.96.0)

* **Saved target edits made from the network and peer pages**. [dashboard#863](https://github.com/netbirdio/dashboard/pull/863)
* **Refined the RDP certificate prompt**. [dashboard#874](https://github.com/netbirdio/dashboard/pull/874)
* **Fixed crashes**: browser translation no longer breaks the dashboard, and resetting a table page while server data loads no longer crashes. [dashboard#867](https://github.com/netbirdio/dashboard/pull/867), [dashboard#868](https://github.com/netbirdio/dashboard/pull/868)
* **Updated Next.js to 16.4.3 and Tailwind CSS to v4**, fixed the npm audit findings and the white outline flash on button click that came with Tailwind v4. [dashboard#852](https://github.com/netbirdio/dashboard/pull/852), [dashboard#876](https://github.com/netbirdio/dashboard/pull/876), [dashboard#866](https://github.com/netbirdio/dashboard/pull/866)
* Updated the announcements with the v0.81 features and bumped the embedded WASM client to v0.81.5. [dashboard#861](https://github.com/netbirdio/dashboard/pull/861), [dashboard#859](https://github.com/netbirdio/dashboard/pull/859), [dashboard#862](https://github.com/netbirdio/dashboard/pull/862), [dashboard#875](https://github.com/netbirdio/dashboard/pull/875)
* Fixed flaky Control Center draft end-to-end tests. [dashboard#869](https://github.com/netbirdio/dashboard/pull/869), [dashboard#864](https://github.com/netbirdio/dashboard/pull/864)

## New Contributors
* @vxfemboy made their first contribution in [#8178](https://github.com/netbirdio/netbird/pull/8178)
* @daniele-casciani made their first contribution in [#7477](https://github.com/netbirdio/netbird/pull/7477)
* @netbirddev made their first contribution in [#8101](https://github.com/netbirdio/netbird/pull/8101)
* @alireza-hdri made their first contribution in [#8242](https://github.com/netbirdio/netbird/pull/8242)
* @smrrazavian made their first contribution in [#8147](https://github.com/netbirdio/netbird/pull/8147)
* @RaphaelFakhri made their first contribution in [dashboard#863](https://github.com/netbirdio/dashboard/pull/863)

**Full Changelog**: [v0.81.0...v0.82.0](https://github.com/netbirdio/netbird/compare/v0.81.0...v0.82.0)
