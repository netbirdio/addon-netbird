# Changelog

## [v0.81.0] - 2026-10-15

### Changed
- Updated to NetBird v0.81.0

### Upstream Release Notes
# Release Notes for v0.81.0

## Highlights

### 🪪 Certificate Posture Checks

A new **Certificate** posture check requires peers to hold a device certificate issued by your own CA, and to prove they still have the private key by signing a challenge from management. A copied certificate without its key fails the check. Management repeats the challenge every 4 hours while the peer is connected, so a removed key or an expired certificate closes access within that window.

The client uses the operating system's own certificate stores: the Local Computer and user Personal stores on Windows (TPM-backed keys included), the System and login keychains on macOS (Secure Enclave keys included), and PEM files in `/etc/netbird/certs`, TSS2 key files, or a tpm2-pkcs11 token on Linux. Paste one or more root or intermediate CA certificates in the dashboard and attach the check to any access policy.

Requires management and client **v0.81.0** or newer. The iOS and Android apps do not support certificate proofs and always fail this check.

<img width="400" height="600" alt="image" src="https://github.com/user-attachments/assets/634c949f-3408-4148-aba2-eae78ceaa9fd" />

📖 [Requiring a device certificate](https://docs.netbird.io/manage/access-control/posture-checks/device-certificates) · [#7535](https://github.com/netbirdio/netbird/pull/7535), [#8052](https://github.com/netbirdio/netbird/pull/8052), [#7890](https://github.com/netbirdio/netbird/pull/7890), [#8174](https://github.com/netbirdio/netbird/pull/8174), [dashboard#798](https://github.com/netbirdio/dashboard/pull/798)

### 🔗 nblink: forward into your network without root

`nblink` is a new standalone binary that forwards local ports into your NetBird network from an ordinary, unprivileged process. It runs the client in userspace mode, so there is no TUN device, no root, no installer, and `/etc/resolv.conf` is never touched. Names on the upstream side resolve inside the tunnel.

It is built for places the regular agent cannot run: managed laptops without admin rights, rootless containers on OpenShift and Podman, CI jobs, MCP servers and local AI agents that need an internal API, and sidecars for apps that hardcode an HTTP endpoint.

```shell
nblink --forward 'http://3000=https://tickets.internal' --forward 'http://8080=https://grafana.internal'
```

With no setup key, `nblink` opens the browser for login and always prints the login URL, so the same command works over SSH. The peer carries a real identity, so what it can reach is decided by your policies. This release forwards HTTP; raw TCP, UDP and SOCKS5 are planned with the same `--forward` syntax.

Binaries ship for Linux, macOS and Windows (`nblink_<version>_<os>_<arch>`) on this release page, and as the `netbirdio/nblink` and `ghcr.io/netbirdio/nblink` container images.

📖 [nblink](https://docs.netbird.io/client/nblink) · [#7955](https://github.com/netbirdio/netbird/pull/7955)

### 📋 Duplicate Policies

Access Control and Agent Network policies now have a **Duplicate** action in the row menu. It opens the create wizard pre-filled with every setting of the source policy, named `<name> (copy)`, and nothing is saved until you click **Add Policy**. Groups, posture checks, resources, providers and guardrails are reused rather than cloned.

<!-- TODO: screenshot of the policy row menu with Duplicate (no docs screenshot exists yet) -->

📖 [Managing Policies](https://docs.netbird.io/manage/access-control/manage-network-access#managing-policies) · [dashboard#841](https://github.com/netbirdio/dashboard/pull/841)

### 🛣️ Path-based access control for Reverse Proxy services

HTTP targets of a Reverse Proxy service can now override the service's authentication for their path. Under a target's **Optional Settings**, choose an **Access** action:

| Action | Behavior |
|--------|----------|
| **Use service authentication** | Apply the authentication configured on the service (default). |
| **Bypass authentication** | Skip SSO, password, PIN and header authentication for this path. CIDR, country and CrowdSec restrictions still apply. |
| **Block access** | Return `403` without contacting the backend. |

A service can require SSO on `/`, bypass authentication for webhook receivers or health checks on `/webhooks/` or `/healthz`, and block `/admin/` or `/metrics` from the public domain entirely. Matching uses the longest case-sensitive literal prefix, and when any target uses an access action the proxy rejects ambiguous paths (`..`, `//`, encoded separators) with `400` before matching. Targets that override the default are marked in the services list.

<img width="200" height="400" alt="image" src="https://github.com/user-attachments/assets/0d53ba58-7c11-4180-ba1e-c894e09f3300" />



<img width="400" height="600" alt="image" src="https://github.com/user-attachments/assets/7be1b62e-d517-435e-be32-7645455feb83" />

Upgrade every proxy in the cluster before using these actions; older proxies ignore them and keep applying the service authentication. **Bypass authentication** is unavailable for NetBird-Only services and Agent Network targets.

📖 [Per-target access actions](https://docs.netbird.io/manage/reverse-proxy/authentication#per-target-access-actions) · [#8114](https://github.com/netbirdio/netbird/pull/8114), [#8115](https://github.com/netbirdio/netbird/pull/8115), [#8116](https://github.com/netbirdio/netbird/pull/8116), [#8117](https://github.com/netbirdio/netbird/pull/8117), [dashboard#823](https://github.com/netbirdio/dashboard/pull/823)


### Deprecation: MySQL store

MySQL support for the Management store is deprecated and will be removed in an upcoming release. If your self-hosted deployment runs on MySQL, migrate to PostgreSQL before then. SQLite and PostgreSQL remain fully supported.

This release adds `netbird-mysql-migrate`, a tool that moves your deployment's data into PostgreSQL. See the migration guide: https://docs.netbird.io/selfhosted/migration/mysql-to-postgresql

## What's New

### 🔒 Security and Access Control

* Added the **Certificate posture check**, with a hardened client, fresh challenge nonces, recovery from stuck proof collections, and fixes for remaining edge cases. [#7535](https://github.com/netbirdio/netbird/pull/7535), [#8052](https://github.com/netbirdio/netbird/pull/8052), [#7890](https://github.com/netbirdio/netbird/pull/7890), [#8174](https://github.com/netbirdio/netbird/pull/8174)
* **Used single-use codes for the OIDC session handoff** between management and the proxy, and renamed the session code query parameter. [#7635](https://github.com/netbirdio/netbird/pull/7635), [#7981](https://github.com/netbirdio/netbird/pull/7981)
* **Enforced strict base64url decoding for JWT validation** in management and the proxy. [#7554](https://github.com/netbirdio/netbird/pull/7554)
* **Refactored the PKCE verifier store into a reusable single-use store**. [#7634](https://github.com/netbirdio/netbird/pull/7634)
* Added a **revocation guard hook to the proxy token API**. [#7732](https://github.com/netbirdio/netbird/pull/7732)
* **Returned a permission-denied error when a setup key is expired or invalid**, instead of a generic failure. [#8086](https://github.com/netbirdio/netbird/pull/8086)

### 🖥️ Client Improvements

* Added **nblink**, an unprivileged forwarder into the network. [#7955](https://github.com/netbirdio/netbird/pull/7955)
* **Replaced the eBPF WireGuard proxy with loopback endpoint addressing**, removing the eBPF dependency for kernel-mode relayed connections. [#7316](https://github.com/netbirdio/netbird/pull/7316)
* **Stopped one unreachable relay from churning connections on the others**, and stopped offering to every peer when the relay transport drops. [#8098](https://github.com/netbirdio/netbird/pull/8098), [#7092](https://github.com/netbirdio/netbird/pull/7092)
* **Kept the advertised ICE session ID when following a remote restart**, avoiding unnecessary reconnects. [#7814](https://github.com/netbirdio/netbird/pull/7814)
* **Cached the box shared key per remote peer** in the Signal client, and cached the interface check shared by ICE agents. [#7807](https://github.com/netbirdio/netbird/pull/7807), [#8001](https://github.com/netbirdio/netbird/pull/8001)
* **Configured the userspace device through its typed API** and **reused one wgctrl client** in the kernel configurer. [#7937](https://github.com/netbirdio/netbird/pull/7937), [#7912](https://github.com/netbirdio/netbird/pull/7912)
* **Discovered interfaces lazily in stdnet** instead of at construction. [#7346](https://github.com/netbirdio/netbird/pull/7346)
* **Forced interactive login when extending the auth session**, evaluated the session deadline against the wall clock, and dropped the pending login flow when a login switches profile. [#7216](https://github.com/netbirdio/netbird/pull/7216), [#7959](https://github.com/netbirdio/netbird/pull/7959), [#7882](https://github.com/netbirdio/netbird/pull/7882)
* **Kept the updater silent until management decides the update mode**. [#7455](https://github.com/netbirdio/netbird/pull/7455)
* **Gated settings updates on value, not on field presence**. [#7398](https://github.com/netbirdio/netbird/pull/7398)
* Added **debug cpu start and stop** commands for CPU profiling. [#7749](https://github.com/netbirdio/netbird/pull/7749)
* **Classified Windows local accounts by NetBIOS name**. [#7628](https://github.com/netbirdio/netbird/pull/7628)
* Fixed the **Windows UI exiting on first launch**, and closed the UI before InstallValidate in the MSI. [#8138](https://github.com/netbirdio/netbird/pull/8138), [#7395](https://github.com/netbirdio/netbird/pull/7395)
* Fixed the **browser login popup** shown from the daemon, and kept the delete-profile dialog open until the delete finishes. [#7408](https://github.com/netbirdio/netbird/pull/7408), [#7752](https://github.com/netbirdio/netbird/pull/7752)
* Added **RTL layout support** and **Galician (gl) localization** to the desktop UI, warned on missing translations, and only offered locales listed in `_index.json`. [#8076](https://github.com/netbirdio/netbird/pull/8076), [#7041](https://github.com/netbirdio/netbird/pull/7041), [#8090](https://github.com/netbirdio/netbird/pull/8090), [#8091](https://github.com/netbirdio/netbird/pull/8091)

### 📱 Mobile

* **Coalesced peer list change notifications** to the Android and iOS listener. [#7546](https://github.com/netbirdio/netbird/pull/7546)
* **Generated the Android debug bundle to a file**. [#7528](https://github.com/netbirdio/netbird/pull/7528)
* **Resolved the Android network route peer by HA unique ID** instead of scanning the full status. [#7705](https://github.com/netbirdio/netbird/pull/7705)
* **Skipped late session warnings on desktop and scheduled them in the app on Android**. [#7548](https://github.com/netbirdio/netbird/pull/7548)

### ⚙️ Management Improvements

* **Exposed peer MAC addresses and made peers searchable by MAC**. [#6553](https://github.com/netbirdio/netbird/pull/6553)
* Added an **account setting to return an error on peer name collision** during AddPeer. [#8154](https://github.com/netbirdio/netbird/pull/8154)
* **Refreshed only affected peers** on DNS zone, DNS record and IPv6 settings changes. [#8050](https://github.com/netbirdio/netbird/pull/8050), [#8051](https://github.com/netbirdio/netbird/pull/8051)
* **Emitted activity events for batch group operations**. [#8180](https://github.com/netbirdio/netbird/pull/8180)
* Added a **MySQL to PostgreSQL migration tool**. [#8095](https://github.com/netbirdio/netbird/pull/8095)
* **Made the Let's Encrypt challenge listener address configurable** for management and signal. [#7706](https://github.com/netbirdio/netbird/pull/7706)
* **Tore down only the peer session that owns the stream**. [#8057](https://github.com/netbirdio/netbird/pull/8057)
* **Updated proxy mappings on peer IP change** and the reserved IP exclusion when generating peer IPs. [#8139](https://github.com/netbirdio/netbird/pull/8139), [#8144](https://github.com/netbirdio/netbird/pull/8144)
* **Expanded loaded snapshots in place**, reducing allocations on network map computation. [#8177](https://github.com/netbirdio/netbird/pull/8177)
* **Cleaned up Agent Network configuration on account deletion** and added a disabled state to the managed Agent Network proxy API. [#7812](https://github.com/netbirdio/netbird/pull/7812), [#7744](https://github.com/netbirdio/netbird/pull/7744)
* **Switched to libopenapi** for managing the OpenAPI-based API, and added the tenant delete endpoint to the spec. [#8056](https://github.com/netbirdio/netbird/pull/8056), [#8054](https://github.com/netbirdio/netbird/pull/8054)
* **Improved Base62 encoding and decoding** performance and robustness. [#3391](https://github.com/netbirdio/netbird/pull/3391)
* **Removed ingress ports**. [#8062](https://github.com/netbirdio/netbird/pull/8062)
* Extracted a **shared database connection and data repository**, and handled connection close on error. [#7649](https://github.com/netbirdio/netbird/pull/7649), [#7740](https://github.com/netbirdio/netbird/pull/7740)
* Fixed the **login filter**, created HttpConfig before applying TLS flag overrides, and moved the child-account check. [#7739](https://github.com/netbirdio/netbird/pull/7739), [#8168](https://github.com/netbirdio/netbird/pull/8168), [#8195](https://github.com/netbirdio/netbird/pull/8195)

### 🔀 Reverse Proxy

* Added **per-target HTTP access actions** (use service authentication, bypass, block), backed by middleware snapshot revisions and container E2E coverage. [#8114](https://github.com/netbirdio/netbird/pull/8114), [#8115](https://github.com/netbirdio/netbird/pull/8115), [#8116](https://github.com/netbirdio/netbird/pull/8116), [#8117](https://github.com/netbirdio/netbird/pull/8117)
* **Required a private proxy cluster for cluster and direct upstream targets**. [#7984](https://github.com/netbirdio/netbird/pull/7984)
* Added an **option to refuse private addresses on direct-upstream dials**. [#7913](https://github.com/netbirdio/netbird/pull/7913)
* **Fell back to the IdP for the proxy session email** when the token does not carry one. [#8112](https://github.com/netbirdio/netbird/pull/8112)

### 📡 Signal and Relay

* **Fixed a race in Relay server shutdown**. [#7607](https://github.com/netbirdio/netbird/pull/7607)
* **Stopped Signal with an error when a server fails**, instead of continuing half-started. [#8105](https://github.com/netbirdio/netbird/pull/8105)

### 🏗️ Infrastructure and CI

* Added **release-wired Red Hat UBI image variants** for management, signal, relay and the combined server, sharing one license collection script. [#8167](https://github.com/netbirdio/netbird/pull/8167), [#7458](https://github.com/netbirdio/netbird/pull/7458), [#7457](https://github.com/netbirdio/netbird/pull/7457), [#7953](https://github.com/netbirdio/netbird/pull/7953), [#8055](https://github.com/netbirdio/netbird/pull/8055)
* Fixed **Red Hat certification preflight** artifact handling. [#7947](https://github.com/netbirdio/netbird/pull/7947), [#8111](https://github.com/netbirdio/netbird/pull/8111)
* **Fixed Traefik versioning and updated CrowdSec** in the self-hosted setup. [#8152](https://github.com/netbirdio/netbird/pull/8152)
* **Replaced the `which` dependency with `command`** in `configure.sh`. [#8007](https://github.com/netbirdio/netbird/pull/8007)
* Added a **Homebrew cask e2e test**, moved the FreeBSD port test to 15.1, and reported both peers' state when the connect test times out. [#7618](https://github.com/netbirdio/netbird/pull/7618), [#7999](https://github.com/netbirdio/netbird/pull/7999), [#7944](https://github.com/netbirdio/netbird/pull/7944)
* **Exempted Crowdin service PRs** from title and docs checks. [#7170](https://github.com/netbirdio/netbird/pull/7170)
* Test fixes for the update message tests, peer IP allocation, and mysql-migrate container tests. [#8000](https://github.com/netbirdio/netbird/pull/8000), [#8173](https://github.com/netbirdio/netbird/pull/8173), [#8121](https://github.com/netbirdio/netbird/pull/8121), [#8128](https://github.com/netbirdio/netbird/pull/8128)

### 📝 Misc

* **Used ASCII hyphens in the LICENSE header** and extended the copyright year to 2026. [#8065](https://github.com/netbirdio/netbird/pull/8065), [#8169](https://github.com/netbirdio/netbird/pull/8169)

## Dashboard (v2.95.0)

* Added the **Certificate posture check** with CA validation, SHA-256 fingerprints, and a mobile-unsupported warning. [dashboard#798](https://github.com/netbirdio/dashboard/pull/798)
* Added **Duplicate** to the Access Control and Agent Network policy row menus. [dashboard#841](https://github.com/netbirdio/dashboard/pull/841)
* Added **per-target HTTP access controls** for Reverse Proxy services, with access badges in target lists. [dashboard#823](https://github.com/netbirdio/dashboard/pull/823)
* Added a **setting to fail on peer name collision**. [dashboard#851](https://github.com/netbirdio/dashboard/pull/851)
* Added **peer search by MAC address**. [dashboard#683](https://github.com/netbirdio/dashboard/pull/683)
* **Added OpenCode and Pi to Connect Agent** with a provider and model picker, and showed which models "All Models" covers. [dashboard#856](https://github.com/netbirdio/dashboard/pull/856), [dashboard#816](https://github.com/netbirdio/dashboard/pull/816)
* **Streamlined Agent Network onboarding**, adjusted it for existing empty accounts, and made sure it adds the user to Users. [dashboard#822](https://github.com/netbirdio/dashboard/pull/822), [dashboard#839](https://github.com/netbirdio/dashboard/pull/839), [dashboard#840](https://github.com/netbirdio/dashboard/pull/840)
* **Drew the user view's agent policies from its peers**. [dashboard#820](https://github.com/netbirdio/dashboard/pull/820)
* **Prevented duplicate routing peers** and fixed live routing peer creation in the Control Center. [dashboard#836](https://github.com/netbirdio/dashboard/pull/836)
* **Showed enable and disable events** for EDR and IdP sync integrations. [dashboard#849](https://github.com/netbirdio/dashboard/pull/849)
* Fixed **searching group peers by owner** and **sortable column headers** for Name, Metric and Domain. [dashboard#818](https://github.com/netbirdio/dashboard/pull/818), [dashboard#681](https://github.com/netbirdio/dashboard/pull/681)
* **Blocked search engines by default** with `robots.txt`. [dashboard#827](https://github.com/netbirdio/dashboard/pull/827)
* Added a **Red Hat UBI image variant** (`-ubi`). [dashboard#829](https://github.com/netbirdio/dashboard/pull/829)
* Added **Storybook with a visual regression check**, and let the e2e suite run against a deployed dashboard. [dashboard#843](https://github.com/netbirdio/dashboard/pull/843), [dashboard#842](https://github.com/netbirdio/dashboard/pull/842)
* Pointed bug reports and feature requests to Discussions, made Manage Plan a primary button, and updated announcements. [dashboard#824](https://github.com/netbirdio/dashboard/pull/824), [dashboard#828](https://github.com/netbirdio/dashboard/pull/828), [dashboard#830](https://github.com/netbirdio/dashboard/pull/830), [dashboard#848](https://github.com/netbirdio/dashboard/pull/848)

## New Contributors
* @a-elkaim made their first contribution in [#6553](https://github.com/netbirdio/netbird/pull/6553)
* @redouan-rhazouani made their first contribution in [#3391](https://github.com/netbirdio/netbird/pull/3391)
* @janekhaertter made their first contribution in [#8065](https://github.com/netbirdio/netbird/pull/8065)
* @B1NAR10 made their first contribution in [#7041](https://github.com/netbirdio/netbird/pull/7041)
* @emrcbrn made their first contribution in [#8138](https://github.com/netbirdio/netbird/pull/8138)

**Full Changelog**: [v0.80.0...v0.81.0](https://github.com/netbirdio/netbird/compare/v0.80.0...v0.81.0)
