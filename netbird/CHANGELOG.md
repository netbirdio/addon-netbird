# Changelog

## [v0.65.0] - 2026-02-13

### Changed
- Updated to NetBird v0.65.0

### Upstream Release Notes
# Release Notes for v0.65.0

## What's New

### 🔀 Reverse Proxy

NetBird now includes a built-in reverse proxy in the management server, enabling proxied access to backend services through your NetBird network. Allowing you to expose your services to the public with the option to secure them with SSO, PINs, or passwords.

No VPN client required for end users. Just point a custom domain at your NetBird server, configure the proxy in the dashboard, and your internal services are securely accessible from any browser. Think of it as a self-hosted alternative to Cloudflare Tunnels, but without the MITM and fully under your control.

**Key features:**

* **Custom domains** - Map your own domains to internal services and let NetBird handle TLS and routing via CNAME verification
* **Built-in authentication** - Protect exposed services with SSO (via your configured IdP), PIN codes, passwords, or magic links directly from the dashboard
* **Multiple targets** - Route traffic to one or more backend peers or resources with optional path-based routing
* **Access logs** - Monitor who's accessing your proxied services with built-in logging
* **Proxy settings** - Fine-tune behavior with options like host header passthrough and redirect rewriting

#### Add a Service

Expose any internal service by selecting a subdomain and adding one or more backend targets. Each target points to a peer or resource on your network.

<img width="400" height="400" alt="image" src="https://github.com/user-attachments/assets/3bd8b332-a49d-4f3e-ba67-b95615b362ca" />


<img width="400" height="400" alt="image" src="https://github.com/user-attachments/assets/0ca780b9-5a10-406b-ae72-4a84586f8dfd" />

#### Custom Domains

Bring your own domain by adding a CNAME record pointing to your NetBird proxy cluster. NetBird handles TLS certificate provisioning automatically.

<img width="400" height="400" alt="image" src="https://github.com/user-attachments/assets/9d162966-7815-4bd2-a2dd-1993e13ae891" />

#### Authentication

Secure your exposed services with multiple authentication methods. Enable one or combine several for layered protection.

<img width="400" height="400" alt="image" src="https://github.com/user-attachments/assets/fd385a87-f7fd-41c0-8998-fe00113d8e57" />


#### Settings

Fine-tune proxy behavior with options like passing the original Host header to your backend or rewriting redirect URLs to use the public domain.

<img width="400" height="400" alt="image" src="https://github.com/user-attachments/assets/1b19c92c-1f8b-40ce-a8ce-a30f21ec5d0d" />

