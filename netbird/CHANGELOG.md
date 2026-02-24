# Changelog

## [v0.66.0] - 2026-02-24

### Changed
- Updated to NetBird v0.66.0

### Upstream Release Notes
## Release Notes for v0.66.0

## 🚀 New Feature: `netbird expose`

We're excited to introduce **`netbird expose`** --- a simple and secure way to expose your local services through the NetBird reverse proxy.

### ⚡ Expose Local Services with Protection

Expose a local HTTP server:

``` bash
netbird expose 8080
```

This instantly publishes your local service via NetBird's reverse proxy.

You can enhance the exposure with built-in protection and customization:

### 🔐 With PIN protection (6 digits)

``` bash
netbird expose 3000 --with-pin 123456
```

### 🔑 With password protection and name prefix

``` bash
netbird expose 8080 --with-password my-secret --with-name-prefix my-app
```

### 👥 Restrict by SSO user groups

``` bash
netbird expose 8080 --with-user-groups engineering,devops
```

### 🌐 Use a custom domain (pre-configured in your account)

``` bash
netbird expose 8080 --with-custom-domain app.example.com
```

### Supported Flags

-   `--with-pin string` --- Protect the exposed service with a 6-digit
    PIN\
-   `--with-password string` --- Add password protection\
-   `--with-user-groups strings` --- Restrict access to specific user
    groups\
-   `--with-custom-domain string` --- Specify a custom domain\
-   `--with-name-prefix string` --- Prefix the generated service name\
-   `--protocol string` --- Protocol to use (`http` or `https`, default
    `http`)

> :warning: NetBird Cloud support is coming soon with hosted proxy nodes. :warning:

Learn more at: https://docs.netbird.io/manage/reverse-proxy/expose-from-cli

Or watch the video below:
[![Watch the video](https://img.youtube.com/vi/dBPPPloqwcU/0.jpg)](https://youtu.be/dBPPPloqwcU)

#### Client Improvements
- Stopped upstream retry loop immediately on **context cancellation**.  
  [#5403](https://github.com/netbirdio/netbird/pull/5403)
- Fixed **busy-loop in network monitor routing socket** on macOS/BSD.  
  [#5424](https://github.com/netbirdio/netbird/pull/5424)
- Fixed **missed sleep/wakeup events on macOS**.  
  [#5418](https://github.com/netbirdio/netbird/pull/5418)
- Removed **connection semaphore** to simplify connection handling.  
  [#5419](https://github.com/netbirdio/netbird/pull/5419)
- Skipped **UAPI listener in netstack mode**.  
  [#5397](https://github.com/netbirdio/netbird/pull/5397)
- Simplified **DNS logging** by removing domain list from log output.  
  [#5396](https://github.com/netbirdio/netbird/pull/5396)
- Excluded **Flow domain from caching** to prevent TLS failures.  
  [#5433](https://github.com/netbirdio/netbird/pull/5433)
- Added **non-default socket file discovery** support.  
  [#5425](https://github.com/netbirdio/netbird/pull/5425)

#### Client Service Expose
- Introduced **client service expose feature** across client and management.  
  [#5411](https://github.com/netbirdio/netbird/pull/5411)
- Refactored expose feature by moving **business logic from gRPC to manager layer**.  
  [#5435](https://github.com/netbirdio/netbird/pull/5435)

#### Proxy Improvements
- Added **access log cleanup**.  
  [#5376](https://github.com/netbirdio/netbird/pull/5376)
- Implemented **access log sorting**.  
  [#5378](https://github.com/netbirdio/netbird/pull/5378)
- Sent **proxy updates on account deletion**.  
  [#5375](https://github.com/netbirdio/netbird/pull/5375)
- Added **pre-shared key (PSK) support** to proxy.  
  [#5377](https://github.com/netbirdio/netbird/pull/5377)

#### Management Improvements
- Refactored **network map component assembly**.  
  [#5193](https://github.com/netbirdio/netbird/pull/5193)
- Added **custom domain counts and service metrics** to self-hosted metrics.  
  [#5414](https://github.com/netbirdio/netbird/pull/5414)

#### Self-Hosted Enhancements
- Added support for **activity store engine** in the combined server.  
  [#5406](https://github.com/netbirdio/netbird/pull/5406)
- Added **Embedded IdP metrics** for improved observability.  
  [#5407](https://github.com/netbirdio/netbird/pull/5407)

**Full Changelog**: [v0.65.3...v0.66.0](https://github.com/netbirdio/netbird/compare/v0.65.3...v0.66.0)
