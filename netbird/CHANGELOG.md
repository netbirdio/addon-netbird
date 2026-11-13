# Changelog

## [v0.83.0] - 2026-11-12

### Changed
- Updated to NetBird v0.83.0

### Upstream Release Notes
# Release Notes for v0.83.0

## ⚠️ Breaking changes: profile ownership and daemon access control

Each profile now has an owner, and local users can control only their own profiles as the daemon has a new IPC authorization layer. Single-user desktops and headless peers registered with a setup key and auto-start (the default) need no action. They reconnect on their own after the upgrade. Act only if one of the cases below applies to you. [#7407](https://github.com/netbirdio/netbird/pull/7407)

| Who is affected | Impact | What to do |
| --- | --- | --- |
| You connect to the daemon with `--daemon-addr tcp://…` | The daemon refuses all TCP requests. | Before upgrading, switch to the default Unix socket or Windows named pipe. 📖 [TCP sockets](https://docs.netbird.io/client/grpc-socket#tcp-sockets-deprecated) |
| A regular user or script runs `netbird` commands on a headless machine (no console login, SSH and RDP don't count) | Commands fail because nobody owns the default profile. | Run `sudo netbird up` once, or run `sudo netbird profile claim default --owner <user>`. 📖 [Headless setup](https://docs.netbird.io/client/profile-ownership#set-up-a-headless-machine) |
| Several local users use NetBird on one machine | Users see and control only their own profiles. Profiles added with `sudo` belong to root. | Add profiles as the user who will use them. To share a profile, set `NB_DISABLE_PROFILE_OWNERSHIP=true` (see below). 📖 [Share a profile](https://docs.netbird.io/client/profile-ownership#share-a-profile-between-users) |

Existing profiles migrate automatically on the first start. 📖 [Upgrade from an earlier version](https://docs.netbird.io/client/profile-ownership#upgrade-from-an-earlier-version)

**New:** To limit daemon access to one group, use `netbird service install --allow-group <group>`. 📖 [Restrict daemon access](https://docs.netbird.io/client/profile-ownership#restrict-daemon-access-to-a-group)

**Opting out:** Set these variables with `netbird service install --service-env …`:

| Variable | Effect |
| --- | --- |
| `NB_DISABLE_PROFILE_OWNERSHIP=true` | Turns ownership off, so all local users share all profiles. Use it together with `--allow-group`. |
| `NB_DISABLE_DEFAULT_PROFILE_CLAIM=true` | Leaves the default profile without an owner until an administrator claims it. Recommended on servers. |

📖 [Profile ownership and daemon access](https://docs.netbird.io/client/profile-ownership) · [Troubleshooting](https://docs.netbird.io/client/profile-ownership#troubleshooting)

## Highlights

### 🤖 Manage NetBird from AI agents with the MCP server

Management can now serve its REST API as a [Model Context Protocol](https://modelcontextprotocol.io) server at `/api/mcp`, so AI agents such as Claude Code can manage a NetBird account with the same permissions as the user they act for. Every tool is one REST operation run in-process against the API, so a tool call goes through the same handler and permission checks and returns the same JSON as the REST call. Tools only act on the caller's own account, and `update_*` tools are marked destructive.

Clients sign in with OAuth: management runs its own OAuth 2.1 authorization server, sends the user to the configured identity provider to log in, and issues tokens that are accepted only on `/api/mcp`. Personal access tokens work too. On the consent page, write access is unticked by default, so a connection is read-only unless the user ticks **Make changes**. Sign-ins, sign-outs and sessions revoked after token reuse are recorded in the activity log.

The MCP server is off unless `NB_MCP_ENABLED=true` is set on management; `NB_MCP_TOOLS` narrows the list of tools. Self-hosted installs with the embedded IdP need nothing else, since the broker's callback is added to the Dex clients on restart.

<img width="2560" height="1800" alt="mcp-consent" src="https://github.com/user-attachments/assets/4b4eaa60-fb5f-4add-808d-42e967241f17" />

📖 [MCP server](https://docs.netbird.io/manage/mcp-server) · [Sign in with OAuth](https://docs.netbird.io/manage/mcp-server#sign-in-with-o-auth) · [Self-hosted setup](https://docs.netbird.io/selfhosted/maintenance/mcp-server) · [#8335](https://github.com/netbirdio/netbird/pull/8335), [#8334](https://github.com/netbirdio/netbird/pull/8334), [#8502](https://github.com/netbirdio/netbird/pull/8502), [#8499](https://github.com/netbirdio/netbird/pull/8499), [#8498](https://github.com/netbirdio/netbird/pull/8498)

### 🛡️ CrowdSec AppSec for the dashboard and API during self-hosted install

The self-hosted installers now offer CrowdSec AppSec once Traefik, the NetBird Proxy and CrowdSec are enabled. When enabled, it protects the dashboard, `/api` and `/oauth2`; relay, gRPC, WebSocket relay paths and services exposed through the NetBird Proxy are not inspected. The option is off by default, because AppSec enforces immediately, blocks protected paths when CrowdSec is unavailable, and can lock out an administrator after repeated rule matches.

📖 [Protect the dashboard with CrowdSec AppSec](https://docs.netbird.io/selfhosted/maintenance/crowdsec-dashboard) · [#8341](https://github.com/netbirdio/netbird/pull/8341)

### 🔑 Keep secrets out of the combined server's `config.yaml`

The combined `netbird-server` now reads the relay auth secret from `NB_AUTH_SECRET` and the store encryption key from `NB_STORE_ENCRYPTION_KEY`. A non-empty variable overrides the value in `config.yaml`, so the file can live in git without secrets. Values are never logged; the startup log names only the variable. `configure.sh` also no longer clears an existing `DataStoreEncryptionKey`.

On self-hosted Enterprise, keep the store encryption key in `config.yaml` and do not set `NB_STORE_ENCRYPTION_KEY`: the Enterprise server requires the key in the file, and its integrations read only that copy. Earlier versions ignore both variables, so do not remove a secret from `config.yaml` before the server runs v0.83.0.

📖 [Keeping secrets out of the config file](https://docs.netbird.io/selfhosted/maintenance/configuration-files#keeping-secrets-out-of-the-config-file) · [Combined server variables](https://docs.netbird.io/selfhosted/environment-variables#combined-server-variables) · [#8405](https://github.com/netbirdio/netbird/pull/8405), [#5258](https://github.com/netbirdio/netbird/pull/5258)

## What's New

### 🔒 Security and Access Control

* **Scoped group resource references to the owning account**. [#8474](https://github.com/netbirdio/netbird/pull/8474)

### 🖥️ Client Improvements

* **Sent the relay hostname as `Host` when dialing a relay by IP**. [#8359](https://github.com/netbirdio/netbird/pull/8359)
* **Let Signal register behind proxies that hold headers until the first message**. [#8380](https://github.com/netbirdio/netbird/pull/8380)
* **Started the update manager when management forces an update**. [#8232](https://github.com/netbirdio/netbird/pull/8232)

### 📱 Mobile

* **Preserved iOS credentials when login checks cannot reach management**. [#8202](https://github.com/netbirdio/netbird/pull/8202)
* **Reported network loss on iOS through the existing recovery callback**. [#7716](https://github.com/netbirdio/netbird/pull/7716)
* **Stopped waiting for the utun interface to be removed on iOS engine stop**. [#8471](https://github.com/netbirdio/netbird/pull/8471)
* **Added `RefreshWireGuardStats` to the iOS SDK**. [#8509](https://github.com/netbirdio/netbird/pull/8509)

### ⚙️ Management Improvements

* **Skipped writes and network map updates for objects that did not change**. [#8404](https://github.com/netbirdio/netbird/pull/8404)
* **Batched peer disconnects and made reconnects lock-free**. [#8373](https://github.com/netbirdio/netbird/pull/8373)
* **Moved peer connection state into separate tables**; a database migration runs on the first start after the upgrade. [#8426](https://github.com/netbirdio/netbird/pull/8426)
* **Added a configurable statement and transaction timeout for the PostgreSQL store**, set with `NB_STORE_STATEMENT_TIMEOUT` and `NB_STORE_TRANSACTION_TIMEOUT`. [#8381](https://github.com/netbirdio/netbird/pull/8381)
* **Added a hard deadline to the update debouncer**, so pending network map updates are always sent. [#8437](https://github.com/netbirdio/netbird/pull/8437)
* **Used the peer's last-seen time to detect stale connected statuses**. [#7268](https://github.com/netbirdio/netbird/pull/7268)
* **Stopped peer expiration jobs when expiration is disabled for the account**. [#8521](https://github.com/netbirdio/netbird/pull/8521)
* **Limited the store calls made for resource policies**. [#8522](https://github.com/netbirdio/netbird/pull/8522)

### 🔀 Reverse Proxy

* **Attributed agent network errors in the access logs**: a failed request now records whether a policy, a global limit, the provider, the proxy or authentication caused it. [#8460](https://github.com/netbirdio/netbird/pull/8460)
* **Kept gateway refusals out of the agent network log and named NetBird in deny messages**. [#8575](https://github.com/netbirdio/netbird/pull/8575)
* **Reported LLM token usage as metrics**. [#8481](https://github.com/netbirdio/netbird/pull/8481)

### 🏗️ Infrastructure and CI

* **Fixed the Enterprise migration when the secret comes from the environment**. [#8436](https://github.com/netbirdio/netbird/pull/8436)
* **Moved the combined server's config onto a shared Viper-based loader**; behavior is unchanged, values still come from the file and defaults. [#8108](https://github.com/netbirdio/netbird/pull/8108)
* **Built releases from the pushed tag instead of GoReleaser's guess**. [#8427](https://github.com/netbirdio/netbird/pull/8427)
* **Made the skip-latest tests independent of their environment**. [#8468](https://github.com/netbirdio/netbird/pull/8468)
* **Stopped dispatching the mobile client submodule bumps on release tags**. [#8347](https://github.com/netbirdio/netbird/pull/8347)

### 📝 Misc

* **Expanded the repository's contribution guidance**. [#7946](https://github.com/netbirdio/netbird/pull/7946)

## Dashboard (v2.97.0)

* **Reshaped the sidebar**: Control Center, Peers, Users and Groups at the top; Networking and Agent Network as collapsible sections; Settings, Tenants, Integrations and Audit Events under Account. Users and Service Users are now tabs, and Agent Network policies are renamed **Agent Policies**. [dashboard#892](https://github.com/netbirdio/dashboard/pull/892)
* **Showed what failed an agent network request** in the REASON column of the access logs (needs management v0.83.0). [dashboard#884](https://github.com/netbirdio/dashboard/pull/884)
* **Added a sync interval setting to EDR integrations**. [dashboard#865](https://github.com/netbirdio/dashboard/pull/865)
* **Allowed single-label domains** for network resources and peer DNS domains. [dashboard#670](https://github.com/netbirdio/dashboard/pull/670), [dashboard#883](https://github.com/netbirdio/dashboard/pull/883)
* **Sized table columns by content** and shared spare width evenly. [dashboard#886](https://github.com/netbirdio/dashboard/pull/886)
* **Kept the default WASM client from moving backwards**. [dashboard#885](https://github.com/netbirdio/dashboard/pull/885)
* **Pointed the IdP sync callout to the docs** and added Discord under Help. [dashboard#881](https://github.com/netbirdio/dashboard/pull/881), [dashboard#890](https://github.com/netbirdio/dashboard/pull/890)
* **Fixed the white outline flashing on modals** after the Tailwind v4 upgrade. [dashboard#882](https://github.com/netbirdio/dashboard/pull/882)

## New Contributors
* @mvanhorn made their first contribution in [#8405](https://github.com/netbirdio/netbird/pull/8405)
* @playground-tjeerd made their first contribution in [#8359](https://github.com/netbirdio/netbird/pull/8359)
* @CodingTil made their first contribution in [#5258](https://github.com/netbirdio/netbird/pull/5258)
* @DominicVonk made their first contribution in [#7716](https://github.com/netbirdio/netbird/pull/7716)
* @lkitllab made their first contribution in [#8471](https://github.com/netbirdio/netbird/pull/8471)

**Full Changelog**: [v0.82.0...v0.83.0](https://github.com/netbirdio/netbird/compare/v0.82.0...v0.83.0)