Learn more:
* [Reverse Proxy Overview](https://docs.netbird.io/manage/reverse-proxy)
* [Custom Domains](https://docs.netbird.io/manage/reverse-proxy/custom-domains)
* [Authentication](https://docs.netbird.io/manage/reverse-proxy/authentication)

> NetBird cloud support is coming soon, with hosted reverse proxy nodes.


### 🏗️ Self-Hosted Improvements

* Added **combined NetBird server** binary for simplified self-hosted deployments, reducing the number of containers needed to run NetBird.
  [#5232](https://github.com/netbirdio/netbird/pull/5232)

### 🔒 Management Improvements

* **Enforced access control on accessible peers**, ensuring proper authorization checks when querying the accessible peers endpoint.
  [#5301](https://github.com/netbirdio/netbird/pull/5301)
* Added **cloud API spec to the public OpenAPI** definition with REST client support.
  [#5222](https://github.com/netbirdio/netbird/pull/5222)

### 🖥️ Client Improvements

* Added **early message buffer for the relay client**, preventing message loss during connection establishment.
  [#5282](https://github.com/netbirdio/netbird/pull/5282)
* **Refactored relay connection container** for improved reliability and code maintainability.
  [#5271](https://github.com/netbirdio/netbird/pull/5271)

## What's Changed

* [misc] Update sign pipeline version by @mlsmaycon in [#5296](https://github.com/netbirdio/netbird/pull/5296)
* [self-hosted] add netbird server by @braginini in [#5232](https://github.com/netbirdio/netbird/pull/5232)
* [management] Enforce access control on accessible peers by @bcmmbaga in [#5301](https://github.com/netbirdio/netbird/pull/5301)
* [misc] Add cloud api spec to public open api with rest client by @bcmmbaga in [#5222](https://github.com/netbirdio/netbird/pull/5222)
* [client] Add early message buffer for relay client by @pappz in [#5282](https://github.com/netbirdio/netbird/pull/5282)
* [client] Refactor/relay conn container by @pappz in [#5271](https://github.com/netbirdio/netbird/pull/5271)
* [management, reverse proxy] Add reverse proxy feature by @pascal-fischer in [#5291](https://github.com/netbirdio/netbird/pull/5291)

**Full Changelog**: [v0.64.6...v0.65.0](https://github.com/netbirdio/netbird/compare/v0.64.6...v0.65.0)

## [v0.64.6] - 2026-02-12

### Changed
- Updated to NetBird v0.64.6

### Upstream Release Notes
## Release Notes for v0.64.6

### What's New

🚨 Security Fix
Security: Fixed account impersonation validation in management API

Fixed a vulnerability in the management server's authentication middleware where the ?account= query parameter could be used to impersonate arbitrary accounts without proper validation when getting a list of accessible peers. It requires the attacker to have prior knowledge of the target accounts' and peer IDs. 

The fix adds explicit validation via IsValidChildAccount() before allowing account switching. Account impersonation is now only permitted when the target account is confirmed as a legitimate child account of the
  requesting user's parent account.

Affected component: Management server HTTP middleware (auth_middleware.go) and `/api/peers/<peer_id>/accessible-peers` endpoint

Severity: High — an authenticated user could potentially access or act on behalf of accounts they should not have access to by passing an arbitrary account parameter and fetching the list of accessible peers.

**Recommendation**: All self-hosted deployments should upgrade to this version.

#### Client Improvements
- Added **missing BSD flags** to the debug bundle.  
  [#5254](https://github.com/netbirdio/netbird/pull/5254)
- Cached the result of `wgInterface.ToInterface()` using `sync.Once` for better performance.  
  [#5256](https://github.com/netbirdio/netbird/pull/5256)
- Fixed **nil pointer panic** in the ICE agent during sleep/wake cycles.  
  [#5261](https://github.com/netbirdio/netbird/pull/5261)
- Always log **DNS forwarder responses** for improved troubleshooting.  
  [#5262](https://github.com/netbirdio/netbird/pull/5262)
- Fixed **netstack detection** and added a **WireGuard port option**.  
  [#5251](https://github.com/netbirdio/netbird/pull/5251)
- Corrected wrong URL logging for `DefaultAdminURL`.  
  [#5252](https://github.com/netbirdio/netbird/pull/5252)
- Added **timing measurements** to `handleSync` for better observability.  
  [#5228](https://github.com/netbirdio/netbird/pull/5228)
- Fixed duplicate firewall rules in **USP filter**.  
  [#5269](https://github.com/netbirdio/netbird/pull/5269)
- Added environment variable to **skip DNS probing** when needed.  
  [#5270](https://github.com/netbirdio/netbird/pull/5270)
- Fixed race condition and ensured correct **message ordering in Relay**.  
  [#5265](https://github.com/netbirdio/netbird/pull/5265)
- Ensured login is checked in **foreground mode** when required.  
  [#5295](https://github.com/netbirdio/netbird/pull/5295)
- Fixed multiple **panics in device and engine code**.  
  [#5287](https://github.com/netbirdio/netbird/pull/5287)
- Cleaned up **stale nftables entries without handle**.  
  [#5272](https://github.com/netbirdio/netbird/pull/5272)

#### Management Improvements
- Fixed incorrectly setting **disconnected status** for connected peers.  
  [#5247](https://github.com/netbirdio/netbird/pull/5247)
- Added **gRPC debounce for message types** to reduce noise.  
  [#5239](https://github.com/netbirdio/netbird/pull/5239)
- Added validation of **stream start time** for connecting peers.  
  [#5267](https://github.com/netbirdio/netbird/pull/5267)
- Fixed `ischild` check logic.  
  [#5279](https://github.com/netbirdio/netbird/pull/5279)

### New Contributors
- @eyJhb made their first contribution in [#5252](https://github.com/netbirdio/netbird/pull/5252)

**Full Changelog**: [v0.64.5...v0.64.6](https://github.com/netbirdio/netbird/compare/v0.64.5...v0.64.6)

## [v0.64.5] - 2026-02-03

### Changed
- Updated to NetBird v0.64.5

### Upstream Release Notes
## What's Changed
* Add selfhosting video by @braginini in https://github.com/netbirdio/netbird/pull/5235
* [management] adding account id validation to accessible peers handler by @pascal-fischer in https://github.com/netbirdio/netbird/pull/5246


**Full Changelog**: https://github.com/netbirdio/netbird/compare/v0.64.4...v0.64.5

## [v0.64.4] - 2026-02-01

### Changed
- Updated to NetBird v0.64.4

### Upstream Release Notes
## What's Changed
* [client] Add macOS default resolvers as fallback by @lixmal in https://github.com/netbirdio/netbird/pull/5201
* [client] Add block inbound option to the embed client by @lixmal in https://github.com/netbirdio/netbird/pull/5215
* [management] Disable local users for a smooth single-idp mode by @braginini in https://github.com/netbirdio/netbird/pull/5226
* [management] disable sync lim by @crn4 in https://github.com/netbirdio/netbird/pull/5233
* [management] run cancelPeerRoutines in goroutine in sync by @crn4 in https://github.com/netbirdio/netbird/pull/5234


**Full Changelog**: https://github.com/netbirdio/netbird/compare/v0.64.3...v0.64.4

## [v0.64.3] - 2026-01-29

### Changed
- Updated to NetBird v0.64.3

### Upstream Release Notes
## What's Changed
* [client] Remove redundant square bracket trimming in USP endpoint parsing by @pappz in https://github.com/netbirdio/netbird/pull/5197
* [client] Refactor/optimise raw socket headers by @pappz in https://github.com/netbirdio/netbird/pull/5174
* [management] fix ephemeral peers being not removed by @crn4 in https://github.com/netbirdio/netbird/pull/5203
* [management] fix skip of ephemeral peers on deletion by @crn4 in https://github.com/netbirdio/netbird/pull/5206
* [client] Stop NetBird on firewall init failure by @lixmal in https://github.com/netbirdio/netbird/pull/5208
* [management] Streamline domain validation by @lixmal in https://github.com/netbirdio/netbird/pull/5211
* [client] Fix WG watcher missing initial handshake by @pappz in https://github.com/netbirdio/netbird/pull/5213


**Full Changelog**: https://github.com/netbirdio/netbird/compare/v0.64.2...v0.64.3

## [v0.64.2] - 2026-01-27

### Changed
- Updated to NetBird v0.64.2

### Upstream Release Notes
## Release Notes for v0.64.2

### What's New
#### Client Improvements
- Consolidated **authentication logic** to improve maintainability and consistency.  
  [#5010](https://github.com/netbirdio/netbird/pull/5010)
- Added **IPv6 support** to the UDP WireGuard proxy.  
  [#5169](https://github.com/netbirdio/netbird/pull/5169)
- Fixed a **flaky JWT SSH test** to improve CI stability.  
  [#5181](https://github.com/netbirdio/netbird/pull/5181)
- Updated **Fyne UI** and added **retry handling** to the exit menu.  
  [#5187](https://github.com/netbirdio/netbird/pull/5187)
- Prevented **eBPF traffic** from being tracked in conntrack.  
  [#5166](https://github.com/netbirdio/netbird/pull/5166)
- Added support for **non-PTY, no-command interactive SSH sessions**.  
  [#5093](https://github.com/netbirdio/netbird/pull/5093)

#### Management & Identity
- Fixed **validator warning messages** to improve clarity.  
  [#5168](https://github.com/netbirdio/netbird/pull/5168)
- Improved **peer deletion error handling**.  
  [#5188](https://github.com/netbirdio/netbird/pull/5188)
- Included **default groups claim** in the CLI audience.  
  [#5186](https://github.com/netbirdio/netbird/pull/5186)
- Added **user invite link** support for the embedded IdP.  
  [#5157](https://github.com/netbirdio/netbird/pull/5157)


**Full Changelog**: [v0.64.1...v0.64.2](https://github.com/netbirdio/netbird/compare/v0.64.1...v0.64.2)

## [v0.60.2] - 2025-11-17

### Changed
- Updated to NetBird v0.60.2
- **BREAKING**: Removed support for armhf, armv7, and i386 architectures
- Only aarch64 and amd64 architectures are now supported

## [v0.54.2] - 2025-11-17

### Changed
- Updated to NetBird v0.54.2
- Improved security by masking sensitive setup key in logs
- Enhanced error handling in service startup script
- Better documentation for DNS resolver workaround

### Fixed
- Fixed inconsistent addon slug in documentation
- Fixed broken repository links
- Updated maintenance year to 2025
- Corrected default management URL across documentation

### Added
- AppArmor security profile
- Healthcheck configuration for monitoring addon status
- Improved file migration logic with better error handling
- Binary existence check before execution

### Security
- Removed sensitive credential logging
- Added AppArmor profile for enhanced security

## [Unreleased]

### Notes
- Based on hassio-addons base image 19.0.0
- Supports aarch64 and amd64 architectures only
- Requires privileged capabilities for VPN functionality
